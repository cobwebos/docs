---
title: Azure Service Fabric 版本
description: Service Fabric 中的最新功能和改进的发行说明。
author: athinanthny
manager: gamonroy
ms.author: atsenthi
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 496a5babe58be4354ffb10a331d35abc8a51b04d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186522"
---
# <a name="service-fabric-releases"></a>Service Fabric 发布

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑难解答指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题跟踪</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支持的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代码示例</a>

本文提供了有关 Service Fabric 运行时和 Sdk 的最新版本和更新的详细信息。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 中的新增功能

### <a name="service-fabric-70"></a>Service Fabric 7。0

Azure Service Fabric 7.0 现已推出！ 可以通过 Azure 门户或 Azure 资源管理器部署更新到7.0。 由于客户反馈在假日期限内发布，因此，在1月之前，不会开始自动更新群集设置为接收自动升级。
在1月，我们将继续执行标准的推出过程，并且启用了自动升级的群集将开始自动接收7.0 更新。 在开始前，我们将提供另一个通知。
我们还将更新我们的计划发布日期，以表明我们考虑到了此策略。 请在此处查看我们未来[发布计划](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。
 
这是 Service Fabric 的最新版本，并已加载主要功能和改进。

### <a name="key-announcements"></a>关键公告
 - [**KeyVaultReference 对应用程序机密（预览版）的支持**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)：已启用[托管标识](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)的 Service Fabric 应用程序现在可以直接引用 Key Vault 机密 URL 作为环境变量、应用程序参数或容器存储库凭据。 Service Fabric 将使用应用程序的托管标识自动解析密钥。 
     
- **提高无状态服务的升级安全性**：为了保证应用程序升级期间的可用性，我们引入了新的配置，用于定义[无状态服务的最小实例数](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)被视为可用。 对于所有服务，此值都为1，且不可更改。 凭借这项全新的每个服务安全检查，你可以确保服务在应用程序升级、群集升级和其他维护（依赖于 Service Fabric 的健康和安全检查）期间保留最少的实例数。
  
- [**用户服务的资源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：用户可以为节点上的用户服务设置资源限制，以防止诸如 Service Fabric 系统服务资源耗尽等情况。 
  
- 对于副本类型，[**服务移动成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 仅当群集中有违反约束的约束冲突时，移动成本非常高的副本才会移动。 有关使用 "非常高" 移动成本的详细信息，请参阅文档，了解其他注意事项。
  
-  **其他群集安全检查**：在此版本中，我们引入了可配置的种子节点仲裁安全检查。 这使你可以自定义在群集生命周期和管理方案中必须有多少种子节点可用。 将阻止在配置的值下面执行群集的操作。 现在，默认值始终是种子节点的仲裁，例如，如果有7种子节点，则默认情况下会阻止在5种子节点下使用的操作。 进行此更改后，你可以将最小安全值设置为6，这一次只允许关闭一个种子节点。
   
- 添加了对[**管理 Service Fabric Explorer 中的备份和还原服务**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支持。 这使得可以直接从 SFX 内进行以下活动：发现备份和还原服务、创建备份策略、启用自动备份、执行即席备份、触发还原操作和浏览现有备份。

- 发布[**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具可帮助验证在滚动应用程序升级期间，在可靠集合中使用的类型是否为向前和向后兼容。 这有助于防止因类型缺失或不兼容而导致升级失败或数据丢失和数据损坏。

- [**在辅助副本上启用稳定读取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：稳定读取会限制辅助副本以返回已确认的值。

此外，此版本还包含其他新功能、bug 修复和性能改进。 有关更改的完整列表，请参阅[发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

| 发行日期 | 发布 | 更多信息 |
|---|---|---|
| 2019年11月18日 | [Azure Service Fabric 7。0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|


### <a name="service-fabric-65"></a>Service Fabric 6。5

此版本包含可支持性、可靠性和性能改进、新功能、bug 修复和增强功能，以便简化群集和应用程序生命周期管理。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中 Service Fabric 工具支持的最终版本。 建议客户转到[Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 。

下面是 Service Fabric 6.5 中的新是：

- Service Fabric Explorer 包含用于检查已上载到映像存储的应用程序的[映像存储查看器](service-fabric-visualizing-your-cluster.md#image-store-viewer)。

- [修补业务流程应用程序（POA）](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含许多自我诊断改进。 建议将 POA 的客户迁移到此版本。

- [默认情况下](service-fabric-visualizing-your-cluster.md#event-store)，为 Service Fabric 6.5 群集启用 EventStore 服务，除非已选择退出。

- 为有状态服务添加了[副本生命周期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [更好地了解种子节点状态](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括在种子节点不正常（*关闭*、*删除*或*未知*）时的群集级警告。

- [Service Fabric 应用程序灾难恢复工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允许 Service Fabric 有状态服务在主群集遇到灾难时迅速恢复。 使用定期备份和还原在辅助备用应用程序上持续同步主群集中的数据。

- Visual Studio 支持[将 .Net Core 应用程序发布到基于 Linux 的群集](service-fabric-how-to-publish-linux-app-vs.md)。

- 当你在 Azure 上升级或创建新的 Linux 群集时，将自动为 Service Fabric 6.5 （以及更高版本）安装[azure SERVICE FABRIC CLI （SFCTL）](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 。

- 默认情况下，在 MacOS/Linux OneBox 群集上安装[SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) 。

有关更多详细信息，请参阅[Service Fabric 6.5 发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5 版本

| 发行日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2日 | [Azure Service Fabric 6.5 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 8月23日，2019 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md)  |


## <a name="previous-versions"></a>以前的版本

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 发行日期 | 发布 | 更多信息 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [适用于 Windows 群集的 Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 5月2日，2019 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
