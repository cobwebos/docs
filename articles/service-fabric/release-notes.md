---
title: Azure 服务结构发布
description: Azure 服务结构的发行说明。 包括有关服务结构的最新功能和改进的信息。
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729604"
---
# <a name="service-fabric-releases"></a>服务交换矩阵版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">故障排除指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题 跟踪</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支持版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代码示例</a>

本文提供有关服务结构运行时和 SDK 的最新版本和更新的详细信息。

## <a name="whats-new-in-service-fabric"></a>服务交换矩阵中的新增功能

### <a name="service-fabric-71"></a>服务结构 7.1
由于当前的 COVID-19 危机，并考虑到客户面临的挑战，我们提供 7.1，但不会自动升级设置为接收自动升级的群集。 我们将暂停自动升级，直到另行通知，以确保客户可以在最适合时应用升级，以避免意外中断。

您可以通过[Azure 门户](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal)或通过[Azure 资源管理器部署](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)更新到 7.1。

启用了自动升级的服务结构群集将在我们恢复标准推出过程后自动开始接收 7.1 更新。 在[服务面料科技社区网站](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)开始推出标准之前，我们会再提供一个公告。
我们还发布了主要版本的支持日期结束的更新，从 6.5 到 7.1[在这里](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions)。 

## <a name="what-is-new-in-service-fabric-71"></a>什么是新的服务内交换矩阵 7.1？
我们很高兴地宣布下一个版本的服务交换矩阵。 此版本加载了关键功能和改进。 下面突出显示了一些关键功能：
## <a name="key-announcements"></a>主要公告
- **General Availability**[**服务结构应用程序服务结构托管标识**的一般可用性](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**支持 Ubuntu 1804**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**预览：VMSS 临时操作系统磁盘支持**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)*： 临时 OS 磁盘是在本地虚拟机上创建的存储，而不是保存到远程 Azure 存储。 建议所有 Service Fabric 节点类型（主节点和辅助节点）使用它们，因为与传统持久性 OS 磁盘相比，临时 OS 磁盘：
      -  减少操作系统磁盘的读取/写入延迟
      -  实现更快的重置/重新映像节点管理操作
      -  降低总体成本（磁盘是免费的，不会产生额外的存储成本）
