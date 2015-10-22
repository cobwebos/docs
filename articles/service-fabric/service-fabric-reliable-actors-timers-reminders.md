<properties
   pageTitle="Reliable Actors 计时器和提醒"
   description="Service Fabric Reliable Actors 的计时器和提醒简介。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="08/05/2015"
   wacn.date=""/>


# 参与者计时器
参与者计时器围绕 .NET 计时器提供简单包装，以便回调方法采用 Actors 运行时提供的基于轮次的并发保证。

参与者可以对其基类使用 `RegisterTimer` 和 `UnregisterTimer` 方法以注册和注销其计时器。下面的示例演示了如何使用计时器 API。这些 API 非常类似于 .NET 计时器。在下面的示例中，当计时器到期时，会由 Actors 运行时调用 `MoveObject` 方法，并保证采用基于轮次的并发，这意味着任何其他参与者方法或计时器/提醒回调在此回调完成执行之前会继续进行。

```csharp
class VisualObjectActor : Actor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    public override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // callback method
            state,                          // state to pass to callback method
            TimeSpan.FromMilliseconds(15),  // amount of time to delay before callback is invoked
            TimeSpan.FromMilliseconds(15)); // time interval between invocation of the callback method

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return TaskDone.Done;
    }
}
```

计时器的下一个周期在回调完成执行之后启动。这意味着计时器会在回调执行期间停止，在回调完成时启动。

如果参与者是有状态参与者（类似于上面的示例），则当回调完成时，参与者Actors 运行时会保存参与者。如果在保存状态时发生错误，则会停用该参与者对象并激活一个新实例。不修改参与者状态的回调方法可以通过在计时器回调中指定 Readonly 属性，来注册为只读计时器回调，如有关 [readonly 方法](/documentation/articles/service-fabric-reliable-actors-introduction#readonly-methods)的部分中所述。

当参与者作为垃圾回收的一部分停用，并且在此之后未调用任何计时器回调时，会停止所有计时器。此外，Actors 运行时不保留有关在停用之前运行的计时器的任何信息。这主要归功于参与者可以注册在将来重新激活时需要的任何计时器。有关详细信息，请参阅有关[参与者垃圾回收](/documentation/articles/service-fabric-reliable-actors-lifecycle)的部分。

## 参与者提醒
提醒是一种机制，用于在指定时间对参与者触发持久回调。其功能类似于计时器，不过与计时器不同，在参与者显式注销提醒之前，会在所有情况下都触发提醒。具体而言，提醒会在参与者停用和故障转移间触发，因为 Actors 运行时会保持有关参与者提醒的信息。

仅有状态参与者支持提醒。无状态参与者无法使用提醒。参与者状态提供程序负责存储有关已由参与者注册的提醒信息。

为了注册提醒，参与者会调用基类上提供的 `RegisterReminder` 方法，如下面的示例中所示。

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

在上面的示例中，`"Pay cell phone bill"` 是提醒名称，这是参与者用于唯一标识提醒的字符串。`BitConverter.GetBytes(amountInDollars)` 是与提醒关联的上下文。它会作为提醒回调的参数（即 `IRemindable.ReceiveReminderAsync`）传递回参与者。

使用提醒的参与者必须实现 `IRemindable` 接口，如下面的示例中所示。

```csharp
public class ToDoListActor : Actor<ToDoList>, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

当触发提醒时，Fabric Actors 运行时会对参与者调用 `ReceiveReminderAsync` 方法。参与者可以注册多个提醒，`ReceiveReminderAsync` 方法可在触发其中任何提醒的任何时候进行调用。参与者可以使用传入给 `ReceiveReminderAsync` 方法的提醒名称来找出触发的提醒。

Actors 运行时会在 `ReceiveReminderAsync` 调用完成时保存参与者状态。如果在保存状态时发生错误，则会停用该参与者对象并激活一个新实例。要指定在提醒回调完成时无需保存状态，可以在调用 `RegisterReminder` 方法来创建提醒时在 `attributes` 参数中设置 `ActorReminderAttributes.ReadOnly` 标志。

要注销提醒，应调用 `UnregisterReminder` 方法，如下面的示例中所示。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

如上所示，`UnregisterReminder` 方法接受 `IActorReminder` 接口。参与者基类支持 `GetReminder` 方法，该方法可以用于通过传入提醒名称来检索 `IActorReminder` 接口。这十分方便，因为参与者无需保存从 `RegisterReminder` 方法调用返回的 `IActorReminder` 接口。

<!---HONumber=74-->