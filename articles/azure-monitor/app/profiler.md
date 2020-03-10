---
title: 使用 Application Insights 探查实时 Azure 应用服务应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure 应用服务上的实时应用。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ba9a2aca73dbdb8de298b68670fd6ab16f810a4d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367672"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>使用 Application Insights 探查实时 Azure 应用服务应用

你可以在 ASP.NET 上运行 Profiler，并使用基本服务层或更高级别在 Azure App Service 上运行 ASP.NET Core 应用。 目前只有通过[此方法](profiler-aspnetcore-linux.md)才能在 Linux 上启用 Profiler。

## <a id="installation"></a> 为应用启用 Profiler
若要为应用启用 Profiler，请遵循下面的说明。 如果你运行的是其他类型的 Azure 服务，以下是在其他受支持的平台上启用探查器的说明：
* [云服务](../../azure-monitor/app/profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 应用程序](../../azure-monitor/app/profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [虚拟机](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler 作为应用服务运行时的一部分预安装。 下面的步骤将演示如何为应用服务启用此服务。 即使已在生成时将 App Insights SDK 包含在应用程序中，也请执行这些步骤。

1. 为应用服务启用 "Always On" 设置。 可以在应用服务的 "配置" 页中的 "常规设置" 下更新设置。
1. 转到 Azure 门户中的“应用服务”窗格。
1. 导航到“设置”>“Application Insights”窗格。

   ![在应用服务门户上启用 App Insights](./media/profiler/AppInsights-AppServices.png)

1. 按窗格中的说明创建新资源，或者选择现有的 App Insights 资源，以便监视应用。 此外，请确保 Profiler 已启用。 如果 Application Insights 资源与应用服务位于不同的订阅中，则不能使用此页来配置 Application Insights。 不过，你仍然可以手动创建必要的应用设置。 [下一节包含有关手动启用探查器的说明。](#enable-profiler-manually-or-with-azure-resource-manager) 

   ![添加 App Insights 站点扩展][Enablement UI]

1. 现已使用应用服务应用设置启用了 Profiler。

    ![Profiler 的应用设置][profiler-app-setting]

## <a name="enable-profiler-manually-or-with-azure-resource-manager"></a>手动或使用 Azure 资源管理器启用探查器
可以通过创建 Azure App Service 的应用设置来启用 Application Insights Profiler。 上面显示的选项的页面会为你创建这些应用设置。 但你可以使用模板或其他方法自动创建这些设置。 如果 Application Insights 资源与 Azure App Service 不同的订阅，则这些设置也将起作用。
下面是启用探查器所需的设置：

|应用设置    | 值    |
|---------------|----------|
|APPINSIGHTS_INSTRUMENTATIONKEY         | Application Insights 资源的 iKey    |
|APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
|DiagnosticServices_EXTENSION_VERSION | ~ 3 |


可以使用[azure 资源管理器模板](../../azure-monitor/app/azure-web-apps.md#app-service-application-settings-with-azure-resource-manager)、 [azure Powershell](https://docs.microsoft.com/powershell/module/az.websites/set-azwebapp) [Azure CLI](https://docs.microsoft.com/cli/azure/webapp/config/appsettings?view=azure-cli-latest)来设置这些值。

### <a name="enabling-profiler-for-other-clouds-manually"></a>为其他云手动启用探查器

如果要为其他云启用探查器，则可以使用以下应用设置。

|应用设置    | 美国政府值| 中国云 |   
|---------------|---------------------|-------------|
|ApplicationInsightsProfilerEndpoint         | https://agent.serviceprofiler.azure.us    | https://profiler.applicationinsights.azure.cn |
|ApplicationInsightsEndpoint | https://dc.applicationinsights.us | https://dc.applicationinsights.azure.cn |

## <a name="disable-profiler"></a>禁用 Profiler

若要为单个应用实例停止或重启 Profiler，请在“Web 作业”下转到“应用资源”。 若要删除 Profiler，请转到“扩展”。

![禁用针对 Web 作业的 Profiler][disable-profiler-webjob]

建议在所有应用上都启用 Profiler，以便尽早发现任何性能问题。

使用 WebDeploy 将更改部署到 web 应用程序时，可以删除探查器的文件。 可以通过在部署期间排除 App_Data 文件夹来防止删除。 


## <a name="next-steps"></a>后续步骤

* [在 Visual Studio 中使用 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
