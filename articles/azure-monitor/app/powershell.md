---
title: 使用 PowerShell 自动化 Azure Application Insights | Microsoft Docs
description: 使用 Azure 资源管理器模板在 PowerShell 中自动创建和管理资源、警报和可用性测试。
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: fba85981f32611164c328945e45de4032ad949eb
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780481"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>使用 PowerShell 管理 Application Insights 资源

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

本文演示如何通过 Azure 资源管理自动创建和更新 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 资源。 例如，可能在生成过程中执行此操作。 除了基本的 Application Insights 资源，还可创建[可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)、设置[警报](../../azure-monitor/app/alerts.md)、设置[定价方案](pricing.md)和创建其他 Azure 资源。

创建这些资源的关键是用于 [Azure 资源管理器](../../azure-resource-manager/management/manage-resources-powershell.md) 的 JSON 模板。 基本过程如下：下载现有资源的 JSON 定义，参数化某些值（如名称），然后在需要创建新资源时运行模板。 可以将多个资源打包在一起，以便一次性创建它们，例如具有可用性测试、警报和连续导出的存储的应用监视器。 某些参数化有一些微妙之处，此处我们将进行介绍。

## <a name="one-time-setup"></a>一次性设置
如果之前尚未将 PowerShell 与 Azure 订阅结合使用：

在要运行脚本的计算机上安装 Azure PowerShell 模块：

