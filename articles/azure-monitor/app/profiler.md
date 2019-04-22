---
title: 使用 Application Insights 探查实时 Azure 应用服务应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure 应用服务上的实时应用。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: b8f6a2d12e1a9920421e6491432b516520ae110b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493733"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure 应用服务应用

你可以运行 Profiler ASP.NET 和 ASP.NET Core 应用正在运行的 Azure 应用服务上使用基本服务层或更高版本。 目前只有通过[此方法](profiler-aspnetcore-linux.md)才能在 Linux 上启用 Profiler。

## <a id="installation"></a> 为应用启用 Profiler
若要为应用启用 Profiler，请遵循下面的说明。 如果您运行不同类型的 Azure 服务，以下是有关其他支持的平台上启用 Profiler 的说明：
* [云服务](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 应用程序](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虚拟机](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

作为应用服务运行时的一部分预安装 Application Insights Profiler。 下面的步骤将说明如何为应用服务中启用它。 即使已在生成时在应用程序中包括 App Insights SDK，请执行以下步骤。

1. 启用"Always On"设置为应用服务。 您可以更新应用服务在常规设置下的配置页中的设置。
1. 转到 Azure 门户中的“应用服务”窗格。
1. 导航到“设置”>“Application Insights”窗格。

   ![在应用服务门户上启用 App Insights](./media/profiler/AppInsights-AppServices.png)

1. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外，请确保 Profiler 已启用。 如果 Application Insights 资源在应用服务属于不同订阅中，不能使用此页用于配置 Application Insights。 您仍可以执行它手动尽管通过创建必要的应用设置手动。 [下一节包含有关手动启用 Profiler 的说明。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![添加 App Insights 站点扩展][Enablement UI]

1. 现已使用应用服务应用设置启用了 Profiler。

    ![Profiler 的应用设置][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手动或使用 Azure 资源管理器中启用 Profiler
可以通过创建应用程序设置为 Azure 应用服务启用 Application Insights Profiler。 使用上面所示的选项页面创建用于这些应用程序的设置。 但是，可以自动创建的模板或其他方式使用这些设置。 Application Insights 资源是在不同的订阅从 Azure 应用服务，这些设置将还起作用。
下面是启用探查器所需的设置：

|应用设置    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 资源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


可以设置使用这些值[Azure 资源管理器模板](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)， [Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)， [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)。


## <a name="disable-profiler"></a>禁用 Profiler

若要为单个应用实例停止或重启 Profiler，请在“Web 作业”下转到“应用资源”。 若要删除 Profiler，请转到“扩展”。

![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有应用上都启用 Profiler，以便尽早发现任何性能问题。

使用 WebDeploy 将更改部署到 web 应用程序时，可以删除 Profiler 的文件。 可以通过从正在删除在部署过程中排除 App_Data 文件夹来防止删除。 


## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
