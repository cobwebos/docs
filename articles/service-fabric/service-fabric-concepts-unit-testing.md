---
title: 对 Azure Service Fabric 中的有状态服务进行单元测试 | Microsoft Docs
description: 了解对 Service Fabric 有状态服务进行单元测试的概念和实践。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881447"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>对 Service Fabric 中的有状态服务进行单元测试

本文介绍对 Service Fabric 有状态服务进行单元测试的概念和实践。 由于应用程序代码在多个不同的上下文下主动运行，因此 Service Fabric 中的单元测试值得考虑。 本文介绍了用于确保应用程序代码涵盖在其可以运行的每个不同上下文下的实践。

## <a name="unit-testing-and-mocking"></a>单元测试和模拟
本文上下文中的单元测试是自动化测试，可以在测试运行程序（如 MSTest 或 NUnit）的上下文中执行。 本文中的单元测试不会对远程资源（如数据库或 RESTFul API）执行操作。 应模拟这些远程资源。 在本文的上下文中进行模拟将虚设、记录和控制远程资源的返回值。

### <a name="service-fabric-considerations"></a>Service Fabric 注意事项
对 Service Fabric 有状态服务进行单元测试有几个注意事项。 首先，服务代码在多个节点上但在不同的角色下执行。 单元测试应评估每个角色下的代码以实现完全覆盖。 不同的角色将为“主要”、“活动次要”、“空闲辅助”和“未知”。 由于 Service Fabric 认为此角色为无效或 null 服务，因此“无”角色通常不需要任何特殊覆盖。 其次，每个节点将在任何给定时间更改其角色。 若要实现完全覆盖，应该在发生角色更改的情况下测试代码执行路径。

## <a name="why-unit-test-stateful-services"></a>为何使用单元测试有状态服务？ 
对有状态服务进行单元测试可以帮助发现造成的某些常见错误，而传统应用程序或特定于域的单元测试不一定会捕获这些错误。 例如，如果有状态服务有任何内存中状态，此类型的测试可以验证此内存中状态是否在每个副本间保持同步。 此类型的测试还可以验证有状态服务是否适当地响应 Service Fabric 业务流程传入的取消令牌。 触发取消后，服务应停止任何长时间运行和/或异步操作。  

## <a name="common-practices"></a>常见做法

以下部分提供关于对有状态服务进行单元测试的最常见做法的建议。 它还建议模拟层应该要与 Service Fabric 业务流程和状态管理紧密地保持一致。 模拟库确实存在提供此功能的库。 [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) 自 3.3.0 开始或更高版本就是这样一个库，提供了建议的模拟功能并遵循下面概述的做法。

### <a name="arrangement"></a>排列

#### <a name="use-multiple-service-instances"></a>使用多个服务实例
单元测试应执行有状态服务的多个实例。 这模拟在群集上实际发生的情况，其中 Service Fabric 会在不同节点中预配运行服务的多个副本。 但是，其中每个实例都将在不同的上下文下执行。 当运行测试时，每个实例都应预先准备群集上预期的角色配置。 例如，如果预期服务的目标副本大小为 3，则 Service Fabric 会在不同的节点上预配三个副本。 其中一个为主要，另外两个为活动次要。

在大多数情况下，其中每个角色的服务执行路径都会略有不同。 例如，如果服务不应接受来自“活动次要”的请求，则服务可能会检查此情况，以返回一个信息性异常，指示尝试了次要的请求。 具有多个实例将允许对这种情况进行测试。

此外，具有多个实例允许测试切换每个实例的角色，以验证响应在角色更改的情况下是否仍一致。

#### <a name="mock-the-state-manager"></a>模拟状态管理器
状态管理器应视为远程资源，并因此进行模拟。 模拟状态管理器时，需要有一些基础内存存储，用于跟踪保存到状态管理器的内容，以便可以进行读取和验证。 实现此目的的一个简单方法是创建每个“可靠集合”类型的模拟实例。 在这些模拟中，使用与针对该集合执行的操作保持紧密一致的数据类型。 以下是每个可靠集合的一些建议数据类型

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>许多状态管理器实例，单个存储
如前所述，状态管理器和可靠集合应视为远程资源。 因此，这些资源应该并且将在单元测试中进行模拟。 但是，在运行有状态服务的多个实例时，要使每个模拟状态管理器在不同的有状态服务实例之间保持同步将是一项挑战。 当有状态服务在群集上运行时，Service Fabric 负责保持每个次要副本的状态管理器与主要副本一致。 因此，测试的行为应相同，以便它们可以模拟角色更改。

实现此同步的一个简单方法是，对存储写入每个“可靠集合”的数据的基础对象使用单一实例模式。 例如，如果有状态服务使用 `IReliableDictionary<string, string>`。 模拟状态管理器应返回 `IReliableDictionary<string, string>` 的模拟。 该模拟可能会使用 `ConcurrentDictionary<string, string>` 来跟踪写入的键/值对。 `ConcurrentDictionary<string, string>` 应为传递给服务的状态管理器的所有实例使用的单一实例。

#### <a name="keep-track-of-cancellation-tokens"></a>跟踪取消令牌
取消令牌是有状态服务的一个重要但却常被忽视的方面。 当 Service Fabric 启动有状态服务的主要副本时，会提供取消令牌。 此取消令牌用于在被删除或降级为其他角色时向服务发出信号。 有状态服务应停止任何长时间运行或异步操作，以便 Service Fabric 能够完成角色更改工作流。

