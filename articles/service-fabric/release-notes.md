---
title: Azure Service Fabric 版本
description: Service Fabric 中的最新功能和改进的发行说明。
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 4a681b3a09def3a7b27b603cf5201aebdbf2e4bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72386206"
---
# <a name="service-fabric-releases"></a>Service Fabric 发布

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">故障排除指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题跟踪</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">支持的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">代码示例</a>

本文提供了有关 Service Fabric 运行时和 Sdk 的最新版本和更新的详细信息。

## <a name="whats-new-in-service-fabric"></a>Service Fabric 中的新增功能

### <a name="service-fabric-65"></a>Service Fabric 6。5

最新的 Service Fabric 版本包含可支持性、可靠性和性能改进、新功能、bug 修复和增强功能，以简化群集和应用程序生命周期管理。

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
| 2019年6月11日 | [Azure Service Fabric 6。5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019 年 7 月 2 日 | [Azure Service Fabric 6.5 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019年7月29日 | [Azure Service Fabric 6.5 刷新版本](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
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
