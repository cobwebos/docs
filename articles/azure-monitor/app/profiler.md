---
title: 使用 Application Insights 探查实时 Azure 应用服务应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure 应用服务上的实时应用。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: daba72639e190bb255dd515237d25c20df433901
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075016"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure 应用服务应用

Profiler 目前适用于 Azure 应用服务上运行的 ASP.NET 和 ASP.NET Core 应用。 若要使用 Profiler，“基本”服务层或更高层是必需的。 目前只有通过[此方法](profiler-aspnetcore-linux.md)才能在 Linux 上启用 Profiler。

## <a id="installation"></a> 为应用启用 Profiler
若要为应用启用 Profiler，请遵循下面的说明。 如果你在运行另一种类型的 Azure 服务，则下面提供了用于在其他受支持平台上启用 Profiler 的说明：
* [云服务](../../azure-monitor/app/profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 应用程序](../../azure-monitor/app/profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [虚拟机](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

预安装 Application Insights Profiler 作为应用服务运行时的一部分，但需启用它才能获得适用于应用服务应用的配置文件。 部署应用后，即使在源代码中包括了 App Insights SDK，也要执行以下步骤来启用 Profiler。

1. 转到 Azure 门户中的“应用服务”窗格。
2. 导航到“设置”>“Application Insights”窗格。

   ![在应用服务门户上启用 App Insights](./media/profiler/AppInsights-AppServices.png)

3. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外，请确保 Profiler 已启用。

   ![添加 App Insights 站点扩展][Enablement UI]

4. 现已使用应用服务应用设置启用了 Profiler。

    ![Profiler 的应用设置][profiler-app-setting]

## <a name="disable-profiler"></a>禁用 Profiler

若要为单个应用实例停止或重启 Profiler，请在“Web 作业”下转到“应用资源”。 若要删除 Profiler，请转到“扩展”。

![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有应用上都启用 Profiler，以便尽早发现任何性能问题。

如果使用 WebDeploy 将更改部署到 Web 应用程序，请确保排除 App_Data 文件夹，以防在部署期间将它删除。 否则，下一次将 Web 应用程序部署到 Azure 时，会删除 Profiler 扩展文件。



## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
