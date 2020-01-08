---
title: 为有状态服务开发单元测试
description: 了解 Azure Service Fabric 中有状态服务的单元测试，以及在开发期间要牢记的特殊注意事项。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639830"
---
# <a name="create-unit-tests-for-stateful-services"></a>为有状态服务创建单元测试
对 Service Fabric 有状态服务进行单元测试可发现传统应用程序或特定于域的单元测试不一定会捕获的常见错误。 在为有状态服务开发单元测试时，应牢记一些特殊注意事项。

1. 每个副本均执行应用程序代码，但在不同的上下文下。 如果服务使用三个副本，则在不同上下文/角色下并行在三个节点上执行服务代码。
2. 有状态服务中存储的状态应在所有副本之间保持一致。 状态管理器和可靠集合将提供这种现成的一致性。 但是，内存中状态将需要由应用程序代码进行管理。
3. 在群集上运行时，每个副本都会在某个时刻更改角色。 如果托管主要副本的节点变得不可用或重载，则次要副本将成为主要副本。 这是 Service Fabric 的自然行为，因此服务必须计划最终在不同的角色下执行。

本文假设已阅读[对 Service Fabric 中的有状态服务进行单元测试](service-fabric-concepts-unit-testing.md)。

## <a name="the-servicefabricmocks-library"></a>ServiceFabric.Mocks 库
从版本 3.3.0 开始，[ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) 提供了一个 API，用于模拟副本和状态管理的业务流程。 这将在示例中使用。

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
[GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric 不由 Microsoft 拥有或维护。但是，这是 Microsoft 建议的用于单元测试有状态服务的库。*

## <a name="set-up-the-mock-orchestration-and-state"></a>设置模拟业务流程和状态
作为测试的排列部分的一部分，将创建模拟副本集和状态管理器。 然后，副本集将自己为每个副本创建已测试服务的实例。 它还将自己执行生命周期事件，例如 `OnChangeRole` 和 `RunAsync`。 模拟状态管理器将确保对状态管理器执行的任何操作均按照实际状态管理器那样运行和保持。

1. 创建将实例化正在测试的服务的服务工厂委托。 这应该与通常在 Service Fabric 服务或执行组件的 `Program.cs` 中找到的服务工厂回叫类似或相同。 这应遵循以下签名：
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. 创建 `MockReliableStateManager` 类的实例。 这将模拟与状态管理器的所有交互。
3. 创建 `MockStatefulServiceReplicaSet<TStatefulService>` 的实例，其中 `TStatefulService` 是要测试服务的类型。 这将需要在步骤 #1 中创建的委托和在 #2 中实例化的状态管理器
4. 将副本添加到副本集。 指定角色（如 Primary、ActiveSecondary、IdleSecondary）和副本的 ID
   > 保留副本 ID！ 这些将有可能在单元测试的行为和断言部分中使用。

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>执行服务请求
可以使用便捷属性和查找在特定副本上执行服务请求。
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>执行服务移动
模拟副本集公开了多种便捷方法来触发不同类型的服务移动。
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>汇总
以下测试演示如何设置三个节点副本集，并验证在角色更改后数据是否可从辅助项中获得。 这可能会引发的典型问题是，不运行 `CommitAsync` 的情况下，在 `InsertAsync` 期间添加的数据是保存到内存中还是保存到可靠集合中的某个内容。 在任一情况下，辅助项都将与主要项不同步。 这将导致服务移动后的响应不一致。

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>后续步骤
了解如何测试[服务间通信](service-fabric-testability-scenarios-service-communication.md)和[使用受控的混沌模拟故障](service-fabric-controlled-chaos.md)。
