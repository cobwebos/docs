---
title: Azure Service Fabric 执行组件中的可重入性
description: 介绍 Service Fabric Reliable Actors 的可重入性（该方法可以基于调用上下文在逻辑上避免阻止）。
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 100cf1f7bf8a0c903cfd61d93d2f923c32cabd11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260956"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors 可重入性
默认情况下，Reliable Actors 运行时允许基于逻辑调用上下文的可重入性。 因此执行组件在处于相同调用上下文链中时，可进行重入操作。 例如，如果执行组件 A 将消息发送给执行组件 B，而后者将消息发送给执行组件 C。在处理消息的过程中，如果执行组件 C 调用执行组件 A，这样的消息重入是允许的。 如果消息属于不同调用上下文，则会在执行组件 A 处受阻，直到处理完现有消息为止。

执行组件重入有两个相关选项，可在 `ActorReentrancyMode` 枚举中定义：

* `LogicalCallContext`（默认行为）
* `Disallowed` - 禁用重入

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
注册期间，可在 `ActorService`的设置中配置可重入性。 该设置适用于执行组件服务中创建的所有执行组件实例。

以下示例演示了将重入模式设置为 `ActorReentrancyMode.Disallowed` 的执行组件服务。 在这种情况下，如果执行组件向另一个执行组件发送可重入消息，则会引发类型为 `FabricException` 的异常。

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>后续步骤
* 在[执行组件 API 参考文档](/previous-versions/azure/dn971626(v=azure.100))中进一步了解可重入性
