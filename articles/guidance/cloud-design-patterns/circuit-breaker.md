---
title: "断路器模式 | Azure | Microsoft Docs"
description: "连接到远程服务或资源时处理故障，此类故障所需修复时间不定。"
categories:
- resiliency
keywords: "设计模式"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: 5630ea3eb805170a20f48e9f605c12bca3f07355

---

# <a name="circuit-breaker"></a>断路器

连接到远程服务或资源时处理故障，此类故障所需恢复时间不定。 这可以提高应用程序的稳定性和复原能力。 

## <a name="context-and-problem"></a>上下文和问题

在分布式环境中，对远程资源和服务的调用可能会由于临时性故障（如网络连接缓慢、超时、资源过载或资源暂时不可用）而失败。 这些故障通常会在短时间内自行更正，而且，应该会准备一个可靠的云应用程序，通过[重试模式](retry.md)这样的策略来处理这些它们。

但是，也可能遇到由于意外事件而导致的故障，且需要更长的时间来进行修复。 这些故障按严重程度从部分连接丢失到服务彻底故障都有可能。 这类情况下，让应用程序持续重试不可能成功的操作是毫无意义的。相反，应用程序应该快速认识到操作已失败，并相应地处理此故障。

此外，如果服务十分繁忙，那么系统一个部分的故障可能会导致连锁故障。 例如，调用服务的操作可以被配置为实现超时，并且在服务未能在此期间内响应时返回失败消息。 但是，此策略可能导致同一操作的许多并发请求受到阻止，直至超时期间到期。 这些阻止的请求可能占用了关键的系统资源，如内存、线程、数据库连接等。 因此，这些资源可能被耗尽，从而导致需要使用相同资源的系统其他不相关部分出现故障。 在这些情况下，更有益的做法便是让操作立即失败并只在服务可能成功时才尝试调用服务。 请注意，设置较短的超时可能有助于解决此问题，但为避免操作在大多数时间内失败，超时不应太短（即使对服务的请求最终会成功）。

## <a name="solution"></a>解决方案

断路器模式可以防止应用程序重复尝试执行很可能失败的操作。 在确定故障的持续时间很长时允许它继续，而不等待故障修复或浪费 CPU 周期。 断路器模式还可让应用程序检测故障是否已经解决。 如果问题已被修复，应用程序便可以尝试调用操作。

> 断路器模式的目的与重试模式不同。 重试模式在预期操作将成功的情况下让应用程序重试操作。 断路器模式则防止应用程序执行很可能失败的操作。 应用程序可以使用重试模式通过断路器调用操作，来组合这两种模式。 但重试逻辑应该对断路器返回的任何异常保持敏感，并且在断路器指示故障为非临时性的情况下放弃重试尝试。

针对可能失败的操作，断路器充当其代理。 代理应监视最近发生的失败次数，并使用此信息来决定是允许操作继续进行，还是立即返回异常。

通过模仿电力断路器的功能，可将代理作为具有以下状态的状态机来实现：

- **关闭**：将来自应用程序的请求路由到操作。 代理维护最近失败次数的计数，如果对操作的调用不成功，代理将递增此计数。 如果在给定时间段内最近失败次数超过指定的阈值，则代理将置于**打开**状态。 此时，代理会启动超时计时器，并且当此计时器过期时，代理将置于**半开**状态。
    
    > 超时计时器的目的是给系统一段时间来解决导致失败的问题，然后允许应用程序再次尝试执行操作。
    
- **打开**：来自应用程序的请求立即失败，并向应用程序返回异常。

- **半开**：允许数量有限的来自应用程序的请求通过并调用操作。 如果这些请求成功，则假定先前导致失败的问题已被修复，并且断路器将切换到**关闭**状态（失败计数器重置）。 如果有任何请求失败，则断路器将假定故障仍然存在，因此它会恢复到**打开**状态，并重新启动超时计时器，再给系统一段时间来从故障中恢复。

    > **半开**状态对于防止恢复服务突然被大量请求淹没很有用。 在服务恢复的同时，它或许能够支持数量有限的请求，直至恢复完成；但当恢复正在进行时，大量的工作可能导致服务超时或再次失败。
    
![断路器状态](images/cache-aside-diagram.png)

在图中，**关闭**状态所使用的失败计数器是基于时间的。 它会定期自动重置。 这有助于防止断路器在遇到偶然失败时进入**打开**状态。 仅当在指定间隔期间内发生指定数量的失败时，才会达到将断路器跳闸到**打开**状态的故障阈值。 **半开**状态使用的计数器记录成功调用操作的次数。 在指定数量的连续操作调用成功后，断路器将恢复到**关闭**状态。 如果任何调用失败，断路器会立即进入**打开**状态，成功计数器将在下次进入**半开**状态时重置。

