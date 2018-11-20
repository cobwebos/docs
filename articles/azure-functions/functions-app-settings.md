---
title: Azure Functions 的应用设置参考
description: 有关 Azure Functions 应用设置或环境变量的参考文档。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 2eb736891b12c07441bc8828ca07dd0b9fa13d98
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458116"
---
# <a name="app-settings-reference-for-azure-functions"></a>Azure Functions 的应用设置参考

函数应用中的应用设置包含对该函数应用的所有函数产生影响的全局配置选项。 在本地运行时，这些设置出现在[环境变量](functions-run-local.md#local-settings-file)中。 本文列出可在函数应用中使用的应用设置。

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

[host.json](functions-host-json.md) 文件和 [local.settings.json](functions-run-local.md#local-settings-file) 文件中提供了其他全局配置选项。

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

使用 Application Insights 时的 Application Insights 检测密钥。 请参阅[监视 Azure Functions](functions-monitoring.md)。

|密钥|示例值|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

用于存储日志并在门户上的“监视”选项卡中显示这些日志的可选存储帐户连接字符串。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

|密钥|示例值|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> 有关性能和体验，建议使用 APPINSIGHTS_INSTRUMENTATIONKEY 和 App Insights 进行监视，而不使用 AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` 表示禁用针对函数应用根 URL 显示的默认登录页。 默认为 `false`。

|密钥|示例值|
|---|------------|
|AzureWebJobsDisableHomepage|true|

如果省略此应用设置或将其设置为 `false`，则会显示类似于以下示例的页来响应 URL `<functionappname>.azurewebsites.net`。

![函数应用登录页](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` 表示在编译 .NET 代码时使用“发布”模式；`false` 表示使用“调试”模式。 默认为 `true`。

|密钥|示例值|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

要启用的 beta 功能的逗号分隔列表。 由这些标志启用的 Beta 功能尚未准备好用于生产，但在发布这些功能之前可针对试验目的启用这些功能。

|密钥|示例值|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

*host.json* 文件和函数文件夹所在的根目录的路径。 在函数应用中，默认值为 `%HOME%\site\wwwroot`。

|密钥|示例值|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

指定用于密钥存储的存储库或提供程序。 目前，支持的存储库包括 blob 存储（“Blob”）和本地文件系统（“Files”）。 默认是在版本 1 中使用 blob，在版本 2 中使用文件系统。 请注意，在版本 1 中，文件系统仅适用于在应用服务计划中运行的功能。

|密钥|示例值|
|---|------------|
|AzureWebJobsSecretStorageType|文件|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Azure Functions 运行时针对除 HTTP 触发的函数以外的其他所有函数使用此存储帐户连接字符串。 存储帐户必须是支持 Blob、队列和表的通用帐户。 请参阅[存储帐户](functions-infrastructure-as-code.md#storage-account)和[存储帐户要求](functions-create-function-app-portal.md#storage-account-requirements)。

|密钥|示例值|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

用于 TypeScript 的编译器的路径。 允许根据需要重写默认值。

|密钥|示例值|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

有效值为“readwrite”和“readonly”。

|密钥|示例值|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

要在此函数应用中使用的 Functions 运行时版本。 波浪符加主要版本号表示使用该主要版本的最新版本（例如“~2”）。 当同一主要版本的新版本可用时，会自动在函数应用中安装新版本。 若要让应用固定使用特定的版本，请使用完整版本号（例如“2.0.12345”）。 默认为“~2”。 `~1` 的值将应用固定到运行时的版本 1.x。

|密钥|示例值|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functionsworkerruntime"></a>FUNCTIONS\_WORKER\_RUNTIME

要在函数应用中加载的语言辅助角色运行时。  这将对应于应用程序中正在使用的语言（例如，“dotnet”）。 对于多种语言的函数，需要将它们发布到多个应用，每个应用都具有相应的辅助角色运行时值。  有效值为 `dotnet` (C#/F#)、`node` (JavaScript) 和 `java` (Java)。

|密钥|示例值|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

仅用于消耗计划。 存储函数应用代码和配置的存储帐户的连接字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#create-a-function-app)。

|密钥|示例值|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

仅用于消耗计划。 函数应用代码和配置的文件路径。 与 WEBSITE_CONTENTAZUREFILECONNECTIONSTRING 结合使用。 默认值是以函数应用名称开头的唯一字符串。 请参阅[创建函数应用](functions-infrastructure-as-code.md#create-a-function-app)。

|密钥|示例值|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

函数应用可以横向扩展到的最大实例数。 默认值为无限制。

> [!NOTE]
> 此设置是预览功能，只有在设置为值 <= 5 时才可靠

|密钥|示例值|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

默认为“8.11.1”。

|密钥|示例值|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|8.11.1|

## <a name="websiterunfrompackage"></a>WEBSITE\_RUN\_FROM\_PACKAGE

让函数应用从已装载的包文件运行。

|密钥|示例值|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

有效值是解析为部署包文件位置的 URL 或 `1`。 设置为 `1` 时，包必须位于 `d:\home\data\SitePackages` 文件夹中。 使用此设置的 zip 部署时，包将自动上传到此位置。 在预览版中，此设置名为 `WEBSITE_RUN_FROM_ZIP`。 有关详细信息，请参阅[从包文件运行函数](run-functions-from-deployment-package.md)。

## <a name="next-steps"></a>后续步骤

[了解如何更新应用设置](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[查看 host.json 文件中的全局设置](functions-host-json.md)

[查看应用服务应用的其他应用设置](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