运行单元测试时，应在测试执行期间保留提供给 RunAsync、ChangeRoleAsync、OpenAsync 和 CloseAsync 的任何取消令牌。 保留这些令牌将允许测试模拟服务关闭或降级，并验证服务是否适当地进行响应。

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>使用模拟的远程资源进行端到端测试
单元测试应尽可能多地执行可以修改有状态服务状态的应用程序代码。 建议测试在本质上更加端到端。 存在的唯一模拟是记录、模拟和/或验证远程资源交互。 这包括与状态管理器和可靠集合的交互。 以下代码片段是用于演示端到端测试的一个测试的 gherkin 的示例：

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

此测试断言当一个副本提升为主要副本时，在该副本上捕获的数据可用于次要副本。 假设可靠集合是员工数据的后备存储，则此测试可能会引发的一个潜在故障是，应用程序代码是否未在 `CommitAsync` 事务上执行以保存新员工。 在这种情况下，获取员工的第二个请求将不会返回第一个请求添加的员工。

### <a name="acting"></a>操作
#### <a name="mimic-service-fabric-replica-orchestration"></a>模拟 Service Fabric 副本业务流程
在管理多个服务实例时，测试应以与 Service Fabric 业务流程相同的方式初始化和拆除这些服务。 例如，在新的主要副本上创建服务时，Service Fabric 将调用 CreateServiceReplicaListener、OpenAsync、ChangeRoleAsync 和 RunAsync。 以下文章中介绍了生命周期事件：

- [有状态服务启动](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [有状态服务关闭](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [有状态服务主副本交换](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>运行副本角色更改
单元测试应以与 Service Fabric 业务流程相同的方式更改服务实例的角色。 以下文章中介绍了角色状态机：

[副本角色状态机](service-fabric-concepts-replica-lifecycle.md#replica-role)

模拟角色更改是测试中更重要的方面之一，可以发现副本的状态彼此不一致的问题。 由于在静态或类级别实例变量中存储内存中状态，可能会出现不一致的副本状态。 这种情况的示例可能是取消令牌、枚举和配置对象/值。 这还将确保服务遵循在 RunAsync 期间提供的取消令牌，以允许发生角色更改。 模拟角色更改还可以发现未编写代码以允许多次调用 RunAsync 时可能会出现的问题。

#### <a name="cancel-cancellation-tokens"></a>取消取消令牌
应该存在单元测试，其中取消了提供给 RunAsync 的取消令牌。 这将允许测试验证服务是否正常关闭。 在此关闭期间，应停止任何长时间运行或异步操作。 服务中可能存在的长时间运行进程的示例是侦听可靠队列的消息的进程。 这可能直接存在于 RunAsync 或后台线程中。 如果取消此取消令牌，则实现应包括退出操作的逻辑。

如果有状态服务使用仅应存在于主要节点上的任何缓存或内存中状态，则应在此时对其进行处置。 这是为了确保此状态在该节点稍后再次成为主要节点时保持一致。 取消测试将允许测试验证此状态是否正确处置。

#### <a name="execute-requests-against-multiple-replicas"></a>针对多个副本执行请求
断言测试应针对不同副本执行相同请求。 当与角色更改配对时，可以发现一致性问题。 示例测试可以执行以下步骤：
1. 针对当前主要节点执行写入请求
2. 针对当前主要节点执行返回在步骤 1 中写入的数据的读取请求
3. 将次要提升为主要。 这还应该将当前主要降级为次要
4. 针对新的次要节点执行步骤 2 中相同的读取请求。

在最后一个步骤中，测试可以断言返回的数据是一致的。 这可能发现的一个潜在问题是，服务返回的数据可能在内存中，但最终由可靠集合支持。 此内存中数据可能无法与可靠集合中存在的内容正确保持同步。

内存中数据通常用于创建可靠集合中存在的数据的辅助索引或聚合。

### <a name="asserting"></a>断言
#### <a name="ensure-responses-match-across-replicas"></a>确保响应在副本之间匹配
单元测试应断言，在转换为主要后，给定请求的响应在多个副本之间是一致的。 这可能会出现潜在的问题，即响应中提供的数据要么不受可靠集合的支持，要么保留在内存中而没有在副本之间同步数据的机制。 这将确保服务在 Service Fabric 重新平衡或故障转移到新的主要副本后，会返回一致的响应。

#### <a name="verify-service-respects-cancellation"></a>验证服务是否遵循取消
在取消令牌被取消时应终止的长时间运行或异步进程，应验证它们在取消后实际上已终止。 这将确保尽管副本更改角色，但在转换完成之前，不打算继续在非主要副本上运行的进程会停止。 还可以发现此类进程阻止 Service Fabric 完成角色更改或关闭请求的问题。

#### <a name="verify-which-replicas-should-serve-requests"></a>验证哪些副本应为请求提供服务
如果请求路由到非主要副本，则测试应断言预期的行为。 Service Fabric 确实能够让次要副本为请求提供服务。 但是，对可靠集合的写入只能从主要副本发生。 如果你的应用程序仅打算使用主要副本为请求提供服务，或者仅有一部分请求可以由次要副本进行处理，则测试应断言正值和负值这两种情况的预期行为。 请求的负值情况路由到不应处理请求的副本，而正值则相反。

## <a name="next-steps"></a>后续步骤
了解如何[对有状态服务进行单元测试](service-fabric-how-to-unit-test-stateful-services.md)。