> 系统恢复是从外部进行的，可能的方法是通过还原或重新启动失败的组件，或修复网络连接。
    
断路器模式在系统从故障中恢复时提供稳定性，并将对性能的影响降至最低。 它可以通过快速拒绝很可能失败的操作的请求（而非等待操作超时或永不返回）来帮助维持系统的响应时间。 如果断路器在每次改变状态时引发事件，则该信息可以用于监视由断路器保护的系统部分的运行状况，或者当断路器跳闸到**打开**状态时，对管理员发出警报。

该模式是可自定义的，并且可以根据可能的故障类型进行调整。 例如，可以向断路器应用可递增的超时计时器。 最开始可以将断路器置于**打开**状态几秒钟，如果故障未得到解决，则将超时增加到几分钟，以此类推。 在某些情况下，与其通过**打开**状态返回失败并引发异常，返回对应用程序来说有意义的默认值实则更加有用。

## <a name="issues-and-considerations"></a>问题和注意事项

在决定如何实现此模式时，应考虑以下几点：

**异常处理**。 通过断路器调用操作的应用程序必须做好在操作不可用的情况下处理异常的准备。 异常的处理方式特定于应用程序。 例如，应用程序可暂时降低其功能，调用替代操作来尝试执行相同的任务或获取相同的数据，或向用户报告异常并让他们稍后再试。

**异常的类型**。 请求可能由于多种原因而失败，其中一些原因可能指示比其他原因更严重的故障类型。 例如，由于远程服务已崩溃且需要数分钟才恢复，或服务临时超载而导致超时，请求可能会失败。 断路器可检查发生的异常的类型，并根据这些异常的性质来调整其策略。 例如，由于服务完全不可用，相比失败次数，有可能需要更多数量的超时异常才能使断路器跳闸至**打开**状态。

**日志记录**。 断路器应记录所有失败的请求（可能还有成功的请求），以使管理员能够监视操作的运行状况。 

**可恢复性**。 应将断路器配置为匹配受其保护的操作的可能恢复模式。 例如，如果断路器长期处于**打开**状态，即使故障原因已得到解决，它也可能引发异常。 类似地，如果断路器从**打开**状态切换到**半开**状态的速度太快，则断路器可能会波动，并减少应用程序的响应时间。

**测试失败的操作**。 在**打开**状态中，断路器可定期执行对远程服务或资源的 ping 操作来决定其是否变得再次可用，而无需使用计时器来判断何时应切换至**半开**状态。 此种 ping 操作可以尝试调用之前失败的操作，或使用由远程服务提供的专门用于测试服务运行状况的特殊操作，如[运行状况终结点监视模式](health-endpoint-monitoring.md)中所述。

**手动替代**。 在失败操作的恢复时间可变性极大的系统中，最好提供手动重置选项以便管理员能够关闭断路器（并重置失败计数器）。 类似地，如果受断路器保护的操作暂时不可用，管理员可以强制断路器进入**打开**状态（并重新启动超时计时器）。

**并发**。 应用程序的大量并发实例可以访问同一断路器。 该实现不应阻止并发请求，或对操作的每个调用添加过多的开销。

**资源区分**。 在对一种类型的资源使用单个断路器时，请注意是否存在多个基础独立提供程序。 例如，在包含多个分片的数据存储中，有可能能够完全访问一个分片，而另一个分片却遇到临时性问题。 如果将这些情况下的错误相应合并，则应用程序可能会尝试访问某些分片（即使失败的可能性很高），而对其他分片的访问则可能会被阻止（即使可能成功）。

**加速的断路**。 有时，失败响应可能包含足够的信息，以便断路器立刻跳闸，并在最短的时间内保持跳闸状态。 例如，来自超载的共享资源的错误响应可能指示不建议立即重试，且应用程序应改为在几分钟后再次尝试。

> [!NOTE]
> 如果服务限制客户端，则服务可返回 HTTP 429（请求过多）；如果服务当前不可用，则返回 HTTP 503（服务不可用）。 响应可包括附加信息，如延迟的预期持续时间。

**重播失败的请求**。 在**打开**状态下，断路器还可以将每个请求的详细信息记录到日志中，并安排这些请求在远程资源或服务可用时重播，而不是简单地快速失败。

