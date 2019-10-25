---
title: 应用程序升级：升级参数 | Microsoft Docs
description: 介绍与升级 Service Fabric 应用程序相关的参数，包括要执行的运行状况检查，以及用于自动撤消升级的策略。
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a4170ac6-192e-44a8-b93d-7e39c92a347e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: atsenthi
ms.openlocfilehash: 6276f347f0cc0ecc03acc95282e83c243679957a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802429"
---
# <a name="application-upgrade-parameters"></a>应用程序升级参数
本文介绍 Azure Service Fabric 应用程序升级期间应用的各种参数。 应用程序升级参数控制升级期间应用的超时和运行状况检查，并指定在升级失败时必须应用的策略。 应用程序参数使用以下项应用于升级：
- PowerShell
- Visual Studio
- SFCTL
- [REST](https://docs.microsoft.com/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

应用程序升级通过三个可供用户选择的升级模式中的一个进行启动。 每个模式都有自己的应用程序参数集：
- 受监视
- 不受监视的自动
- 不受监视的手动

每部分中都介绍了适用的必需和可选参数，如下所述。

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio 和 PowerShell 参数

通过 PowerShell 进行的 Service Fabric 应用程序升级使用 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) 命令。 将 **Monitored**、**UnmonitoredAuto** 或 **UnmonitoredManual** 参数传递给 [Start-ServiceFabricApplicationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricapplicationupgrade) 即可选择升级模式。

Visual Studio Service Fabric 应用程序升级参数通过“Visual Studio 升级设置”对话框进行设置。 Visual Studio 升级模式通过“升级模式”下拉框从“Monitored”、“UnmonitoredAuto”或“UnmonitoredManual”中进行选择。 有关详细信息，请参阅[在 Visual Studio 中配置 Service Fabric 应用程序的升级](service-fabric-visualstudio-configure-upgrade.md)。

### <a name="required-parameters"></a>必需的参数
（PS=PowerShell，VS=Visual Studio）

| 参数 | 应用于 | 描述 |
| --- | --- | --- |
ApplicationName |PS| 正在升级的应用程序的名称。 示例：fabric:/VisualObjects、fabric:/ClusterMonitor。 |
ApplicationTypeVersion|PS|作为升级目标的应用程序类型版本。 |
FailureAction |PS、VS|允许的值为 **Rollback**、**Manual** 和 **Invalid**。 *Monitored* 升级遇到违反监视策略或运行状况策略的情况时需执行的补救操作。 <br>**Rollback** 指定将升级自动回退到升级前的版本。 <br>**Manual** 指示将升级切换到 *UnmonitoredManual* 升级模式。 <br>**Invalid** 指示故障操作无效。|
受监视 |PS|指示升级模式为“受监视”。 在 cmdlet 完成某个升级域的升级以后，如果该升级域和群集的运行状况符合定义的运行状况策略，则 Service Fabric 会升级下一个升级域。 如果升级域或群集不满足运行状况策略，则升级失败，Service Fabric 会回退升级域的升级，或者会按指定策略回复到手动模式。 这是建议用于生产环境的应用程序升级模式。 |
UpgradeMode | VS | 允许的值为 **Monitored**（默认）、**UnmonitoredAuto** 或 **UnmonitoredManual**。 有关详细信息，请查看本文中每个模式的 PowerShell 参数。 |
UnmonitoredAuto | PS | 指示升级模式为“不受监视的自动”。 Service Fabric 在升级某个升级域以后，就会直接升级下一个升级域，而不管应用程序运行状况如何。 不建议将此模式用于生产环境，它只适用于应用程序开发阶段。 |
UnmonitoredManual | PS | 指示升级模式为“不受监视的手动”。 Service Fabric 在升级某个升级域以后，会等待你使用 *Resume-ServiceFabricApplicationUpgrade* cmdlet 升级下一个升级域。 |

### <a name="optional-parameters"></a>可选参数

运行状况评估参数为可选参数。 如果在启动升级时未指定运行状况评估条件，则 Service Fabric 将使用应用程序实例的 ApplicationManifest.xml 中指定的应用程序运行状况策略。

请使用表底部的水平滚动条来查看完整的说明字段。

（PS=PowerShell，VS=Visual Studio）

