---
title: 在 Azure App Service 中为 .NET 应用启用 Snapshot Debugger |Microsoft Docs
description: 启用 Azure App Service 中的 .NET 应用 Snapshot Debugger
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671420"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>启用 Azure App Service 中的 .NET 应用 Snapshot Debugger

Snapshot Debugger 当前适用于在 Windows 服务计划的 Azure App Service 上运行的 ASP.NET 和 ASP.NET Core 应用。

## <a id="installation"></a>启用 Snapshot Debugger
若要为应用启用 Snapshot Debugger，请按照下面的说明进行操作。 如果运行的是其他类型的 Azure 服务，请参阅以下说明，了解如何在其他受支持的平台上启用 Snapshot Debugger：
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

如果你使用的是预览版 .NET Core，请先按照为[其他环境启用 Snapshot Debugger 的](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)说明操作，将[microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 包添加到应用程序中，然后完成下面的说明。 

Application Insights Snapshot Debugger 作为应用服务运行时的一部分预安装，但你需要将其打开，以便为你的应用服务应用获取快照。 部署应用后，即使已在源代码中包含 Application Insights SDK，请按照以下步骤启用快照调试器。

1. 转到 Azure 门户中的“应用服务”窗格。
2. 导航到“设置”>“Application Insights”窗格。

   ![在应用服务门户上启用 App Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 同时确保两个开关 Snapshot Debugger 都已**打开**。

   ![添加 App Insights 站点扩展][Enablement UI]

4. 现在使用应用服务应用设置启用 Snapshot Debugger。

    ![Snapshot Debugger 的应用设置][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>禁用 Snapshot Debugger

遵循与**启用 Snapshot Debugger**相同的步骤，但将 Snapshot Debugger 的两个开关切换为 "**关闭**"。
建议你在所有应用上都启用了 Snapshot Debugger，以方便诊断应用程序异常。

## <a name="next-steps"></a>后续步骤

- 生成可触发异常的应用程序的流量。 然后，等待10到15分钟，将快照发送到 Application Insights 的实例。
- 请参阅 Azure 门户中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 若要帮助解决 Snapshot Debugger 问题，请参阅[Snapshot Debugger 故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