**外部服务的不恰当超时**。 对于配置有很长超时时间的外部服务中失败的操作，断路器可能无法完全保护应用程序不产生此类操作。 如果超时过长，则在断路器指示操作已失败之前，可能会在较长时间内阻止运行断路器的线程。 此时，许多其他应用程序实例也可能尝试通过断路器调用服务，并在它们全部失败之前占用大量的线程。

## <a name="when-to-use-this-pattern"></a>何时使用此模式

使用此模式：
- 防止应用程序尝试调用远程服务或访问共享资源（如果此操作很可能失败）。

不推荐使用此模式：
- 用于处理对应用程序中的本地私有资源的访问，例如内存中数据结构。 在此环境中，使用断路器会增加系统开销。 
- 作为处理应用程序的业务逻辑中的异常的替代。

## <a name="example"></a>示例

在 Web 应用程序中，多个页面使用从外部服务检索的数据进行填充。 如果系统实现最小缓存，对这些页面的大多数点击都将导致对服务的往返。 从 Web 应用程序到服务的连接可以配置超时期间（通常为 60 秒），如果服务在此时间段内未响应，则每个网页中的逻辑将假定服务不可用并引发异常。

但是，如果服务失败并且系统非常繁忙，则可能会在异常发生之前强制用户等待最长 60 秒。 最终，可能会耗尽内存、连接和线程等资源，从而阻止其他用户连接到系统（即使他们并不是访问从服务检索数据的页面）。

通过添加更多 Web 服务器和实现负载平衡来扩展系统可能会延迟资源耗尽的时间，但无法解决问题，因为用户请求仍然无响应，所有 Web 服务器最终仍会耗尽资源。

包装连接到服务并在断路器中检索数据的逻辑，有助于解决该问题，并更从容地处理服务失败。 用户请求仍然会失败，但速度更快，且不会阻止资源。

`CircuitBreaker` 类在实现 `ICircuitBreakerStateStore` 接口的对象中维持有关断路器的状态信息，如以下代码中所示。

```csharp
interface ICircuitBreakerStateStore
{
  CircuitBreakerStateEnum State { get; }

  Exception LastException { get; }

  DateTime LastStateChangedDateUtc { get; }

  void Trip(Exception ex);

  void Reset();

  void HalfOpen();

  bool IsClosed { get; }
}
```

`State` 属性指示断路器的当前状态，并且由 `CircuitBreakerStateEnum` 枚举定义为**打开**、**半开**或**关闭**。 如果断路器处于关闭状态，则 `IsClosed` 属性应为 true，如果处于打开或半开状态，则为 false。 `Trip` 方法将断路器的状态切换为打开状态，并记录导致状态更改的异常，以及异常发生的日期和时间。 `LastException` 和 `LastStateChangedDateUtc` 属性返回此信息。 `Reset` 方法关闭断路器，`HalfOpen` 方法将断路器设置为半开。

示例中的 `InMemoryCircuitBreakerStateStore` 类包含 `ICircuitBreakerStateStore` 接口的实现。 `CircuitBreaker` 类创建此类的实例以维持断路器的状态。

`CircuitBreaker` 类中的 `ExecuteAction` 方法包装指定为 `Action` 委托的操作。 如果断路器处于关闭状态，`ExecuteAction` 将调用 `Action` 委托。 如果操作失败，异常处理程序会调用 `TrackException`，它将断路器状态设置为打开。 以下代码示例突出显示此流。

```csharp
public class CircuitBreaker
{
  private readonly ICircuitBreakerStateStore stateStore =
    CircuitBreakerStateStoreFactory.GetCircuitBreakerStateStore();

  private readonly object halfOpenSyncObject = new object ();
  ...
  public bool IsClosed { get { return stateStore.IsClosed; } }

  public bool IsOpen { get { return !IsClosed; } }

  public void ExecuteAction(Action action)
  {
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open.
      ... (see code sample below for details)
    }

    // The circuit breaker is Closed, execute the action.
    try
    {
      action();
    }
    catch (Exception ex)
    {
      // If an exception still occurs here, simply 
      // retrip the breaker immediately.
      this.TrackException(ex);

      // Throw the exception so that the caller can tell
      // the type of exception that was thrown.
      throw;
    }
  }

  private void TrackException(Exception ex)
  {
    // For simplicity in this example, open the circuit breaker on the first exception.
    // In reality this would be more complex. A certain type of exception, such as one
    // that indicates a service is offline, might trip the circuit breaker immediately. 
    // Alternatively it might count exceptions locally or across multiple instances and
    // use this value over time, or the exception/success ratio based on the exception
    // types, to open the circuit breaker.
    this.stateStore.Trip(ex);
  }
}
```

