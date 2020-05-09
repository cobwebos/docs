---
title: 监视 Azure 应用服务性能 | Microsoft Docs
description: Azure 应用服务的应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 0f4d4dedab30839db56cb47ac7ac103413f2d4be
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733436"
---
# <a name="monitor-azure-app-service-performance"></a>监视 Azure 应用服务性能

现在，可以比过去更轻松地针对 [Azure 应用服务](https://docs.microsoft.com/azure/app-service/)中运行的基于 ASP.NET 和 ASP.NET Core 的 Web 应用程序启用监视。 以前需要手动安装某个站点扩展，而现在应用服务映像中默认会内置最新的扩展/代理。 本文逐步讲解如何启用 Application Insights 监视，并提供有关如何自动完成大规模部署的初步指导。

> [!NOTE]
> 通过“开发工具” > “扩展”手动添加 Application Insights 站点扩展的功能已弃用。   此扩展安装方法依赖于每个新版本的手动更新。 扩展的最新稳定版现在会[预装](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)在应用服务映像中。 这些文件位于 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` 中，每发布一个稳定版本，它们都会自动更新。 如果在下文中遵循基于代理的说明启用监视，系统会自动删除已弃用的扩展。

## <a name="enable-application-insights"></a>启用 Application Insights

可通过两种方法为 Azure 应用服务托管的应用程序启用应用程序监视：

* **基于代理的应用程序监视** (ApplicationInsightsAgent)。  
    * 这是启用监视的最简单方法，无需完成任何高级配置。 这种监视通常称为“运行时”监视。 对于 Azure 应用服务，我们建议至少启用此级别的监视，然后可根据具体的方案，评估是否需要通过手动检测来启用更高级的监视。

* 安装 Application Insights SDK 以**通过代码手动检测应用程序**。

    * 此方法的可自定义性要高得多，但需要[添加 Application Insights SDK NuGet 包中的一个依赖项](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 使用此方法还需要自行管理对最新版本的包的更新。

    * 如果需要发出自定义 API 调用来跟踪基于代理的监视在默认情况下不会捕获的事件/依赖项，则需要使用此方法。 有关详细信息，请查看 [自定义事件和指标的 API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 一文。 对于基于 Linux 的工作负载，这也是当前唯一支持的选项。

> [!NOTE]
> 如果同时检测基于代理的监视和基于 SDK 的手动检测，则只会遵守手动检测设置。 这是为了防止发送重复数据。 若要了解详细信息，请查看下面的[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="enable-agent-based-monitoring"></a>启用基于代理的监视

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> 不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 Urlcompression> 的组合。 有关详细信息，请参阅[故障排除部分](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)中的说明。


1. 在应用服务的 Azure 控制面板中**选择 "Application Insights** "。

    ![在“设置”下选择“Application Insights”](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

     > [!NOTE]
     > 当单击“确定”**** 来创建新资源时，将提示你**应用监视设置**。 选择“继续”**** 会将新的 Application Insights 资源链接到你的应用服务，这样做还会**触发应用服务的重新启动**。 

     ![检测 Web 应用](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 ASP.NET 应用监视默认已启用，它提供两种不同级别的集合。

    ![根据平台选择选项](./media/azure-web-apps/choose-options-new.png)
 
 下面是针对每个路由收集的数据的摘要：
        
|  | .NET 基本集合 | .NET 推荐的集合 |
| --- | --- | --- |
| 添加 CPU、内存和 I/O 使用情况趋势 |是 |是 |
| 收集使用情况趋势，并启用从可用性结果到事务的关联 | 是 |是 |
| 收集未经主机进程处理的异常 | 是 |是 |
| 提高使用采样时，负载下的 APM 指标准确性 | 是 |是 |
| 跨请求/依赖项边界关联微服务 | 否（仅单实例 APM 功能） |是 |

3. 若要配置采样等设置（以前可以通过 applicationinsights.config 文件进行控制），现在可以通过应用程序设置使用相应的前缀来与这些设置交互。 

    * 例如，若要更改初始采样百分比，可以创建名为 `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage`、值为 `100` 的应用程序设置。

    * 有关受支持自适应采样遥测处理器设置的列表，可以参阅[代码](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs)和[相关的文档](https://docs.microsoft.com/azure/azure-monitor/app/sampling)。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

支持以下版本的 .NET Core： ASP.NET Core 2.0、ASP.NET Core 2.1、ASP.NET Core 2.2 ASP.NET Core 3。0

基于代理/扩展的监视目前**不支持**将 .NET Core 提供的完整框架、独立部署和基于 Linux 的应用程序作为目标。 （在上述所有方案中，都可通过代码进行[手动检测](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)。）

1. 在应用服务的 Azure 控制面板中**选择 "Application Insights** "。

    ![在“设置”下选择“Application Insights”](./media/azure-web-apps/settings-app-insights-01.png)

   * 除非已为此应用设置了 Application Insights 资源，否则请选择创建新资源。 

     > [!NOTE]
     > 当单击“确定”**** 来创建新资源时，将提示你**应用监视设置**。 选择“继续”**** 会将新的 Application Insights 资源链接到你的应用服务，这样做还会**触发应用服务的重新启动**。 

     ![检测 Web 应用](./media/azure-web-apps/create-resource-01.png)

2. 指定要使用哪些资源后，可以选择 Application Insights 根据平台为应用程序收集数据的方式。 对于 .NET Core 2.0、2.1、2.2 和 3.0，.NET Core 提供“建议的集合”或“已禁用”。********

    ![根据平台选择选项](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

从应用服务 web 应用中的 "**设置** > " 下**选择 "Application Insights** > **启用**"。 基于 Node.js 代理的监视目前为预览版。

# <a name="java"></a>[Java](#tab/java)

基于 Java 应用服务的 Web 应用程序当前不支持基于自动代理/扩展的监视。 若要针对 Java 应用程序启用监视，需[手动检测应用程序](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)。

# <a name="python"></a>[Python](#tab/python)

基于 Python 应用服务的 Web 应用程序当前不支持基于自动代理/扩展的监视。 若要针对 Python 应用程序启用监视，需[手动检测应用程序](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)。

---

## <a name="enable-client-side-monitoring"></a>启用客户端监视

# <a name="net"></a>[.NET](#tab/net)

可以选择为 ASP.NET 启用客户端监视。 若要启用客户端监视：

* 选择“设置”>“应用程序设置”。****
   * 在“应用程序设置”下，添加新的**应用设置名称**和**值**：

     名称：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值：`true`

   * **保存**设置并**重新启动**应用。

![应用程序设置 UI 的屏幕截图](./media/azure-web-apps/appinsights-javascript-enabled.png)

若要禁用客户端监视，请从“应用程序设置”中删除关联的键值对，或将值设置为 false。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

对于使用“建议的集合”的 .NET Core 应用，**默认已启用**客户端监视，无论是否存在应用设置“APPINSIGHTS_JAVASCRIPT_ENABLED”。****

如果出于某种原因想要禁用客户端监视：

* 选择**设置** > **应用程序设置**
   * 在“应用程序设置”下，添加新的**应用设置名称**和**值**：

     名称：`APPINSIGHTS_JAVASCRIPT_ENABLED`

     值：`false`

   * **保存**设置并**重新启动**应用。

![应用程序设置 UI 的屏幕截图](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

若要针对 Node.js 应用程序启用客户端监视，需[手动将客户端 JavaScript SDK 添加到应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="java"></a>[Java](#tab/java)

若要针对 Java 应用程序启用客户端监视，需[手动将客户端 JavaScript SDK 添加到应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

# <a name="python"></a>[Python](#tab/python)

若要针对 Python 应用程序启用客户端监视，需[手动将客户端 JavaScript SDK 添加到应用程序](https://docs.microsoft.com/azure/azure-monitor/app/javascript)。

---

## <a name="automate-monitoring"></a>自动监视

若要在 Application Insights 中启用遥测数据收集，只需设置“应用程序设置”：

   ![应用服务应用程序设置和可用的 Application Insights 设置](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>应用程序设置定义

|应用设置名称 |  定义 | 值 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 用于控制运行时监视的主扩展。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  （仅限默认模式）已启用基本功能以确保最佳性能。 | `default` 或 `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | 控制是否要启用二进制重写引擎 `InstrumentationEngine`。 此设置会对性能以及冷启动/启动时间造成影响。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | 控制是否要随依赖项调用一起捕获 SQL 和 Azure 表文本。 性能警告：应用程序冷启动时间将会受到影响。 此设置需要`InstrumentationEngine`。 | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>使用 Azure 资源管理器配置应用服务应用程序设置

可以使用 [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)来管理和配置应用服务的应用程序设置。 使用 Azure 资源管理器自动化部署新的应用服务资源或修改现有资源的设置时，可以使用此方法。

下面是应用服务的应用程序设置 JSON 基本结构：

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

此[模板](https://github.com/Andrew-MSFT/BasicImageGallery)可帮助使用为 Application Insights 配置的应用程序设置创建一个 Azure 资源管理器模板示例，具体而言，从[第 238 行](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)开始的节用于创建此模板。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>自动创建 Application Insights 资源并链接到新建的应用服务。

若要使用配置的所有默认 Application Insights 设置创建 Azure 资源管理器模板，请像在启用了 Application Insights 的情况下创建新的 Web 应用时一样开始操作。

选择“自动化选项”****

   ![应用服务 Web 应用创建菜单](./media/azure-web-apps/create-web-app.png)

此选项将使用配置的全部所需设置生成最新的 Azure 资源管理器模板。

  ![应用服务 Web 应用模板](./media/azure-web-apps/arm-template.png)

下面是一个示例，请将 `AppMonitoredSite` 的所有实例替换为你的站点名称：

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

若要通过 PowerShell 启用应用程序监视，只需更改基础的应用程序设置。 以下示例为资源组“AppMonitoredRG”中名为“AppMonitoredSite”的网站启用应用程序监视，并将数据配置为发送到“012345678-abcd-ef01-2345-6789abcd”检测密钥。

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

### <a name="upgrading-from-versions-289-and-up"></a>从版本 2.8.9 及更高版本升级

从版本 2.8.9 的升级会自动发生，无需额外的操作。 新的监视位将在后台传送到目标应用服务，应用程序重启时，会拾取这些位。

若要检查正在运行的扩展版本，请访问 `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![URL 路径 http://yoursitename.scm.azurewebsites.net/ApplicationInsights 的屏幕截图](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>从版本 1.0.0 - 2.6.5 升级

从版本 2.8.9 开始，将使用预装的站点扩展。 如果使用更低的版本，可通过两种方法之一进行更新：

* [通过门户启用扩展进行升级](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)。 （即使已安装 Azure 应用服务的 Application Insights 扩展，UI 也只会显示“启用”按钮。**** 在幕后，旧的专用站点扩展将被删除。）

* [通过 PowerShell 升级](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)：

    1. 设置应用程序设置以启用预装的站点扩展 ApplicationInsightsAgent。 请参阅[通过 PowerShell 启用](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)。
    2. 手动删除名为“Azure 应用服务的 Application Insights 扩展”的专用站点扩展。

如果已从低于 2.5.1 的版本完成升级，请检查是否已从应用程序 bin 文件夹中删除了 ApplicationInsigths dll。[参阅故障排除步骤](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)。

## <a name="troubleshooting"></a>疑难解答

下面是我们针对 Azure 应用服务中运行的基于 .NET 和 .NET Core 的应用程序的基于扩展/代理的监视提供的分步故障排除指南。

> [!NOTE]
> 仅支持通过基于手动 SDK 的检测在 Azure 应用服务中使用 Java 应用程序，因此，以下步骤不适用于这些方案。

1. 通过 `ApplicationInsightsAgent` 检查应用程序是否受监视。
    * 检查 `ApplicationInsightsAgent_EXTENSION_VERSION` 应用设置是否设置为值“~2”。
2. 确保应用程序符合监视要求。
    * 浏览到 `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`。

    ![https://yoursitename.scm.azurewebsites/applicationinsights 结果页的屏幕截图](./media/azure-web-apps/app-insights-sdk-status.png)

    * 确认 `Application Insights Extension Status` 为 `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * 如果未运行，请遵照[启用 Application Insights 监视的说明](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * 确认状态源存在并类似于：`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 如果不存在类似的值，则表示应用程序当前未运行或不受支持。 为确保应用程序运行，请尝试手动访问应用程序 URL/应用程序终结点，以提供运行时信息。

    * 确认 `IKeyExists` 为 `true`
        * 如果是`false`，请将`APPINSIGHTS_INSTRUMENTATIONKEY`并`APPLICATIONINSIGHTS_CONNECTION_STRING`将 ikey guid 添加到应用程序设置。

    * 确认 `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly` 和 `AppContainsAspNetTelemetryCorrelationAssembly` 没有任何对应的条目。
        * 如果存在其中的任何条目，请从应用程序中删除以下包：`Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource` 和 `Microsoft.AspNet.TelemetryCorrelation`。

下表更详细地解释了这些值的含义、其根本原因和建议的修复方法：

|问题值|说明|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | 此值表示扩展已检测到 SDK 的某个功能已在应用程序中存在，因此将会回退。 原因可能是引用了 `System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation` 或 `Microsoft.ApplicationInsights`  | 删除引用。 某中的某些引用是从特定的 Visual Studio 模板默认添加的，而旧版 Visual Studio 可能会添加对 `Microsoft.ApplicationInsights` 的引用。
|`AppAlreadyInstrumented:true` | 如果应用程序面向 .NET Core 2.1 或 2.2 并引用 [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) 元包，则它会引入 Application Insights，因此扩展将会回退。 | [建议](https://github.com/aspnet/Announcements/issues/287) .NET Core 2.1、2.2 客户改用 Microsoft.AspNetCore.App 元包。|
|`AppAlreadyInstrumented:true` | 出现此值的原因还可能是前一部署的应用文件夹中存在上述 dll。 | 清除应用文件夹，以确保删除这些 dll。 检查本地应用程序的 bin 目录和应用服务上的 wwwroot 目录。 （若要检查应用服务 web 应用的 wwwroot 目录：高级工具（Kudu） > 调试控制台 > CMD > home\site\wwwroot）。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | 此值表示扩展已检测到对应用程序中的 `Microsoft.AspNet.TelemetryCorrelation` 的引用，因此将会回退。 | 删除引用。
|`AppContainsDiagnosticSourceAssembly**:true`|此值表示扩展已检测到对应用程序中的 `System.Diagnostics.DiagnosticSource` 的引用，因此将会回退。| 删除引用。
|`IKeyExists:false`|此值表示 AppSetting 中不存在检测密钥 `APPINSIGHTS_INSTRUMENTATIONKEY`。 可能的原因：值可能已被意外删除，忘记了在自动化脚本中设置值，等等。 | 确保该设置在应用服务的应用程序设置中存在。

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>不支持 APPINSIGHTS_JAVASCRIPT_ENABLED 和 urlCompression

如果在对内容进行编码的情况下使用 APPINSIGHTS_JAVASCRIPT_ENABLED=true，可能会出现如下所示的错误： 

- 500 URL 重写错误
- 发生 500.53 URL 重写模块错误，并出现消息“对 HTTP 响应的内容进行编码(gzip)时，无法应用出站重写规则”。 

这是因为 APPINSIGHTS_JAVASCRIPT_ENABLED 应用程序设置指定为 true，同时存在内容编码。 目前不支持此方案。 解决方法是从应用程序设置中删除 APPINSIGHTS_JAVASCRIPT_ENABLED。 遗憾的是，这意味着，如果仍然需要客户端/浏览器端 JavaScript 检测，则需要对网页使用手动 SDK 引用。 请遵照[说明](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)使用 JavaScript SDK 进行手动检测。

有关 Application Insights 代理/扩展的最新信息，请查看[发行说明](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)。

### <a name="php-and-wordpress-are-not-supported"></a>不支持 PHP 和 WordPress

不支持 PHP 和 WordPress 站点。 对于这些工作负荷的服务器端监视，当前没有正式支持的 SDK/代理。 但是，通过将客户端 javascript 添加到网页，可以使用[JAVASCRIPT SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)在 PHP 或 WordPress 站点上手动检测客户端事务。

### <a name="connection-string-and-instrumentation-key"></a>连接字符串和检测密钥

如果正在使用无代码置备监视，则只需要连接字符串。 但是，我们仍建议在执行手动检测时设置检测密钥，以保持与早期版本的 SDK 之间的向后兼容性。

## <a name="next-steps"></a>后续步骤
* [在实时应用上运行探查器](../app/profiler.md)。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - 使用 Application Insights 监视 Azure Functions
* [将 Azure 诊断配置为](../platform/diagnostics-extension-to-application-insights.md)向 Application Insights 发送数据。
* [监视服务运行状况指标](../platform/data-platform.md)以确保服务可用且做出快速响应。
* 每当操作事件发生或指标超过阈值时[接收警报通知](../platform/alerts-overview.md)。
* 若要从访问网页的浏览器获取客户端遥测数据，请使用[适用于 JavaScript 应用和网页的 Application Insights](javascript.md)。
* [设置可用性 Web 测试](monitor-web-app-availability.md)，以便在站点关闭时发出警报。
