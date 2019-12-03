---
title: 新建 Azure Application Insights 资源 | Microsoft Docs
description: 为新的实时应用程序手动设置 Application Insights 监视。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/02/2019
ms.openlocfilehash: 11b581649e6d7a048899394d69e574e8f9a4d22c
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689218"
---
# <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

Azure Application Insights 在 Microsoft Azure *资源*中显示有关应用程序的数据。 因此创建新资源是[设置监视新应用程序 Application Insights][start]的一部分。 创建新资源后，可以获取其检测密钥，并使用它来配置 Application Insights SDK。 检测密钥会将遥测数据链接到资源。

## <a name="sign-in-to-microsoft-azure"></a>登录到 Microsoft Azure

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="create-an-application-insights-resource"></a>创建 Application Insights 资源

登录到[Azure 门户](https://portal.azure.com)，并创建 Application Insights 资源：

![单击左上角的 "+" 号。 选择开发人员工具后跟 Application Insights](./media/create-new-resource/new-app-insights.png)

   | 设置        |  Value           | 描述  |
   | ------------- |:-------------|:-----|
   | 名称      | 唯一值 | 标识要监视的应用程序的名称。 |
   | **资源组**     | myResourceGroup      | 新的或现有资源组的名称以托管 App Insights 数据。 |
   | 位置 | 美国东部 | 选择附近的位置，或接近应用程序的托管位置。 |

> [!NOTE]
> 虽然你可以在不同的资源组中使用相同的资源名称，但使用全局唯一名称可能会很有用。 如果你计划[执行跨资源查询](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application)，因为它可以简化所需的语法，这会很有用。

在必填字段中输入适当的值，然后选择 "**查看 + 创建**"。

![在必填字段中输入值，然后选择 "查看 + 创建"。](./media/create-new-resource/review-create.png)

创建应用后，将打开一个新窗格。 在此窗格中可以查看有关所监视的应用程序的性能和使用情况数据。 

## <a name="copy-the-instrumentation-key"></a>复制检测密钥

检测密钥用于标识要与遥测数据关联的资源。 需要将检测密钥添加到应用程序的代码中。

![单击并复制检测密钥](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>在应用中安装 SDK

在应用中安装 Application Insights SDK。 此步骤在很大程度上依赖于应用程序的类型。

使用检测密钥来配置在[应用程序中安装的 SDK][start]。

SDK 包括发送遥测数据的标准模块，无需编写任何其他代码。 若要更详细地跟踪用户操作或诊断问题，请[使用 API][api]发送自己的遥测数据。

## <a name="creating-a-resource-automatically"></a>自动创建资源

### <a name="powershell"></a>PowerShell

新建 Application Insights 资源

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>示例

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>结果

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

有关此 cmdlet 的完整 PowerShell 文档，以及如何检索检测密钥的详细说明，请参阅[Azure PowerShell 文档](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0)。

### <a name="azure-cli-preview"></a>Azure CLI （预览）

若要访问预览版 Application Insights 需要首先运行 Azure CLI 命令：

```azurecli
 az extension add -n application-insights
```

如果不运行 `az extension add` 命令，将看到一条错误消息，指出： `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

现在，可以运行以下内容来创建 Application Insights 资源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>示例

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>结果

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

有关此命令的完整 Azure CLI 文档，以及如何检索检测密钥的详细说明，请参阅[Azure CLI 文档](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)。

## <a name="next-steps"></a>后续步骤
* [诊断搜索](../../azure-monitor/app/diagnostic-search.md)
* [探索指标](../../azure-monitor/app/metrics-explorer.md)
* [编写分析查询](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md