- 支持[**按主题通用名称声明服务结构应用程序的服务终结点证书应用程序**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)。
- [**对容器化服务的健康探测的支持**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)：支持容器化应用的活度探测机制。 Liveness 探测有助于宣布容器化应用程序的真实性，当它们不及时响应时，将导致重新启动。 
- 支持[容器和](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview)[来宾可执行](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction)应用程序的[**初始化代码包**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages)。 这允许按指定顺序执行代码包（例如容器）来执行服务包初始化。
- **Fabric 观察员和群集观察者**是捕获与 SF 群集的不同方面相关的服务结构遥测的无状态应用程序。 这两个应用程序都已准备好部署到 Windows 生产群集，以便通过应用程序见解、事件源和日志分析的已实施支持来捕获丰富的遥测数据。
    - [**Fabric观察员 （FO） 2.0**](https://github.com/microsoft/service-fabric-observer)- 在所有节点上运行，生成运行状况事件，在达到用户配置的资源使用阈值时发出遥测。 此版本包含多个跨监视、数据管理、运行状况事件详细信息、结构化遥测的增强功能。
     - [**群集观察者 （CO） 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - 在一个节点上运行，捕获群集级运行状况遥测。 在此版本中，ClusterObserver 还会监视节点状态，并在节点关闭/禁用/禁用的时间超过用户指定时间段时发出遥测。

### <a name="improve-application-life-cycle-experience"></a>改善应用程序生命周期体验

- **[预览：请求耗尽](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**：在计划的服务维护期间（如服务升级或节点停用）期间，您希望允许服务正常耗尽连接。 此功能在服务配置中添加实例关闭延迟持续时间。 在计划操作期间，SF 将从发现中删除服务的地址，然后等待此持续时间，然后再关闭服务。
- **[自动子群集检测和平衡](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**：当具有不同放置约束的服务具有通用[负载指标](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)时，就会发生子群集。 如果不同节点集上的负载差异很大，则 Service Fabric 群集资源管理器认为群集是不平衡的，即使由于放置限制，群集具有最佳平衡。 因此，它尝试重新平衡群集，可能导致不必要的服务移动（因为"不平衡"无法显著改善）。 从此版本开始，群集资源管理器现在将尝试自动检测这些类型的配置，并了解何时可以通过移动来修复不平衡，以及何时应该让事情独立，因为无法进行实质性的改进。  
- [**辅助副本的不同移动成本**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)：我们引入了新的移动成本值"非常高"，在某些情况下提供了额外的灵活性，以定义是否应将单独的移动成本用于辅助副本。
- 支持容器化应用[**的活度探测**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage )机制。 Liveness 探测有助于宣布容器化应用程序的真实性，当它们不及时响应时，将导致重新启动。
- [**运行到完成/一次服务**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>图像存储改进
 - 默认情况下，Service Fabric 7.1 使用**自定义传输来保护节点之间的文件传输**。 对 SMB 文件共享的依赖项将从版本 7.1 中删除。 安全的 SMB 文件共享仍然存在于包含映像存储服务副本的节点上，供客户选择退出默认值，以及升级和降级到旧版本。
       
 ### <a name="reliable-collections-improvements"></a>可靠的集合改进

- [**在内存中，仅存储支持状态服务使用可靠集合**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)： 易失性可靠集合允许将数据保存到磁盘，以持久化，以抵御大规模中断，可用于工作负载，例如，可以容忍偶尔的数据丢失。基于[易失性可靠集合的限制和限制](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)，我们建议对于不需要持久性的工作负载，对于处理罕见仲裁丢失情况的服务，我们建议这样做。
- [**预览：服务结构备份资源管理器**](https://github.com/microsoft/service-fabric-backup-explorer)：为了简化服务交换矩阵状态应用程序的可靠集合备份的管理，服务结构备份资源管理器使用户能够
    - 审核和审查可靠集合的内容，
    - 将当前状态更新为一致的视图
    - 创建可靠集合的当前快照的备份
    - 修复数据损坏
                 
### <a name="service-fabric-71-releases"></a>服务结构 7.1 版本
| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2020年4月20日 | [Azure 服务结构 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [发行说明](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>服务结构 7.0

Azure 服务结构 7.0 现已可用！ 您将能够通过 Azure 门户或通过 Azure 资源管理器部署更新到 7.0。 由于客户对假期期间发布版本的反馈，我们不会开始自动更新群集，这些群集设置为接收自动升级，直到 1 月。
在一月份，我们将恢复标准推出过程，启用自动升级的群集将自动开始接收 7.0 更新。 我们将在推出开始前提供另一个公告。
我们还将更新计划发布日期，以表明我们考虑到了此政策。 在此处查看我们未来[发布时间表](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)的更新。
 
这是服务结构的最新版本，并加载了关键功能和改进。

### <a name="key-announcements"></a>主要公告
 - [**KeyVault 对应用程序机密的支持（预览）：**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references)已启用[托管标识](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)的服务结构应用程序现在可以直接引用密钥保管库密钥 URL 作为环境变量、应用程序参数或容器存储库凭据。 Service Fabric 将使用应用程序的托管标识自动解析机密。 
     
- **改进了无状态服务的升级安全性**：为了保证应用程序升级期间的可用性，我们引入了新的配置，[以定义认为可用的无状态服务的最小实例数](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet)。 以前，此值对于所有服务都是 1，并且不可更改。 通过这种新的每服务安全检查，您可以确保服务在应用程序升级、群集升级和其他依赖于 Service Fabric 的健康和安全检查的维护期间保留最少的向上实例数。
  
- [**用户服务的资源限制**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)：用户可以为节点上的用户服务设置资源限制，以防止出现资源耗尽服务结构系统服务等情况。 
  
- [**副本类型的服务移动成本非常高**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)。 仅当群集中存在无法以任何其他方式修复的约束冲突时，才会移动具有极高移动成本的副本。 有关"非常高"移动成本的使用是否合理以及其他注意事项，请参阅文档以获取更多信息。
  
-  **其他群集安全检查**：在此版本中，我们引入了可配置的种子节点仲裁安全检查。 这允许您自定义在群集生命周期和管理方案中必须可用的种子节点数。 将群集带到配置的值以下的操作将被阻止。 今天，默认值始终是种子节点的仲裁，例如，如果您有 7 个种子节点，默认情况下将低于 5 个种子节点的操作被阻止。 通过此更改，您可以创建最小安全值 6，这样一次只能关闭一个种子节点。
   
- 添加了对[**在服务结构资源管理器中管理备份和还原服务**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster)的支持。 这使得以下活动可以直接从 SFX 内部进行：发现备份和还原服务、创建备份策略、启用自动备份、执行临时备份、触发还原操作和浏览现有备份。

- 宣布[**可靠集合缺失类型工具**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)的可用性：此工具有助于验证在滚动应用程序升级期间，可靠集合中使用的类型前后兼容。 这有助于防止升级失败或数据丢失以及由于缺少或不兼容的类型而导致的数据损坏。

- [**在辅助副本上启用稳定读取**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)：稳定读取将限制辅助副本返回已仲裁已解除的值。

此外，此版本还包含其他新功能、Bug 修复以及可支持性、可靠性和性能改进。 有关更改的完整列表，请参阅[发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)。

### <a name="service-fabric-70-releases"></a>服务交换矩阵 7.0 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 11 月 18 日 | [Azure 服务结构 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020年1月30日 | [Azure 服务结构 7.0 刷新发布](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020年2月6日 | [Azure 服务结构 7.0 刷新发布](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020年3月2日 | [Azure 服务结构 7.0 刷新发布](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>服务结构 6.5

此版本包括可支持性、可靠性和性能改进、新功能、错误修复以及简化群集和应用程序生命周期管理的增强功能。

> [!IMPORTANT]
> Service Fabric 6.5 是 Visual Studio 2015 中支持服务交换矩阵工具的最终版本。 建议客户今后前往[Visual Studio 2019。](https://visualstudio.microsoft.com/vs/)

以下是服务交换矩阵 6.5 中的新增功能：

- 服务结构资源管理器包括用于检查已上载到映像存储的应用程序[的图像存储查看器](service-fabric-visualizing-your-cluster.md#image-store-viewer)。

- [修补程序编排应用程序 （POA）](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包含许多自诊断改进。 建议 POA 的客户迁移到此版本。

- 默认情况下，服务结构 6.5 群集[启用事件存储服务](service-fabric-visualizing-your-cluster.md#event-store)，除非您已选择退出。

- 为有状态服务添加了[副本生命周期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)。

- [种子节点状态的可见性更好](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括种子节点不正常（*向下*、*删除*或*未知*）的群集级警告。

- [服务结构应用程序灾难恢复工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允许在主群集遇到灾难时快速恢复服务交换矩阵状态服务。 主群集中的数据使用定期备份和还原在辅助备用应用程序中持续同步。

- Visual Studio 支持[将 .NET 核心应用程序发布到基于 Linux 的群集](service-fabric-how-to-publish-linux-app-vs.md)。

- 升级或创建 Azure 上的新 Linux 群集时，将自动为服务结构 6.5（和更高版本）安装[Azure 服务结构 CLI （SFCTL）。](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)

- [默认情况下，SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)安装在 MacOS/Linux OneBox 群集上。

有关详细信息，请参阅[服务结构 6.5 发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

### <a name="service-fabric-65-releases"></a>服务交换矩阵 6.5 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2019 年 6 月 11 日 | [Azure 服务结构 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2日 | [Azure 服务结构 6.5 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019 年 7 月 29 日 | [Azure 服务结构 6.5 刷新发布](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Aug 23, 2019 | [Azure 服务结构 6.5 刷新发布](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019年10月14日 | [Azure 服务结构 6.5 刷新发布](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [发行说明](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>以前的版本

### <a name="service-fabric-64-releases"></a>服务交换矩阵 6.4 版本

| 发布日期 | 发布 | 更多信息 |
|---|---|---|
| 2018年11月30日 | [Azure 服务结构 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Azure 服务结构 6.4 Windows 群集的刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019年2月4日 | [Azure 服务结构 6.4 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019年3月4日 | [Azure 服务结构 6.4 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019年4月8日 | [Azure 服务结构 6.4 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019年5月2日 | [Azure 服务结构 6.4 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019年5月28日 | [Azure 服务结构 6.4 刷新发布](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
