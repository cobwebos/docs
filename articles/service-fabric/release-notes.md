---
title: Azure Service Fabric 版本
description: Azure Service Fabric 的发行说明。 包含有关 Service Fabric 中的最新功能和改进的信息。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 28870a197af07e964a50a06ffeef08f3b71451f4
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891729"
---
# <a name="service-fabric-releases"></a>Service Fabric 发布

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">疑难解答指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题跟踪</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支持的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代码示例</a>

本文提供了有关 Service Fabric 运行时和 Sdk 的最新版本和更新的详细信息。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 中的新增功能

### <a name="service-fabric-71"></a>Service Fabric 7。1
由于当前 COVID-19 危机，并且考虑到我们的客户所面临的挑战，我们提供7.1，但不会自动升级设置为接收自动升级的群集。 我们正在暂停自动升级，直到出现进一步的通知，以确保客户可以在最适合的情况下应用升级，以避免意外中断。

你可以通过[Azure 门户](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal)或[Azure 资源管理器部署](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)更新到7.1。

恢复标准推出过程后，启用了自动升级的 Service Fabric 群集将开始自动接收7.1 更新。 我们将在标准推出[Service Fabric 技术社区网站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)开始之前提供另一公告。
对于从 6.5[到7.1 的](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions)主要发布，我们还发布了到支持日期的更新。 

