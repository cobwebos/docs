---
title: 使用 Application Insights 探查实时 Azure 应用服务应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure 应用服务上的实时应用。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 4d41ece86240a20afea06bff3469b5c02c6e46ff
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121191"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure 应用服务应用

你可以在使用基本服务层级或更高层级的 Azure 应用服务上运行的 ASP.NET 和 ASP.NET Core 应用上运行 Profiler。 目前只有通过[此方法](profiler-aspnetcore-linux.md)才能在 Linux 上启用 Profiler。

## <a name="enable-profiler-for-your-app"></a><a id="installation"></a> 为应用启用 Profiler
若要为应用启用 Profiler，请遵循下面的说明。 如果你在运行另一种类型的 Azure 服务，则下面提供了用于在其他受支持平台上启用 Profiler 的说明：
* [云服务](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 应用程序](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虚拟机](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 是作为应用服务运行时的一部分预先安装的。 下面的步骤将展示如何为应用服务启用它。 请遵循这些步骤，即使你在构建时已在你的应用中包括了 App Insights SDK。

1. 为你的应用服务启用“Always On”设置。 你可以在应用服务的“配置”页面中在“常规设置”下更新设置。
1. **** 转到 Azure 门户中的“应用服务”窗格。
1. 导航到“设置”>“Application Insights”窗格****。

   ![在应用服务门户上启用 App Insights](./media/profiler/AppInsights-AppServices.png)

1. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外，请确保 Profiler 已启用****。 如果你的 Application Insights 资源位于与应用服务不同的订阅中，则无法使用此页面来配置 Application Insights。 你仍然可以通过手动创建所需的应用设置来手动执行此操作。 [下一部分包含有关手动启用 Profiler 的说明。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![添加 App Insights 站点扩展][Enablement UI]

1. 现已使用应用服务应用设置启用了 Profiler。

    ![Profiler 的应用设置][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手动或使用 Azure 资源管理器启用 Profiler
可以通过为你的 Azure 应用服务创建应用设置来启用 Application Insights Profiler。 包含如上所示选项的页面为你创建这些应用设置。 但是，你可以使用模板或其他方式自动创建这些设置。 如果你的 Application Insights 资源与 Azure 应用服务处于不同的订阅中，这些设置也将起作用。
下面是启用 Profiler 时需要执行的配置：

|应用设置    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 资源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~3 |


可以使用 [Azure 资源管理器模板](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、[Azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp)、[Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest) 设置这些值。

### <a name="enabling-profiler-for-other-clouds-manually"></a>手动为其他云启用 Profiler

如果要为其他云启用 Profiler，可以使用以下应用设置。

|应用设置    | 美国政府值| 中国云 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | `https://agent.serviceprofiler.azure.us`    | `https://profiler.applicationinsights.azure.cn` |
|ApplicationInsightsEndpoint | `https://dc.applicationinsights.us` | `https://dc.applicationinsights.azure.cn` |

## <a name="disable-profiler"></a>禁用 Profiler

若要为单个应用实例停止或重启 Profiler，请在“Web 作业”下转到“应用资源”****。 若要删除 Profiler，请转到“扩展”****。

![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有应用上都启用 Profiler，以便尽早发现任何性能问题。

使用 WebDeploy 将更改部署到 Web 应用程序时可能会删除 Profiler 的文件。 可以通过从部署期间的删除中排除 App_Data 文件夹来防止此删除。 


## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