1. 安装 [Microsoft Web 平台安装程序（v5 或更高版本）](https://www.microsoft.com/web/downloads/platform.aspx)。
2. 使用它安装 Microsoft Azure PowerShell。

除了使用资源管理器模板，还可以使用许多 [Application Insights PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)，以编程方式轻松地配置 Application Insights 资源。 通过这些 cmdlet 启用的功能包括：

* 创建和删除 Application Insights 资源
* 获取 Application Insights 资源及其属性的列表
* 创建并管理连续导出
* 创建并管理应用程序密钥
* 设置每日上限
* 设置定价计划

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>使用 PowerShell cmdlet 创建 Application Insights 资源

下面演示了如何使用 [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) cmdlet 在 Azure 的“美国东部”数据中心创建新的 Application Insights 资源：

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>使用资源管理器模板创建 Application Insights 资源

下面演示了如何使用资源管理器模板创建新的 Application Insights 资源。

### <a name="create-the-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

创建新的 .json 文件，在此示例中将它命名为 `template1.json`。 将此内容复制到其中：

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>使用资源管理器模板创建新的 Application Insights 资源

1. 在 PowerShell 中，使用 `$Connect-AzAccount` 登录到 Azure
2. 使用 `Set-AzContext "<subscription ID>"` 将上下文设置为某个订阅
2. 运行新的部署，以便创建新的 Application Insights 资源：
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` 是要创建新资源的组。
   * `-TemplateFile` 必须在自定义参数前出现。
   * `-appName` 是要创建的资源的名称。

可添加其他参数，并且可在模板的参数部分找到说明。

## <a name="get-the-instrumentation-key"></a>获取检测密钥

创建应用程序资源后，需使用检测密钥： 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

若要查看 Application Insights 资源的一系列其他属性，请使用：

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

其他属性可通过以下 cmdlet 获取：
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

请参阅此[详细文档](https://docs.microsoft.com/powershell/module/az.applicationinsights)以获取这些 cmdlet 的参数。  

## <a name="set-the-data-retention"></a>设置数据保留期

以下三种方法可通过编程方式设置 Application Insights 资源上的数据保留。

### <a name="setting-data-retention-using-a-powershell-commands"></a>使用 PowerShell 命令设置数据保留

下面是一组简单的 PowerShell 命令，用于设置 Application Insights 资源的数据保留：

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>使用 REST 设置数据保留

若要获取 Application Insights 资源的当前数据保留期，可以使用 OSS 工具 [ARMClient](https://github.com/projectkudu/ARMClient)。  （了解有关 ARMClient 的详细信息，请参阅[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)和[Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)。） 下面是使用`ARMClient`的一个示例，用于获取当前的保留期：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

用于设置保留期的命令是一个类似的 PUT：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

若要使用上面的模板将数据保留期设置为 365 天，请运行以下代码：

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>使用 PowerShell 脚本设置数据保留

也可使用以下脚本来更改保留期。 请复制此脚本，将其另存为 `Set-ApplicationInsightsRetention.ps1`。

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

然后，可以下述形式使用此脚本：

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>设置每日上限

若要获取每日上限属性，请使用 [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet： 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

若要设置每日上限属性，请使用同一 cmdlet。 例如，若要将上限设置为 300 GB/天，请使用以下代码：

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

还可以使用[ARMClient](https://github.com/projectkudu/ARMClient)来获取和设置每日上限参数。  若要获取当前值，请使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>设置每日上限重置时间

若要设置每日上限重置时间，可以使用[ARMClient](https://github.com/projectkudu/ARMClient)。 下面是使用`ARMClient`的一个示例，用于将重置时间设置为新小时（在此示例中为 12:00 UTC）：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>设置定价计划 

若要获取当前定价计划，请使用 [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) cmdlet：

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

若要设置定价计划，请使用同一 cmdlet 并指定 `-PricingPlan`：  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

也可使用上面的资源管理器模板在现有的 Application Insights 资源上设置定价计划，省略“microsoft.insights/components”资源以及计费资源中的 `dependsOn` 节点。 例如，若要将它设置为“每 GB”计划（以前称为“基本”计划），请运行以下代码：

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode`定义为：

|价格代码|plan|
|---|---|
|1|“每 GB”计划（以前名为“基本”计划）|
|2|“每节点”计划（以前名为“企业”计划）|

最后，你可以使用[ARMClient](https://github.com/projectkudu/ARMClient)来获取和设置定价计划和每日上限参数。  若要获取当前值，请使用：

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

您可以使用设置所有这些参数：

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

这会将每日上限设置为 200 GB/天，将每日上限重置时间配置为 12:00 UTC，当达到上限并达到警告级别时发送电子邮件，并将警告阈值设置为达到 cap 的90%。  

## <a name="add-a-metric-alert"></a>添加指标警报

若要自动创建指标警报，请参阅[指标警报模板一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>添加可用性测试

若要自动执行可用性测试，请参阅[指标警报模板一文](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)。

## <a name="add-more-resources"></a>添加更多资源

要自动创建任何其他种类的资源，请手动创建示例，并从 [Azure 资源管理器](https://resources.azure.com/)中复制其代码并参数化。 

1. 打开 [Azure 资源管理器](https://resources.azure.com/)。 通过 `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` 向下导航到应用程序资源。 
   
    ![在 Azure 资源浏览器中导航](./media/powershell/01.png)
   
    *Components* 是用于显示应用程序的基本 Application Insights 资源。 对关联警报规则和可用性 Web 测试都有单独的资源。
2. 将组件的 JSON 复制到 `template1.json` 中的适当位置。
3. 删除这些属性：
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. 打开 `webtests` 和 `alertrules` 节并将各项的 JSON 复制到模板中。 （不要从 `webtests` 或 `alertrules` 节点复制：请进入其下的项中。）
   
    每个 Web 测试都有关联的警报规则，因此必须复制这两者。
   
    还可以包含指标相关警报。 [指标名称](powershell-alerts.md#metric-names)。
5. 在每个资源中插入此行：
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>参数化模板
现在需要将特定名称更换为参数。 若要[参数化模板](../../azure-resource-manager/templates/template-syntax.md)，则使用[一组帮助程序函数](../../azure-resource-manager/templates/template-functions.md)编写表达式。 

不能仅参数化字符串的一部分，因此使用 `concat()` 生成字符串。

下面是将要进行的替换示例。 每个替换有多个匹配项。 模板中可能需要其他项。 这些示例使用已在模板顶部定义的参数和变量。

| find | 替换项 |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`（小写） |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>设置资源之间的依赖关系
Azure 应严格按顺序设置资源。 若要确保某一设置在下一设置开始前完成，则添加依赖关系行：

* 在可用性测试资源中：
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 在可用性测试的警报资源中：
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>后续步骤
其他自动化文章：

* [创建 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - 不使用模板的快速方法。
* [设置警报](powershell-alerts.md)
* [创建 Web 测试](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [将 Azure 诊断发送到 Application Insights](powershell-azure-diagnostics.md)
* [从 GitHub 部署到 Azure](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [创建版本注释](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)