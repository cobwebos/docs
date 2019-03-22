---
title: 启用 Azure 应用服务中的.NET 应用程序的快照调试器 |Microsoft Docs
description: 启用 Azure 应用服务中的.NET 应用程序的快照调试器
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: brahmnes
ms.openlocfilehash: 7ff93dffe2dd82bdbba204b4235a297b337438f5
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57876214"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>启用 Azure 应用服务中的.NET 应用程序的快照调试器

快照调试程序目前适用于 Windows 的服务计划运行 Azure 应用服务的 ASP.NET 和 ASP.NET Core 应用。

## <a id="installation"></a> 启用快照调试程序
若要为应用启用快照调试程序，请执行下面的说明。 如果你正在运行不同类型的 Azure 服务，以下是有关其他支持的平台上启用快照调试程序的说明：
* [Azure 云服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服务](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虚拟机和虚拟机规模集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [在本地虚拟机或物理计算机](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights 快照调试程序预装的应用服务运行时，但需要将其启用以获取快照，以实现应用服务应用。 部署应用后，即使在源代码中包括了 Application Insights SDK，请执行以下步骤来启用快照调试程序。

1. 转到 Azure 门户中的“应用服务”窗格。
2. 导航到“设置”>“Application Insights”窗格。

   ![在应用服务门户上启用 App Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外请确保这两个开关的 Snapshot Debugger**上**。

   ![添加 App Insights 站点扩展][Enablement UI]

4. 快照调试程序现已启用使用应用服务应用程序设置。

    ![快照调试程序的应用设置][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>禁用快照调试器

按照相同的步骤与用于**启用 Snapshot Debugger**，但这两个开关切换到的快照调试器**关闭**。
我们建议，必须启用上所有的应用，为了便于诊断的应用程序异常的快照调试程序。

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

