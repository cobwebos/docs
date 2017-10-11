---
title: "Reliable Actors 计时器和提醒 |Microsoft 文档"
description: "Service Fabric Reliable Actors 计时器和提醒简介。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 00c48716-569e-4a64-bd6c-25234c85ff4f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 06b026ce06e0f16a77ac238de0af2263f272933c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="actor-timers-and-reminders"></a>Actor 计时器和提醒
通过注册计时器或提醒，参与者可以计划定期自身的工作。 本文演示如何使用计时器和提醒，并说明它们之间的差异。

## <a name="actor-timers"></a>Actor 计时器
Actor 计时器提供简单包装的.NET 或 Java 计时器，以确保回调方法采用基于轮次的并发保证 Actors 运行时提供。

参与者可以使用`RegisterTimer`(C#) 或`registerTimer`(Java) 和`UnregisterTimer`(C#) 或`unregisterTimer`(Java) 方法对其基类来注册和注销其计时器。 下面的示例演示如何使用计时器 Api。 中的 Api 是非常类似于.NET 计时器或 Java 计时器。 在此示例中，当计时器已到期，Actors 运行时将调用`MoveObject`(C#) 或`moveObject`(Java) 方法。 保证该方法采用基于轮次的并发。 这意味着，任何其他参与者方法或计时器/提醒回调将在进行此回调完成执行之前。

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

在回调完成执行后，将启动计时器的下一个周期。 这意味着时该回调正在执行，并且在回调完成时启动停止计时器。

Actors 运行时保存在回调完成时对 actor 状态管理器所做的更改。 如果错误出现在保存状态，将停用该参与者对象并将激活的新实例。

当参与者作为垃圾回收的一部分停用时，会停止所有计时器。 在此之后不调用任何计时器回调。 此外，Actors 运行时不保留有关在停用之前运行的计时器的任何信息。 它是归功于参与者可以注册在将来重新激活时所需的任何计时器。 有关详细信息，请参阅部分[actor 垃圾回收](service-fabric-reliable-actors-lifecycle.md)。

## <a name="actor-reminders"></a>参与者提醒
提醒是一种机制来触发持久回调参与者在指定的时间。 它们的功能是类似于计时器。 但与计时器不同都触发提醒在所有情况下直到参与者显式注销或参与者显式删除。 具体而言，提醒会触发在参与者停和故障转移，因为 Actors 运行时保留有关参与者提醒的信息。

若要注册提醒，参与者会调用`RegisterReminderAsync`方法提供基本类，如下面的示例中所示：

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
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

在此示例中，`"Pay cell phone bill"`是提醒名称。 这是参与者用于唯一标识提醒的字符串。 `BitConverter.GetBytes(amountInDollars)`(C#) 是与提醒关联的上下文。 它将传递回参与者作为提醒回调的自变量即`IRemindable.ReceiveReminderAsync`(C#) 或`Remindable.receiveReminderAsync`(Java)。

使用提醒的参与者必须实现`IRemindable`接口，如下面的示例中所示。

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

当触发提醒时，将调用 Reliable Actors 运行时`ReceiveReminderAsync`(C#) 或`receiveReminderAsync`参与者 (Java) 方法。 参与者可以注册多个提醒和`ReceiveReminderAsync`(C#) 或`receiveReminderAsync`(Java) 方法调用时触发其中任何提醒。 参与者可以使用传入到提醒名称`ReceiveReminderAsync`(C#) 或`receiveReminderAsync`(Java) 方法，以找出触发的提醒。

Actors 运行时保存参与者的状态时`ReceiveReminderAsync`(C#) 或`receiveReminderAsync`(Java) 调用完成。 如果错误出现在保存状态，将停用该参与者对象并将激活的新实例。

若要注销提醒，参与者会调用`UnregisterReminderAsync`(C#) 或`unregisterReminderAsync`(Java) 方法，如下面的示例中所示。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

如上所示， `UnregisterReminderAsync`(C#) 或`unregisterReminderAsync`(Java) 方法接受`IActorReminder`(C#) 或`ActorReminder`(Java) 接口。 参与者基类支持`GetReminder`(C#) 或`getReminder`(Java) 方法，可以用来检索`IActorReminder`(C#) 或`ActorReminder`通过传入提醒名称 (Java) 接口。 此方法很方便的因为参与者无需保留`IActorReminder`(C#) 或`ActorReminder`(Java) 接口从返回`RegisterReminder`(C#) 或`registerReminder`(Java) 方法调用。

## <a name="next-steps"></a>后续步骤
了解有关 Reliable Actor 事件和可重入性：
* [Actor 事件](service-fabric-reliable-actors-events.md)
* [Actor 重新进入](service-fabric-reliable-actors-reentrancy.md)
