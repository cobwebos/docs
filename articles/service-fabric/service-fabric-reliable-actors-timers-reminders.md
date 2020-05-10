---
title: Reliable Actors 计时器和提醒
description: 介绍 Service Fabric Reliable Actors 的计时器和提醒，包括有关何时使用每项的指导。
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 67dc5d9706c2176b2fe70d2540be00d0af79fd80
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996359"
---
# <a name="actor-timers-and-reminders"></a>执行组件计时器和提醒
执行组件可通过注册计时器或提醒来计划自身的定期工作。 本文演示如何使用计时器和提醒，并说明它们之间的差异。

## <a name="actor-timers"></a>执行组件计时器
执行组件计时器围绕 .NET 或 Java 计时器提供一个简单包装器，确保回叫方法采用 Actors 运行时提供的基于轮次的并发保证。

执行组件可以对其基类使用 `RegisterTimer`(C#) 或 `registerTimer`(Java) 和 `UnregisterTimer`(C#) 或 `unregisterTimer`(Java) 方法以注册和注销其计时器。 下面的示例演示了如何使用计时器 API。 这些 API 非常类似于 .NET 计时器或 Java 计时器。 在此示例中，当计时器到期时，Actors 运行时会调用 `MoveObject`(C#) 或 `moveObject`(Java) 方法。 可保证该方法遵循基于轮次的并发。 这意味着，任何其他执行组件方法或计时器/提醒回调将一直进行，直到此回调完成执行为止。

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
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
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

计时器的下一个周期在回调完成执行之后启动。 这意味着计时器会在回调执行期间停止，在回调完成时启动。

执行组件运行时会保存在回叫完成时，对执行组件的状态管理器所做的更改。 如果在保存状态时发生错误，则会停用该执行组件对象并激活一个新实例。

与[提醒](#actor-reminders)不同，计时器不能更新。 如果`RegisterTimer`再次调用，将注册新的计时器。

如果在垃圾回收过程中停用了执行组件，所有计时器会停止。 此后不会调用任何计时器回调。 此外，执行组件运行时不保留有关在停用之前运行的计时器的任何信息。 这主要归功于执行组件可以注册在将来重新激活时需要的任何计时器。 有关详细信息，请参阅[执行组件垃圾回收](service-fabric-reliable-actors-lifecycle.md)的部分。

## <a name="actor-reminders"></a>执行组件提醒
提醒是一种机制，用于在指定时间对执行组件触发持久回调。 其功能类似于计时器。 但与计时器不同的是，提醒会在所有情况下触发，直到执行组件显式注销提醒或显式删除执行组件。 具体而言，提醒会在执行组件停用和故障转移间触发，因为 执行组件运行时会使用执行组件状态提供程序保存有关执行组件提醒的信息。 与计时器不同的是，可以通过使用相同的*reminderName*再次调用注册`RegisterReminderAsync`方法（）来更新现有提醒。

> [!NOTE]
> 提醒的可靠性依赖于执行组件状态提供程序所提供的状态可靠性保证。 这意味着，如果将状态持久性设置为 "*无*"，则在故障转移后将不会触发提醒。

为了注册提醒，执行组件会调用基类[`RegisterReminderAsync`](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync?view=azure-dotnet#remarks)上提供的方法，如以下示例中所示：

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

在本例中，`"Pay cell phone bill"` 是提醒名称。 这是执行组件用于唯一标识提醒的字符串。 `BitConverter.GetBytes(amountInDollars)`(C#) 是与提醒相关联的上下文。 它会作为提醒回叫的参数（即 `IRemindable.ReceiveReminderAsync`(C#) 或 `Remindable.receiveReminderAsync`(Java)）传递回执行组件。

使用提醒的执行组件必须实现 `IRemindable` 接口，如以下示例所示。

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

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
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

触发提醒时，Reliable Actors 运行时会对执行组件调用 `ReceiveReminderAsync`(C#) 或 `receiveReminderAsync`(Java) 方法。 一个执行组件可以注册多个提醒，而 `ReceiveReminderAsync`(C#) 或 `receiveReminderAsync`(Java) 方法会在触发其中任一提醒时调用。 执行组件可以使用传入给 `ReceiveReminderAsync`(C#) 或 `receiveReminderAsync`(Java) 方法的提醒名称来找出触发的提醒。

Actors 运行时会在 `ReceiveReminderAsync`(C#) 或 `receiveReminderAsync`(Java) 调用完成时保存执行组件的状态。 如果在保存状态时发生错误，则会停用该执行组件对象并激活一个新实例。

为了注销提醒，执行组件会调用 `UnregisterReminderAsync`(C#) 或 `unregisterReminderAsync`(Java) 方法，如以下示例所示。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

如上所示， `UnregisterReminderAsync`(C#) 或 `unregisterReminderAsync`(Java) 方法接受 `IActorReminder`(C#) 或 `ActorReminder`(Java) 接口。 执行组件基类支持 `GetReminder`(C#) 或 `getReminder`(Java) 方法，该方法可用于通过传入提醒名称来检索 `IActorReminder`(C#) 或 `ActorReminder`(Java) 接口。 这十分方便，因为执行组件无需保存从 `RegisterReminder`(C#) 或 `registerReminder`(Java) 方法调用返回的 `IActorReminder`(C#) 或 `ActorReminder`(Java) 接口。

## <a name="next-steps"></a>后续步骤
了解 Reliable Actor 事件和可重入性：
* [执行组件事件](service-fabric-reliable-actors-events.md)
* [执行组件可重入性](service-fabric-reliable-actors-reentrancy.md)