下面的示例显示了当断路器未关闭时所执行的代码（上例中已省略）。 它首先检查断路器已经打开的时间是否长于 `CircuitBreaker` 类中的本地 `OpenToHalfOpenWaitTime` 字段所指定的时间。 如果是这种情况，则 `ExecuteAction` 方法将断路器设置为半开，然后尝试执行 `Action` 委托所指定的操作。

如果操作成功，断路器将重置为关闭状态。 如果操作失败，则跳回打开状态，并且更新异常发生的时间，从而使断路器再等待一段时间，然后才再次尝试执行操作。

如果断路器仅在短时间内（小于 `OpenToHalfOpenWaitTime` 值）处于打开状态，则 `ExecuteAction` 方法只会引发 `CircuitBreakerOpenException` 异常并返回导致断路器转换到打开状态的错误。

此外，它使用锁来防止断路器在半开时尝试对操作执行并发调用。 将处理调用操作的并发尝试（犹如断路器处于打开状态一样），该尝试将失败，其异常会在稍后进行说明。

```csharp
    ...
    if (IsOpen)
    {
      // The circuit breaker is Open. Check if the Open timeout has expired.
      // If it has, set the state to HalfOpen. Another approach might be to
      // check for the HalfOpen state that had be set by some other operation.
      if (stateStore.LastStateChangedDateUtc + OpenToHalfOpenWaitTime < DateTime.UtcNow)
      {
        // The Open timeout has expired. Allow one operation to execute. Note that, in
        // this example, the circuit breaker is set to HalfOpen after being 
        // in the Open state for some period of time. An alternative would be to set 
        // this using some other approach such as a timer, test method, manually, and 
        // so on, and check the state here to determine how to handle execution
        // of the action. 
        // Limit the number of threads to be executed when the breaker is HalfOpen.
        // An alternative would be to use a more complex approach to determine which
        // threads or how many are allowed to execute, or to execute a simple test 
        // method instead.
        bool lockTaken = false;
        try
        {
          Monitor.TryEnter(halfOpenSyncObject, ref lockTaken)
          if (lockTaken)
          {
            // Set the circuit breaker state to HalfOpen.
            stateStore.HalfOpen();

            // Attempt the operation.
            action();

            // If this action succeeds, reset the state and allow other operations.
            // In reality, instead of immediately returning to the Open state, a counter
            // here would record the number of successful operations and return the
            // circuit breaker to the Open state only after a specified number succeed.
            this.stateStore.Reset();
            return;
          }
          catch (Exception ex)
          {
            // If there's still an exception, trip the breaker again immediately.
            this.stateStore.Trip(ex);

            // Throw the exception so that the caller knows which exception occurred.
            throw;
          }
          finally
          {
            if (lockTaken)
            {
              Monitor.Exit(halfOpenSyncObject);
            }
          }
        }
      }
      // The Open timeout hasn't yet expired. Throw a CircuitBreakerOpen exception to
      // inform the caller that the call was not actually attempted, 
      // and return the most recent exception received.
      throw new CircuitBreakerOpenException(stateStore.LastException);
    }
    ...
```

为了使用 `CircuitBreaker` 对象来保护操作，应用程序会创建 `CircuitBreaker` 类的实例并调用 `ExecuteAction` 方法，指定要作为参数执行的操作。 如果操作由于断路器打开而失败，应用程序应做好捕获 `CircuitBreakerOpenException` 异常的准备。 以下代码展示一个示例：

```csharp
var breaker = new CircuitBreaker();

try
{
  breaker.ExecuteAction(() =>
  {
    // Operation protected by the circuit breaker.
    ...
  });
}
catch (CircuitBreakerOpenException ex)
{
  // Perform some different action when the breaker is open.
  // Last exception details are in the inner exception.
  ...
}
catch (Exception ex)
{
  ...
}
```

## <a name="related-patterns-and-guidance"></a>相关模式和指南

实现此模式时，以下模式也可能有用：

- [重试模式](retry.md)。 描述当应用程序尝试连接到服务或网络资源（通过明显地重试先前失败的操作）时，它如何处理预期的临时故障。 

- [运行状况终结点监视模式](health-endpoint-monitoring.md)。 通过向由服务公开的终结点发送请求，断路器可能能够测试服务的运行状况。 服务应返回指示其状态的信息。



<!--HONumber=Nov16_HO3-->


