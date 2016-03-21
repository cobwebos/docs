<properties
   pageTitle="Reliable Services 编程模型的高级用法 | Microsoft Azure"
   description="了解 Service Fabric 的 Reliable Service 编程模型的高级用法，以便在服务中提高灵活性。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.date="01/28/2016"
   wacn.date=""/>

# Reliable Services 编程模型的高级用法
Azure Service Fabric 可简化可靠的无状态服务和有状态服务的编写与管理。本指南讨论 Reliable Services 编程模型的高级用法，以便针对服务获得更多控制和灵活性。阅读本指南之前，你自己应熟悉 [Reliable Services 编程模型](/documentation/articles/service-fabric-reliable-services-introduction)。

## 无状态服务的基类
StatelessService 基类提供 RunAsync() 和 CreateServiceInstanceListeners()，这对于大多数无状态服务而言已够用。StatelessServiceBase 类是 StatelessService 的基础，公开了其他服务生命周期事件。如果需要更多控制或灵活性，则可以从 StatelessServiceBase 派生。有关详细信息，请参阅有关 [StatelessService](https://msdn.microsoft.com/zh-cn/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) 和 [StatelessServiceBase](https://msdn.microsoft.com/zh-cn/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) 的开发人员参考文档。

- `void OnInitialize(StatelessServiceInitializiationParameters)`
    OnInitialize 是 Service Fabric 调用的第一个方法。提供服务初始化信息，例如服务名称、分区 ID、实例 ID 和代码包信息。此处不应执行任何复杂处理。应在 OnOpenAsync 中完成冗长的初始化。

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
    要使用无状态服务实例时，可调用  OnOpenAsync。此时可以启动扩展的服务初始化任务。

- `Task OnCloseAsync(CancellationToken)`
    要正常关闭无状态服务实例时，可调用 OnCloseAsync。升级服务代码、由于负载平衡而移动服务实例或是检测到暂时性故障时，可能会出现这种情况。OnCloseAsync 可以用于安全地关闭任何资源、停止任何后台处理、完成外部状态保存或关闭现有连接。

- `void OnAbort()`
    要强制关闭无状态服务实例时，可调用 OnAbort。当在节点上检测到永久性故障时，或者当 Service Fabric 由于内部错误而无法可靠地管理服务实例的生命周期时，通常会调用此方法。

## 有状态服务的基类
StatefulService 基类对于大多数有状态服务应足够使用。与无状态服务类似，StatefulServiceBase 类是 StatefulService 的基础，公开了其他服务生命周期事件。此外，你还可以提供自定义可靠状态提供程序，并可以选择在辅助副本上支持通信侦听器。如果需要更多控制或灵活性，则可以从 StatefulServiceBase 派生。有关详细信息，请参阅有关 [StatefulService](https://msdn.microsoft.com/zh-cn/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) 和 [StatefulServiceBase](https://msdn.microsoft.com/zh-cn/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) 的开发人员参考文档。

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
    当有状态服务更改角色（例如更改为主副本或辅助副本）时，会调用 OnChangeRoleAsync。主副本将指定为写状态（允许创建和写入可靠集合）。辅助副本将指定为读取状态（只能从现有的可靠集合读取）。可以启动或更新后台任务以响应角色更改，例如在辅助副本上执行只读验证、报告生成或数据挖掘。

- `IStateProviderReplica CreateStateProviderReplica()`
    有状态服务应具有可靠状态提供程序。StatefulService 使用 ReliableStateManager 类，该类提供可靠集合（例如字典和队列）。你可以重写此方法，以便通过将 ReliableStateManagerConfiguration 传递给其构造函数来配置 ReliableStateManager 类。然后，可以提供自定义状态序列化程序，指定数据丢失时会发生什么情况，并配置复制器/状态提供程序。

StatefulServiceBase 还提供与 StatelessServiceBase 相同的四个生命周期事件，具有相同的语义和用例：

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 后续步骤
有关与 Service Fabric 相关的更高级主题，请参阅以下文章：

- [配置有状态 Reliable Services](/documentation/articles/service-fabric-reliable-services-configuration)

- [Service Fabric 运行状况简介](/documentation/articles/service-fabric-health-introduction.md)

- [使用系统运行状况报告进行故障排除](/documentation/articles/service-fabric-understand-and-troubleshoot-with-system-health-reports)

- [放置约束概述](/documentation/articles/service-fabric-placement-constraint)

<!---HONumber=Mooncake_0314_2016-->