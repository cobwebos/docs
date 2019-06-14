---
title: Azure Service Fabric 版本
description: Service Fabric 中的最新的功能和改进的发行说明。
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 5610c6d31732144086812bb02b65cfaffa067eae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67062993"
---
# <a name="service-fabric-releases"></a>Service Fabric 版本

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">故障排除指南</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">问题跟踪</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">支持选项</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">受支持的版本</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">的代码示例</a>

本文提供的最新版本和 Service Fabric 运行时和 Sdk 的最新更新的详细信息。

## <a name="whats-new-in-service-fabric"></a>**什么是 Service Fabric 中的新增功能**

### <a name="service-fabric-65"></a>Service Fabric 6.5

最新的 Service Fabric 版本包括可支持性、 可靠性和性能改进的新功能、 bug 修复和增强功能，以简化群集和应用程序生命周期管理。

> [!IMPORTANT]
> Service Fabric 6.5 是使用 Service Fabric 工具支持在 Visual Studio 2015 中的最终版本。 建议客户将移到[Visual Studio 2019](https://visualstudio.microsoft.com/vs/)今后。

下面是什么是 Service Fabric 6.5 中的新增功能：

- 包括 Service Fabric Explorer[图像存储查看器](service-fabric-visualizing-your-cluster.md#image-store-viewer)用于检查应用程序上传到映像存储区。

- [修补业务流程应用程序 (POA)](service-fabric-patch-orchestration-application.md)版本[1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0)包括了许多自诊断改进。 建议使用 POA 客户可迁移到此版本。

- [默认情况下启用 EventStore 服务](service-fabric-visualizing-your-cluster.md#event-store)适用于 Service Fabric 6.5 群集，除非已选择退出。

- 添加[副本生命周期事件](service-fabric-diagnostics-event-generation-operational.md#replica-events)对于有状态服务。

- [更好的种子节点状态的可见性](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)，包括群集级别警告，如果种子节点处于不正常状态 (*向下*，*已删除*或*未知*)。

- [Service Fabric 应用程序灾难恢复工具](https://github.com/Microsoft/Service-Fabric-AppDRTool)允许 Service Fabric 有状态服务主群集在遇到灾难时快速恢复。 在使用定期备份和还原的辅助备用应用程序上不断同步主群集中的数据。

- 适用于 visual Studio 支持[.NET Core 应用发布到基于 Linux 的群集](service-fabric-how-to-publish-linux-app-vs.md)。

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)将自动安装适用于 Service Fabric 6.5 （和更高版本） 升级或 Azure 上创建新的 Linux 群集。

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) MacOS/Linux 单机群集上的默认情况下安装。

有关更多详细信息，请参阅[Service Fabric 6.5 发行说明](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)。

## <a name="previous-versions"></a>以前的版本

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4 版本

| 发布日期 | 发行版本 | 更多信息 |
|---|---|---|
| 2018 年 11 月 30日日 | [Azure Service Fabric 6.4 ](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018 年 12 月 12 日 | [Azure Service Fabric 6.4 刷新版本的 Windows 群集](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [发行说明](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019 年 2 月 4日日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019 年 3 月 4日日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019 年 4 月 8日日 | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019 年 5 月 2 日， | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019 年 5 月 28 日， | [Azure Service Fabric 6.4 刷新版本](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [发行说明](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
