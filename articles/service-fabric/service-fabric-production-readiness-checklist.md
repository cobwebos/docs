---
title: Azure Service Fabric 生产就绪情况核对清单 | Microsoft Docs
description: 遵循最佳做法，让 Service Fabric 应用程序和群集做好生产准备。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo
ms.openlocfilehash: e94280f9df1d4ac59856a73f6f6c2b7f7a0b9cc0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107485"
---
# <a name="production-readiness-checklist"></a>生产就绪情况核对清单

应用程序和群集是否准备好接收生产流量？ 运行和测试应用程序及群集并不一定意味着它们已准备好投入生产。 通过查看以下核对清单，使应用程序和群集保持平稳运行。 强烈建议检查所有这些项目。 显然，可以选择为特定明细项目（例如，自己的诊断框架）使用替代解决方案。


## <a name="pre-requisites-for-production"></a>生产的先决条件
1. [Azure Service Fabric 安全性最佳做法](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices)是： 
1. 使用 X.509 证书
1. 配置安全性策略
1. 配置适用于 Azure Service Fabric 的 SSL
1. 将 Azure Service Fabric 与网络隔离和安全功能结合使用
1. 出于安全考虑，设置 Azure Key Vault
1. Microsoft.Network/loadBalancers 将用户分配到角色
1. 如果使用执行组件编程模型，则实现 Reliable Actors 安全配置
1. 对于具有超过 20 个核心或 10 个节点的群集，请为系统服务创建专用的主节点类型。 添加[放置约束](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)，保留系统服务的主节点类型。
1. 对主节点类型使用 D2v2 或更高版本的 SKU。 建议选择至少有 50 GB 硬盘容量的 SKU。
1. 生产群集必须是[安全的](service-fabric-cluster-security.md)。 有关设置安全群集的示例，请参阅此[群集模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)。 使用证书的通用名称，避免使用自签名证书。
1. 添加[容器和服务的资源约束](service-fabric-resource-governance.md)，以便它们消耗的节点资源不超过 75%。 
1. 理解并设置[持续性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)。 对于运行有状态工作负载的节点类型，建议使用银级或更高的持续性级别。 主节点类型应将持续性级别设置为银级或更高级别。
1. 理解并选取节点类型的[可靠性级别](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)。 建议使用银级或更高级别的可靠性。
1. 对工作负载进行负载和缩放测试，确定群集的[容量需求](service-fabric-cluster-capacity.md)。 
1. 服务和应用程序受到监视，并且系统会生成并存储应用程序日志，并会发送警报。 有关示例，请参阅[添加到 Service Fabric 应用程序日志记录](service-fabric-how-to-diagnostics-log.md)并[使用 Azure Monitor 日志监视容器](service-fabric-diagnostics-oms-containers.md)。
1. 使用警报监视群集 (例如，对于[Azure Monitor 日志](service-fabric-diagnostics-event-analysis-oms.md))。 
1. 使用警报监视的基础的虚拟机规模集基础结构 (例如，对于[Azure Monitor 日志](service-fabric-diagnostics-oms-agent.md)。
1. 该群集始终具有[主要和辅助证书](service-fabric-cluster-security-update-certs-azure.md)（因此不会将你拒之门外）。
1. 维护用于开发、暂存和生产的独立集群。 
1. 首先会在开发和暂存群集中测试[应用程序升级](service-fabric-application-upgrade.md)和[群集升级](service-fabric-tutorial-upgrade-cluster.md)。 
1. 关闭生产集群中的自动升级，并对开发和暂存群集启用自动升级（根据需要回滚）。 
1. 为服务建立恢复点目标 (RPO)，设置[灾难恢复流程](service-fabric-disaster-recovery.md)并进行测试。
1. 手动或以编程方式计划群集的[缩放](service-fabric-cluster-scaling.md)。
1. 计划群集节点的[修补](service-fabric-patch-orchestration-application.md)。 
1. 建立一个 CI/CD 管道，以便不断测试最新的更改。 例如，使用 [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) 或 [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. 使用[故障分析服务](service-fabric-testability-overview.md)测试负载下的开发和暂存集群，并引入受控[混沌测试](service-fabric-controlled-chaos.md)。 
1. 计划应用程序的[缩放](service-fabric-concepts-scalability.md)。 


如果使用 Service Fabric Reliable Services 或 Reliable Actors 编程模型，则需检查以下项目：
1. 在本地开发期间升级应用程序，检查服务代码是否履行了 `RunAsync` 方法中的取消令牌，并关闭了自定义通信侦听器。
1. 使用可靠集合时，避免出现[常见错误](service-fabric-work-with-reliable-collections.md)。
1. 运行负载测试时监视 .NET CLR 内存性能计数器，并检查是否存在高垃圾回收率或堆增长是否失控。
1. 维护 [Reliable Services 和 Reliable Actors](service-fabric-reliable-services-backup-restore.md) 的脱机备份，并测试恢复过程。
1. 理想情况下，主 NodeType 虚拟机实例计数应该等于群集可靠性层的最小值；适于超过层最小值的条件包括：在垂直缩放主 NodeTypes 虚拟机规模集 SKU 时临时超过。

## <a name="optional-best-practices"></a>可选的最佳做法

虽然上述列表是投入生产的先决条件，但也应考虑以下各项：
1. 插入 [Service Fabric 运行状况模型](service-fabric-health-introduction.md)以扩展内置运行状况评估和报告。
1. 部署一个监视应用程序并报告[负载](service-fabric-cluster-resource-manager-metrics.md)以实现[资源均衡](service-fabric-cluster-resource-manager-balancing.md)的自定义监视程序。 


## <a name="next-steps"></a>后续步骤
* [部署 Service Fabric Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [部署 Service Fabric Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* 了解 Service Fabric [应用程序生命周期](service-fabric-application-lifecycle.md)。