> [!div class="mx-tdBreakAll"]
> | 参数 | 应用于 | 描述 |
> | --- | --- | --- |
> | ApplicationParameter |PS、VS| 指定应用程序参数的替代。<br>PowerShell 应用程序参数指定为哈希表名称/值对。 例如，@{ "VotingData_MinReplicaSetSize" = "3"; "VotingData_PartitionCount" = "1" }。<br>Visual Studio 应用程序参数可以在“发布 Service Fabric 应用程序”对话框的“应用程序参数文件”字段中指定。
> | 确认 |PS| 允许的值为 **True** 和 **False**。 提示你在运行 cmdlet 之前进行确认。 |
> | ConsiderWarningAsError |PS、VS |允许的值为 **True** 和 **False**。 默认值为 **False**。 在升级期间评估应用程序的运行状况时，将应用程序的警告运行状况事件视为错误。 默认情况下，Service Fabric 不会将警告运行状况事件评估为失败（错误），因此即使存在警告事件，升级也可以继续。 |
> | DefaultServiceTypeHealthPolicy | PS、VS |以 MaxPercentUnhealthyPartitionsPerService、MaxPercentUnhealthyReplicasPerPartition、MaxPercentUnhealthyServices 格式指定用于受监视升级的默认服务类型的运行状况策略。 例如，5,10,15 指示以下值：MaxPercentUnhealthyPartitionsPerService = 5，MaxPercentUnhealthyReplicasPerPartition = 10，MaxPercentUnhealthyServices = 15。 |
> | Force | PS、VS | 允许的值为 **True** 和 **False**。 指示升级过程跳过警告消息并强制升级，即使在版本号不变的情况下也是如此。 这适用于本地测试，但不建议用于生产环境，因为它要求删除现有的部署，导致停机和可能的数据丢失。 |
> | ForceRestart |PS、VS |如果更新配置或数据包而不更新服务代码，则仅当 ForceRestart 属性设置为 **True** 时，服务才会重启。 更新完成后，Service Fabric 将通知服务新的配置包或数据包可用。 该服务负责应用所做的更改。 如有必要，该服务可进行重启。 |
> | HealthCheckRetryTimeoutSec |PS、VS |声明升级失败之前，Service Fabric 继续执行运行状况评估的持续时间（以秒为单位）。 默认为 600 秒。 此持续时间在 *HealthCheckWaitDurationSec* 秒后开始。 在此 *HealthCheckRetryTimeout* 期间，Service Fabric 可能会对应用程序执行多次运行状况检查。 默认值为 10 分钟，应该针对应用程序相应地自定义该值。 |
> | HealthCheckStableDurationSec |PS、VS |在转到下一个升级域或完成升级之前，为了验证应用程序是否稳定而要等待的持续时间（以秒为单位）。 此等待持续时间用于防止在执行了运行状况检查后，未检测到运行状况更改。 默认值为 120 秒，应该针对应用程序相应地自定义该值。 |
> | HealthCheckWaitDurationSec |PS、VS | 完成升级域的升级后，在 Service Fabric 评估应用程序的运行状况之前需要等待的时间（以秒为单位）。 也可将此持续时间视为应用程序应先运行多长时间才可被视为正常运行。 如果运行状况检查通过，升级过程将转到下一个升级域。  如果运行状况检查失败，则在再次重试运行状况检查之前，Service Fabric 会等待 [UpgradeHealthCheckInterval](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#clustermanager) 秒的时间，直到 *HealthCheckRetryTimeoutSec* 秒的时间用完。 建议的默认值为 0 秒。 |
> | MaxPercentUnhealthyDeployedApplications|PS、VS |建议的默认值为 0。 指定在将应用程序视为不正常和升级失败之前，可以不正常的最大已部署应用程序数（请参阅[运行状况部分](service-fabric-health-introduction.md)）。 此参数在节点上定义应用程序运行状况，可帮助检查升级过程中的问题。 通常，应用程序的副本将与另一个节点负载均衡，使应用程序看上去运行正常，从而使升级继续。 通过指定严格的 *MaxPercentUnhealthyDeployedApplications* 运行状况，Service Fabric 可以快速检测应用程序包的问题，这样就产生了一种采用快速失败机制的升级。 |
> | MaxPercentUnhealthyServices |PS、VS |*DefaultServiceTypeHealthPolicy* 和 *ServiceTypeHealthPolicyMap* 的参数。 建议的默认值为 0。 指定在将应用程序视为不正常和升级失败之前，应用程序实例中可以不正常的最大服务数。 |
> | MaxPercentUnhealthyPartitionsPerService|PS、VS |*DefaultServiceTypeHealthPolicy* 和 *ServiceTypeHealthPolicyMap* 的参数。 建议的默认值为 0。 指定在将服务视为不正常之前，服务中可以不正常的最大分区数。 |
> | MaxPercentUnhealthyReplicasPerPartition|PS、VS |*DefaultServiceTypeHealthPolicy* 和 *ServiceTypeHealthPolicyMap* 的参数。 建议的默认值为 0。 指定在将分区视为不正常之前，分区中不正常的最大副本数。 |
> | ServiceTypeHealthPolicyMap | PS、VS | 代表的运行状况策略用于评估属于某个服务类型的服务的运行状况。 采用以下格式的哈希表输入：@ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} 例如：@{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | TimeoutSec | PS、VS | 指定操作的超时设置（以秒为单位）。 |
> | UpgradeDomainTimeoutSec |PS、VS |升级单个升级域的最长时间（以秒为单位）。 如果达到了此超时限制，升级会停止，然后根据 *FailureAction* 的设置继续。 默认值为 never（无期限），应该针对应用程序相应地自定义该值。 |
> | UpgradeReplicaSetCheckTimeoutSec |PS、VS |以秒为度量单位。<br>**无状态服务** - 在单个升级域内，Service Fabric 尝试确保服务的其他实例可用。 如果有多个目标实例，则 Service Fabric 等待多个实例可用，直到达到最大超时值。 此超时使用 *UpgradeReplicaSetCheckTimeoutSec* 属性指定。 如果超时到期，Service Fabric 将继续进行升级，而无论服务实例的数量。 如果只有一个目标实例，则 Service Fabric 不会等待，而是会立即继续进行升级。<br><br>**有状态服务** - 在单个升级域内，Service Fabric 尝试确保副本集具有仲裁。 Service Fabric 会等待可用的仲裁，直到达到最大超时值（由 *UpgradeReplicaSetCheckTimeoutSec* 属性指定）。 如果超时到期，Service Fabric 将继续进行升级，而无论是否具有仲裁。 前滚时，此设置设置为 never（无限）；回退时，设置为 1200 秒。 |
> | UpgradeTimeoutSec |PS、VS |应用于整个升级的超时（以秒为单位）。 如果达到了此超时限制，则升级会停止，并会触发 *FailureAction*。 默认值为 never（无期限），应该针对应用程序相应地自定义该值。 |
> | WhatIf | PS | 允许的值为 **True** 和 **False**。 显示如果此 cmdlet 运行将会发生什么。 此 cmdlet 未运行。 |

*MaxPercentUnhealthyServices*、*MaxPercentUnhealthyPartitionsPerService* 和 *MaxPercentUnhealthyReplicasPerPartition* 条件可按照应用程序实例的服务类型进行指定。 为每个服务设置这些参数可让应用程序包含不同评估策略的不同服务类型。 例如，无状态网关服务类型可以有一个 *MaxPercentUnhealthyPartitionsPerService*，它不同于特定应用程序实例的有状态引擎服务类型。

## <a name="sfctl-parameters"></a>SFCTL 参数

通过 Service Fabric CLI 进行的 Service Fabric 应用程序升级使用 [sfctl application upgrade](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade) 命令以及下面的必需和可选参数。

### <a name="required-parameters"></a>必需的参数

| 参数 | 描述 |
| --- | --- |
| application-id  |要升级的应用程序的 ID。 <br> 这通常是不带“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“\~”字符隔开。 例如，如果应用程序名称为 "fabric：/myapp/app1"，则6.0 及更低版本中的应用程序标识为 "myapp\~app1"，在以前的版本中为 "myapp/app1"。|
application-version |作为升级目标的应用程序类型版本。|
参数  |升级应用程序时应用的应用程序参数替代的 JSON 编码列表。|

### <a name="optional-parameters"></a>可选参数

| 参数 | 描述 |
| --- | --- |
default-service-health-policy | 默认使用的健康策略的 [JSON](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy) 编码规范，用于评估服务类型的运行状况。 映射默认为空。 |
failure-action | 允许的值为 **Rollback**、**Manual** 和 **Invalid**。 *Monitored* 升级遇到违反监视策略或运行状况策略的情况时需执行的补救操作。 <br>**Rollback** 指定将升级自动回退到升级前的版本。 <br>**Manual** 指示将升级切换到 *UnmonitoredManual* 升级模式。 <br>**Invalid** 指示故障操作无效。|
force-restart | 如果更新配置或数据包而不更新服务代码，则仅当 ForceRestart 属性设置为 **True** 时，服务才会重启。 更新完成后，Service Fabric 将通知服务新的配置包或数据包可用。 该服务负责应用所做的更改。 如有必要，该服务可进行重启。 |
health-check-retry-timeout | 执行 *FailureAction* 前，当应用程序或群集不正常时，重试运行状况评估所需的时间。 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 默认值：PT0H10M0S。 |
health-check-stable-duration | 升级继续到下一升级域之前，应用程序或群集必须保持正常的时长。 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 默认值：PT0H2M0S。 |
health-check-wait-duration | 应用运行状况策略之前，完成升级域后等待的时间长度。 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 默认值：0。|
max-unhealthy-apps | 建议的默认值为 0。 指定在将应用程序视为不正常和升级失败之前，可以不正常的最大已部署应用程序数（请参阅[运行状况部分](service-fabric-health-introduction.md)）。 此参数在节点上定义应用程序运行状况，可帮助检查升级过程中的问题。 通常，应用程序的副本将与另一个节点负载均衡，使应用程序看上去运行正常，从而使升级继续。 通过指定严格的 *max-unhealthy-apps* 运行状况，Service Fabric 可以快速检测应用程序包的问题，这样就产生了一种采用快速失败机制的升级。 由介于 0 到 100 间的数字表示。 |
mode | 允许的值为 **Monitored**、**UpgradeMode**、**UnmonitoredAuto**、**UnmonitoredManual**。 默认值为 **UnmonitoredAuto**。 有关这些值的说明，请参阅 Visual Studio 和 PowerShell *必需的参数*部分。|
replica-set-check-timeout |以秒为度量单位。 <br>**无状态服务** - 在单个升级域内，Service Fabric 尝试确保服务的其他实例可用。 如果有多个目标实例，则 Service Fabric 等待多个实例可用，直到达到最大超时值。 此超时是使用 *replica-set-check-timeout* 属性指定的。 如果超时到期，Service Fabric 将继续进行升级，而无论服务实例的数量。 如果只有一个目标实例，则 Service Fabric 不会等待，而是会立即继续进行升级。<br><br>**有状态服务** - 在单个升级域内，Service Fabric 尝试确保副本集具有仲裁。 Service Fabric 将等待一个仲裁可用，直到达到最大超时值（由 *replica-set-check-timeout* 属性指定）。 如果超时到期，Service Fabric 将继续进行升级，而无论是否具有仲裁。 前滚时，此设置设置为 never（无限）；回退时，设置为 1200 秒。 |
service-health-policy | 包含每个服务类型名称的服务类型健康策略的 JSON 编码映射。 映射默认为空。 [参数 JSON 格式。](https://docs.microsoft.com/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap) “Value”部分的 JSON 包含 **MaxPercentUnhealthyServices**、**MaxPercentUnhealthyPartitionsPerService** 和 **MaxPercentUnhealthyReplicasPerPartition**。 有关这些参数的说明，请参阅 Visual Studio 和 PowerShell “可选参数”部分。
timeout | 指定操作的超时设置（以秒为单位）。 默认值：60。 |
upgrade-domain-timeout | 执行 *FailureAction* 前，每个升级域需等待的时长。 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 默认值为 never（无期限），应该针对应用程序相应地自定义该值。 默认值：P10675199DT02H48M05.4775807S。 |
upgrade-timeout | 执行 *FailureAction* 前，每个升级域需等待的时长。 首先，会将其解释为表示 ISO 8601 持续时间的一个字符串。 如果那失败，则会将其解释为表示总毫秒数的一个数字。 默认值为 never（无期限），应该针对应用程序相应地自定义该值。 默认值：P10675199DT02H48M05.4775807S。|
warning-as-error | 允许的值为 **True** 和 **False**。 默认值为 **False**。 可以作为标记进行传递。 在升级期间评估应用程序的运行状况时，将应用程序的警告运行状况事件视为错误。 默认情况下，Service Fabric 不会将警告运行状况事件评估为失败（错误），因此即使存在警告事件，升级也可以继续。 |

## <a name="next-steps"></a>后续步骤
[使用 Visual Studio 升级应用程序](service-fabric-application-upgrade-tutorial.md)逐步讲解了如何使用 Visual Studio 进行应用程序升级。

[使用 Powershell 升级应用程序](service-fabric-application-upgrade-tutorial-powershell.md)逐步讲解了如何使用 PowerShell 进行应用程序升级。

[在 Linux 上使用 Service Fabric CLI 升级应用程序](service-fabric-application-lifecycle-sfctl.md#upgrade-application)介绍如何使用 Service Fabric CLI 完成应用程序升级。

[使用 Service Fabric Eclipse 插件升级应用程序](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application)

了解如何使用[数据序列化](service-fabric-application-upgrade-data-serialization.md)，使应用程序在升级后保持兼容。

参考[高级主题](service-fabric-application-upgrade-advanced.md)，了解如何在升级应用程序时使用高级功能。

参考[对应用程序升级进行故障排除](service-fabric-application-upgrade-troubleshooting.md)中的步骤来解决应用程序升级时的常见问题。
