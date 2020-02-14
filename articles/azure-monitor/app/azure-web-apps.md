---
title: 监视 Azure 应用服务性能 | Microsoft Docs
description: Azure 应用服务的应用程序性能监视。 图表加载和响应时间、依赖关系信息，并设置性能警报。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/11/2019
ms.openlocfilehash: 3ca9cbf2e282e3f67af3c5da470a3d81e6055f98
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189588"
---
# <a name="monitor-azure-app-service-performance"></a>监视 Azure 应用服务性能

现在比以往更轻松地在[Azure 应用 Services](https://docs.microsoft.com/azure/app-service/)上的 ASP.NET 和基于 ASP.NET Core 的 web 应用程序上启用监视。 尽管以前需要手动安装站点扩展，但在默认情况下，最新的扩展/代理现在内置于应用服务映像中。 本文将指导你启用 Application Insights 监视，并提供有关自动执行大规模部署过程的初步指导。

> [!NOTE]
> 不推荐使用**开发工具** > **扩展**来手动添加 Application Insights 站点扩展。 此扩展安装方法依赖于每个新版本的手动更新。 此扩展的最新稳定版本现在[预安装](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)为应用服务映像的一部分。 文件位于 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` 中，并随每个稳定版本自动更新。 如果按照基于代理的说明来启用下面的监视，它将自动删除不推荐使用的扩展。

## <a name="enable-application-insights"></a>启用 Application Insights

可以通过两种方式为 Azure 应用 Services 托管的应用程序启用应用程序监视：

* **基于代理的应用程序监视**（ApplicationInsightsAgent）。  
    * 此方法是最容易启用的，无需高级配置。 它通常称为 "运行时" 监视。 对于 Azure 应用服务，我们建议你至少启用此级别的监视，然后根据你的具体方案，通过手动检测来评估是否需要更高级的监视。

* 通过在代码中安装 Application Insights SDK，**手动检测应用程序**。

    * 此方法的可自定义性更高，但它需要[在 APPLICATION INSIGHTS SDK NuGet 包上添加依赖项](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 此方法还意味着您必须自行管理包的最新版本的更新。

    * 如果需要进行自定义 API 调用以跟踪默认情况下未与基于代理的监视一起捕获的事件/依赖关系，则需要使用此方法。 有关详细信息，请参阅 API，了解[自定义事件和指标](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)。

> [!NOTE]
> 如果同时检测基于代理的监视和基于 SDK 的手动检测，则只会遵守手动检测设置。 这是为了防止发送重复的数据。 若要了解详细信息，请查看下面的[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>启用基于代理的监视

# <a name="nettabnet"></a>[.NET](#tab/net)

> [!NOTE]
> 不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 Urlcompression> 的组合。 有关详细信息，请参阅[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)中的说明。


1. 在应用服务的 Azure 控制面板中，选择“Application Insights”。

    ![在“设置”下选择“Application Insights”](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

     > [!NOTE]
     > 当单击“确定”来创建新资源时，将提示你**应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会**触发应用服务的重新启动**。 

     ![检测 Web 应用](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 默认情况下，ASP.NET 应用监视具有两个不同的集合级别。

    ![根据平台选择选项](./media/azure-web-apps/choose-options-new.png)

   * .NET 基本集合级别提供基本的单实例 APM 功能。

   * .NET 建议集合级别：
       * 添加 CPU、内存和 I/O 使用情况趋势。
       * 跨请求/依赖关系边界关联微服务。
       * 收集使用情况趋势，并启用从可用性结果到事务的关联。
       * 收集未经主机进程处理的异常。
       * 提高使用采样时，负载下的 APM 指标准确性。

3. 若要配置采样等设置，你之前可以通过 applicationinsights.config 文件进行控制，现在可以通过应用程序设置和相应的前缀与这些相同的设置进行交互。 

    * 例如，若要更改初始采样百分比，可以创建的应用程序设置为： `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` 和值 `100`。

    * 有关支持的自适应采样遥测处理器设置的列表，可以查阅[代码](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs)和[相关文档](https://docs.microsoft.com/azure/azure-monitor/app/sampling)。

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

支持以下版本的 .NET Core： ASP.NET Core 2.0、ASP.NET Core 2.1、ASP.NET Core 2.2 ASP.NET Core 3。0

当前**不支持**基于 .net Core、自包含部署和基于 Linux 的应用程序的完整框架。 （通过代码进行的[手动检测](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)将适用于上述所有情况。）

1. 在应用服务的 Azure 控制面板中，选择“Application Insights”。

    ![在“设置”下选择“Application Insights”](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

     > [!NOTE]
     > 当单击“确定”来创建新资源时，将提示你**应用监视设置**。 选择“继续”会将新的 Application Insights 资源链接到你的应用服务，这样做还会**触发应用服务的重新启动**。 

     ![检测 Web 应用](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用的资源后，可以选择希望 Application Insights 如何为应用程序收集每个平台的数据。 .NET Core 为 .NET Core 2.0、2.1、2.2 和3.0 提供**推荐的收集**或**禁用**。

    ![根据平台选择选项](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

从应用服务 web 应用中的 "**设置**" > **选择 "Application Insights** " > **启用**"。 基于 node.js 代理的监视目前处于预览阶段。

# <a name="javatabjava"></a>[Java](#tab/java)

基于 Java 应用服务的 web 应用程序当前不支持基于自动代理/扩展的监视。 若要为 Java 应用程序启用监视，需要[手动检测你的应用程序](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)。

# <a name="pythontabpython"></a>[Python](#tab/python)

基于 Python 应用服务的 web 应用程序当前不支持基于自动代理/扩展的监视。 若要为你的 Python 应用程序启用监视，需要[手动检测你的应用程序](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)。

---

## <a name="enable-client-side-monitoring"></a>启用客户端监视

# <a name="nettabnet"></a>[.NET](#tab/net)

客户端监视适用于 ASP.NET。 启用客户端监视：

* 选择 "**设置**" > "应用程序设置" * * * *
   * 在 "应用程序设置" 下，添加新的**应用程序设置名称**和**值**：

     名称：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值： `true`

   * **保存**设置并**重新启动**应用。

![应用程序设置 UI 的屏幕截图](./media/azure-web-apps/appinsights-javascript-enabled.png)

若要禁用客户端监视，请从应用程序设置中删除关联的键值对，或将值设置为 false。

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

**默认情况**下，使用**建议集合**为 .net Core 应用启用客户端监视，而不考虑应用设置 "APPINSIGHTS_JAVASCRIPT_ENABLED" 是否存在。

如果出于某种原因要禁用客户端监视，请执行以下操作：

* 选择**设置** > **应用程序设置**
   * 在 "应用程序设置" 下，添加新的**应用程序设置名称**和**值**：

     名称： `APPINSIGHTS_JAVASCRIPT_ENABLED`

     值： `false`

   * **保存**设置并**重新启动**应用。

![应用程序设置 UI 的屏幕截图](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejstabnodejs"></a>[Node.js](#tab/nodejs)

若要为 node.js 应用程序启用客户端监视，需要[手动将客户端 JAVASCRIPT SDK 添加到应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="javatabjava"></a>[Java](#tab/java)

若要为 Java 应用程序启用客户端监视，需要[手动将客户端 JAVASCRIPT SDK 添加到应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="pythontabpython"></a>[Python](#tab/python)

若要为你的 Python 应用程序启用客户端监视，你需要[手动将客户端 JAVASCRIPT SDK 添加到你的应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

---

## <a name="automate-monitoring"></a>自动监视

若要启用 Application Insights 的遥测收集，只需设置应用程序设置：

   ![具有可用 Application Insights 设置的应用服务应用程序设置](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  Definition | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 主扩展，控制运行时监视。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  仅在默认模式下，启用了基本功能，以便确保最佳性能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制是否将启用二进制重写引擎 `InstrumentationEngine`。 此设置会影响性能，并影响冷启动/启动时间。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 控制是否将捕获 SQL & Azure 表文本以及依赖项调用。 性能警告：应用程序冷启动时间将会受到影响。 此设置需要 `InstrumentationEngine`。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure 资源管理器的应用服务应用程序设置

可以通过[Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)来管理和配置应用服务的应用程序设置。 使用 Azure 资源管理器自动化部署新的应用服务资源或修改现有资源的设置时，可以使用此方法。

应用服务的应用程序设置 JSON 的基本结构如下：

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

有关为 Application Insights 配置应用程序设置的 Azure 资源管理器模板的示例，此[模板](https://github.com/Andrew-MSFT/BasicImageGallery)可能很有用，特别是从[第238行](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)开始的部分。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自动创建 Application Insights 资源，并链接到新创建的应用服务。

若要创建配置了所有默认 Application Insights 设置的 Azure 资源管理器模板，请开始此过程，就如同在启用 Application Insights 的情况下创建新的 Web 应用。

选择**自动化选项**

   ![应用服务 web 应用创建菜单](./media/azure-web-apps/create-web-app.png)

此选项生成最新的 Azure 资源管理器模板，并配置所有所需的设置。

  ![应用服务 web 应用模板](./media/azure-web-apps/arm-template.png)

下面是一个示例，将 `AppMonitoredSite` 的所有实例替换为你的站点名称：

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>通过 PowerShell 启用

为了通过 PowerShell 启用应用程序监视，只需更改底层应用程序设置。 下面是一个示例，它为资源组 "AppMonitoredRG" 中名为 "AppMonitoredSite" 的网站启用应用程序监视，并配置要发送到 "012345678-abcd-ef01-2345-6789abcd" 检测密钥的数据。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>升级监视扩展/代理

### <a name="upgrading-from-versions-289-and-up"></a>从2.8.9 和更高版本升级

无需执行任何其他操作，就会自动从版本2.8.9 升级。 新的监视位是在后台向目标应用服务提供的，在应用程序重新启动时，会选取它们。

若要查看正在运行的扩展的版本，请访问 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Url 路径 http://yoursitename.scm.azurewebsites.net/ApplicationInsights 屏幕截图](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>从1.0.0 版升级-2.6。5

从版本2.8.9 开始，使用预安装的站点扩展。 如果你是早期版本，则可以通过以下两种方式之一进行更新：

* [通过门户启用来升级](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)。 （即使已安装 Azure App Service 的 Application Insights 扩展，UI 也只显示 "**启用**" 按钮。 在后台，将删除旧的专用站点扩展。）

* [通过 PowerShell 升级](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)：

    1. 设置应用程序设置，以启用预安装的站点扩展 ApplicationInsightsAgent。 请参阅[通过 Powershell 启用](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)。
    2. 手动删除 Azure App Service 的名为 Application Insights 扩展的专用站点扩展。

如果从2.5.1 之前的版本进行升级，请检查是否已从应用程序 bin 文件夹中删除 ApplicationInsigths dll，[请参阅故障排除步骤](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="troubleshooting"></a>故障排除

下面是针对在 Azure 应用服务上运行的基于 .NET 和 .NET Core 的应用程序的基于扩展/代理监视的分步疑难解答指南。

> [!NOTE]
> Java 应用程序仅支持通过基于手动 SDK 的检测 Azure 应用服务，因此以下步骤不适用于这些方案。

1. 通过 `ApplicationInsightsAgent`检查是否监视了应用程序。
    * 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值 "~ 2"。
2. 确保应用程序满足要监视的要求。
    * 浏览到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![https://yoursitename.scm.azurewebsites/applicationinsights 结果页的屏幕截图](./media/azure-web-apps/app-insights-sdk-status.png)

    * 确认 `Application Insights Extension Status` `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 如果未运行，请按照[启用 Application Insights 监视说明操作](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 确认状态源是否存在，如下所示： `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 如果不存在相似的值，则表示应用程序当前未运行或不受支持。 若要确保应用程序正在运行，请尝试手动访问应用程序 url/应用程序终结点，这将允许运行时信息变得可用。

    * 确认 `IKeyExists` `true`
        * 如果 `false`，请将 ikey guid `APPINSIGHTS_INSTRUMENTATIONKEY` 和 `APPLICATIONINSIGHTS_CONNECTION_STRING` 添加到应用程序设置。

    * 确认没有任何条目用于 `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly`和 `AppContainsAspNetTelemetryCorrelationAssembly`。
        * 如果其中有任何项存在，请从应用程序中删除以下包： `Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource`和 `Microsoft.AspNet.TelemetryCorrelation`。

下表提供了有关这些值的含义、其根本原因和推荐修补程序的更详细说明：

|问题值|说明|修复
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值指示该扩展已检测到 SDK 的某个方面已经存在于应用程序中，并将进行回退。 这可能是由于引用 `System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation`或 `Microsoft.ApplicationInsights`  | 删除引用。 某些 Visual studio 模板默认情况下会添加其中的某些引用，较旧版本的 Visual Studio 可能会添加对 `Microsoft.ApplicationInsights`的引用。
|`AppAlreadyInstrumented:true` | 如果应用程序面向 .NET Core 2.1 或2.2，并引用[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ，则会将其引入 Application Insights，扩展将会关闭。 | [建议](https://github.com/aspnet/Announcements/issues/287)使用 .net Core 2.1、2.2 的客户改为使用 AspNetCore 元包。|
|`AppAlreadyInstrumented:true` | 此值还可能由以前部署的应用程序文件夹中存在以上 dll 引起。 | 清除应用文件夹以确保删除这些 dll。 检查本地应用程序的 bin 目录和应用服务上的 wwwroot 目录。 （若要检查应用服务 web 应用的 wwwroot 目录：高级工具（Kudu） > 调试控制台 > CMD > home\site\wwwroot）。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 此值指示扩展检测到对应用程序中的 `Microsoft.AspNet.TelemetryCorrelation` 的引用，并且将会进行回退。 | 删除引用。
|`AppContainsDiagnosticSourceAssembly**:true`|此值指示扩展检测到对应用程序中的 `System.Diagnostics.DiagnosticSource` 的引用，并且将会进行回退。| 删除引用。
|`IKeyExists:false`|此值指示 AppSetting 中不存在检测密钥，`APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：值可能已被意外删除，忘记了在自动化脚本中设置值，等等。 | 请确保应用服务应用程序设置中存在该设置。

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 Urlcompression>

如果在对内容进行编码的情况下使用 APPINSIGHTS_JAVASCRIPT_ENABLED = true，则可能会收到类似于以下内容的错误： 

- 500 URL 重写错误
- 500.53 URL 重写模块错误，消息出站重写规则在编码 HTTP 响应的内容时无法应用（"gzip"）。 

这是因为 APPINSIGHTS_JAVASCRIPT_ENABLED 应用程序设置设置为 true，同时同时提供内容编码。 目前尚不支持此方案。 解决方法是从应用程序设置中删除 APPINSIGHTS_JAVASCRIPT_ENABLED。 遗憾的是，如果仍需要客户端/浏览器的 JavaScript 规范，则网页需要手动 SDK 引用。 请按照 JavaScript SDK 的手动检测的[说明](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)进行操作。

有关 Application Insights 代理/扩展的最新信息，请查看[发行说明](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)。

## <a name="next-steps"></a>后续步骤
* [在实时应用上运行探查器](../app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 监视 Azure Functions
* [将 Azure 诊断配置为](../platform/diagnostics-extension-to-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../platform/data-platform.md)以确保服务可用且做出快速响应。
* 每当操作事件发生或指标超过阈值时[接收警报通知](../platform/alerts-overview.md)。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](javascript.md)。
* [设置可用性 Web 测试](monitor-web-app-availability.md)，以便在站点关闭时发出警报。