## <a name="what-is-new-in-service-fabric-71"></a>Service Fabric 7.1 的新功能是什么？
我们非常高兴地宣布 Service Fabric 的下一版本。 此版本已加载，其中包含主要功能和改进。 下面突出显示了一些主要功能：
## <a name="key-announcements"></a>关键公告
- **General Availability** [ **Service Fabric 应用程序 Service Fabric 托管标识**的公开上市](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**支持 Ubuntu 18.04**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**预览版：虚拟机规模集临时 os 磁盘支持**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *：临时 os 磁盘是在本地虚拟机上创建的存储，不保存到远程 Azure 存储。 建议为所有 Service Fabric 节点类型（主节点和辅助节点类型）执行这些操作，这与传统的永久性 OS 磁盘相比，临时操作系统磁盘相同：
      -  降低操作系统磁盘的读/写延迟
      -  实现更快的重置/重新映像节点管理操作
      -  降低总体成本（磁盘免费，不会产生额外的存储成本）
- 支持[**按使用者公用名对 Service Fabric 应用程序声明服务终结点证书**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)。
- [**容器化服务的运行状况探测支持**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)：支持容器化应用程序的活动探测机制。 活动探测帮助公布容器化应用程序的活动，并在这些应用程序不及时响应时，会导致重新启动。 
- 支持[容器](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview)和[来宾可执行](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction)应用程序的[**初始化代码包**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages)。 这允许按指定顺序执行代码包（如容器）来执行服务包初始化。
- **FabricObserver 和 ClusterObserver**是无状态应用程序，用于捕获与 SF 的不同方面相关的 Service Fabric 遥测。 这两个应用程序都准备好部署到 Windows 生产群集，以捕获丰富的遥测并实现对 Applicationinsights.config、EventSource 和 LogAnalytics 的支持。
    - [**FabricObserver （FO） 2.0**](https://github.com/microsoft/service-fabric-observer)-在所有节点上运行，会生成运行状况事件，并在达到用户配置的资源使用率阈值时发出遥测。 此版本包含跨监视、数据管理、运行状况事件详细信息、结构化遥测的几项增强功能。
     - [**ClusterObserver （CO） 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) -在一个节点上运行，捕获群集级别运行状况遥测。 在此版本中，ClusterObserver 还监视节点状态，并在节点处于关闭/禁用/禁用状态的时间超过用户指定的时间段时发出遥测。

### <a name="improve-application-life-cycle-experience"></a>提高应用程序生命周期体验

- **[预览：请求排出](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**：在计划内服务维护（如服务升级或节点停用）期间，你希望允许服务正常排出连接。 此功能在服务配置中添加实例关闭延迟持续时间。 在计划的操作期间，SF 会从发现中删除服务的地址，并在关闭服务前等待此持续时间。
- **[自动 Subcluster 检测和平衡](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**：当具有不同放置约束的服务具有一个通用[负载度量值](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)时，将发生 Subclustering。 如果不同组节点上的负载差别很大，则 Service Fabric 群集资源管理器认为群集不均衡，即使由于放置约束的原因而达到了最大的平衡。 因此，它会尝试重新平衡群集，这可能会导致不必要的服务移动（因为无法显著提高 "不平衡"）。 从此版本开始，群集资源管理器现在将尝试自动检测这些种类的配置，并了解何时可以通过移动来修复不平衡的情况，以及在不能对其进行重大改进的情况下，应该单独保留一些内容。  
- [**辅助副本的不同移动成本**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)：我们引入了新的移动成本值 VeryHigh，在某些方案中提供了额外的灵活性，以定义是否应为辅助副本使用单独的移动成本。
- 已为容器化应用程序启用[**活动探测**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage )机制。 活动探测帮助公布容器化应用程序的活动，并在这些应用程序不及时响应时，会导致重新启动。
- [**针对服务运行到完成/一次**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>映像存储改进
 - **默认情况下，Service Fabric 7.1 使用自定义传输来保护节点之间的文件传输**。 对 SMB 文件共享的依赖关系从版本7.1 中移除。 受保护的 SMB 文件共享仍然存在于包含客户选择映像存储服务副本的节点上，以便选择退出默认值以及升级并降级到旧版本。
       
 ### <a name="reliable-collections-improvements"></a>可靠集合改进

- [**在 "仅内存" 中，使用可靠集合对有状态服务的存储支持：通过**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)可变可靠集合，可以将数据保留在磁盘中，以实现大规模中断，可用于复制缓存等工作负荷，例如，偶尔可能会丢失数据。 根据[易失性可靠集合的限制和限制](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)，我们建议将此用于不需要持久性的工作负荷，以应对用于处理仲裁丢失情况的服务。
- [**预览： Service Fabric 备份资源管理器**](https://github.com/microsoft/service-fabric-backup-explorer)：为了简化 Service Fabric 有状态应用程序的可靠集合备份的管理，Service Fabric 备份资源管理器使用户能够
    - 审核和查看可靠集合的内容，
    - 将当前状态更新为一致视图
    - 创建可靠集合的当前快照的备份
    - 修复数据损坏
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2020年4月20日 | [Azure Service Fabric 7。1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [发行说明](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7。0

Azure Service Fabric 7.0 现已推出！ 可以通过 Azure 门户或 Azure 资源管理器部署更新到7.0。 由于客户反馈在假日期限内发布，因此，在1月之前，不会开始自动更新群集设置为接收自动升级。
在1月，我们将继续执行标准的推出过程，并且启用了自动升级的群集将开始自动接收7.0 更新。 在开始前，我们将提供另一个通知。
我们还将更新我们的计划发布日期，以表明我们考虑到了此策略。 请在此处查看我们未来[发布计划](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。
 
这是 Service Fabric 的最新版本，并已加载主要功能和改进。

### <a name="key-announcements"></a>关键公告
 - [**KeyVaultReference 对应用程序机密（预览版）的支持**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)：已启用[托管标识](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)的 Service Fabric 应用程序现在可以直接引用 Key Vault 机密 URL 作为环境变量、应用程序参数或容器存储库凭据。 Service Fabric 将使用应用程序的托管标识自动解析密钥。 
     
- **提高无状态服务的升级安全性**：为了保证应用程序升级期间的可用性，我们引入了新的配置，用于定义[无状态服务的最小实例数](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)被视为可用。 对于所有服务，此值都为1，且不可更改。 凭借这项全新的每个服务安全检查，你可以确保服务在应用程序升级、群集升级和其他维护（依赖于 Service Fabric 的健康和安全检查）期间保留最少的实例数。
  
- [**用户服务的资源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：用户可以为节点上的用户服务设置资源限制，以防止诸如 Service Fabric 系统服务资源耗尽等情况。 
  
- 对于副本类型，[**服务移动成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 仅当群集中有违反约束的约束冲突时，移动成本非常高的副本才会移动。 请参阅链接文档，以了解有关何时使用 "非常高" 移动成本的详细信息，以及其他注意事项。
  
-  **其他群集安全检查**：在此版本中，我们引入了可配置的种子节点仲裁安全检查。 这使你可以自定义在群集生命周期和管理方案中必须有多少种子节点可用。 将阻止在配置的值下面执行群集的操作。 现在，默认值始终是种子节点的仲裁，例如，如果有7种子节点，则默认情况下会阻止在5种子节点下使用的操作。 进行此更改后，你可以将最小安全值设置为6，这一次只允许关闭一个种子节点。
   
- 添加了对[**管理 Service Fabric Explorer 中的备份和还原服务**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支持。 这使得可以直接从 SFX 内进行以下活动：发现备份和还原服务、创建备份策略、启用自动备份、执行即席备份、触发还原操作和浏览现有备份。

- 发布[**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具可帮助验证在滚动应用程序升级期间，在可靠集合中使用的类型是否为向前和向后兼容。 这有助于防止因类型缺失或不兼容而导致升级失败或数据丢失和数据损坏。

- [**在辅助副本上启用稳定读取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：稳定读取会限制辅助副本以返回已确认的值。

此外，此版本还包含其他新功能、bug 修复和性能改进。 有关更改的完整列表，请参阅[发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure Service Fabric 7。0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020 年 3 月 2 日 | [Azure Service Fabric 7.0 刷新版本](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

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

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>以前的版本

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2018年11月30日 | [Azure Service Fabric 6。4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [适用于 Windows 群集的 Azure Service Fabric 6.4 更新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 5月2日，2019 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 5月28日，2019 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
