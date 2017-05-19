---
title: "使用 PowerShell 自动化 Azure Application Insights | Microsoft Docs"
description: "使用 Azure Resource Manager 模板在 PowerShell 中自动创建资源、警报和可用性测试。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e1ceaf7baef021f97f70a6b1a5203e178db613db
ms.contentlocale: zh-cn
ms.lasthandoff: 04/03/2017


---
#  <a name="create-application-insights-resources-using-powershell"></a>使用 PowerShell 创建 Application Insights 资源
本文演示如何通过 Azure 资源管理自动创建和更新 [Application Insights](app-insights-overview.md) 资源。 例如，你可能会这样做作为生成过程的一部分。 除了基本的 Application Insights 资源，还可创建[可用性 Web 测试](app-insights-monitor-web-app-availability.md)、设置[警报](app-insights-alerts.md)、设置[定价方案](app-insights-pricing.md)和创建其他 Azure 资源。

创建这些资源的关键是用于 [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md) 的 JSON 模板。 简而言之，过程如下：下载现有资源的 JSON 定义，参数化某些值（如名称），然后在需要创建新资源时运行模板。 可以将多个资源打包在一起，以便一次性创建它们，例如具有可用性测试、警报和连续导出的存储的应用监视器。 某些参数化有一些微妙之处，此处我们将进行介绍。

## <a name="one-time-setup"></a>一次性设置
如果之前尚未将 PowerShell 与 Azure 订阅结合使用：

在要运行脚本的计算机上安装 Azure Powershell 模块：

1. 安装 [Microsoft Web 平台安装程序（v5 或更高版本）](http://www.microsoft.com/web/downloads/platform.aspx)。
2. 使用它来安装 Microsoft Azure Powershell。

## <a name="create-an-azure-resource-manager-template"></a>创建 Azure Resource Manager 模板
创建新的 .json 文件，在此示例中将它命名为 `template1.json`。 将此内容复制到其中：

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
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
                    "description": "1 = Basic, 2 = Enterprise"
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
                "defaultValue": 24,
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
                    "ApplicationId": "[parameters('appName')]"
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



## <a name="create-application-insights-resources"></a>创建 Application Insights 资源
1. 在 PowerShell 中，登录到 Azure：
   
    `Login-AzureRmAccount`
2. 运行如下命令：
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` 是要创建新资源的组。
   * `-TemplateFile` 必须在自定义参数前出现。
   * `-appName` 是要创建的资源的名称。

可添加其他参数，并且可在模板的参数部分找到说明。

## <a name="to-get-the-instrumentation-key"></a>获取检测密钥
创建应用程序资源后，需使用检测密钥： 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>设置价格计划

可设置[价格计划](app-insights-pricing.md)。

若要使用上述模板通过企业价格计划创建应用资源，请执行以下步骤：

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|价格代码|计划|
|---|---|
|1|基本|
|2|Enterprise|

* 如果仅想使用默认的基本价格计划，可在模板中省略 CurrentBillingFeatures 资源。
* 若要在创建组件资源后更改价格计划，可使用一个忽略“microsoft.insights/components”资源的模板。 此外，请忽略计费资源中的 `dependsOn` 节点。 

若要验证已更新的价格计划，请查看浏览器中的“功能+定价”边栏选项卡。 **刷新浏览器视图**以确保其显示最新状态。



## <a name="add-a-metric-alert"></a>添加指标警报

若要同时设置指标警报和应用资源，请将如下代码合并到模板文件：

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

调用模板时，可选择添加此参数：

    `-responseTime 2`

当然可以参数化其他字段。 

若要查找其他警报规则的类型名称和配置详细信息，请手动创建规则，然后在 [Azure Resource Manager](https://resources.azure.com/) 中对其进行检查。 


## <a name="add-an-availability-test"></a>添加可用性测试

此示例用于进行 ping 测试（测试一页）。  

可用性测试中**有两个部分**：测试本身和通知失败的警报。

将以下代码合并到创建应用的模板文件中。

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

若要发现其他测试位置的代码，或自动创建更复杂的 Web 测试，请手动创建示例，然后在 [Azure Resource Manager](https://resources.azure.com/) 中参数化代码。

## <a name="add-more-resources"></a>添加更多资源

若要自动创建任何其他种类的资源，请手动创建示例，然后从 [Azure Resource Manager](https://resources.azure.com/) 中复制其代码并参数化。 

1. 打开 [Azure Resource Manager](https://resources.azure.com/)。 通过 `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` 向下导航到应用程序资源。 
   
    ![在 Azure 资源浏览器中导航](./media/app-insights-powershell/01.png)
   
    *Components* 是用于显示应用程序的基本 Application Insights 资源。 对关联警报规则和可用性 Web 测试都有单独的资源。
2. 将组件的 JSON 复制到 `template1.json` 中的适当位置。
3. 删除这些属性：
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. 打开 webtests 和 alertrules 部分并将各项的 JSON 复制到模板中。 （不要将从 webtests 或 alertrules 节点复制：转到其下的项。）
   
    每个 Web 测试都有关联的警报规则，因此必须复制这两者。
   
    还可以包含指标相关警报。 [指标名称](app-insights-powershell-alerts.md#metric-names)。
5. 在每个资源中插入此行：
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>参数化模板
现在需要将特定名称更换为参数。 若要[参数化模板](../azure-resource-manager/resource-group-authoring-templates.md)，则使用[一组帮助程序函数](../azure-resource-manager/resource-group-template-functions.md)编写表达式。 

不能仅参数化字符串的一部分，因此使用 `concat()` 生成字符串。

下面是将要进行的替换示例。 每个替换有多个匹配项。 模板中可能需要其他项。 这些示例使用已在模板顶部定义的参数和变量。

| 查找项 | 替换项 |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`（小写） |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>删除 GUID 和 ID。 |

### <a name="set-dependencies-between-the-resources"></a>设置资源之间的依赖关系
Azure 应严格按顺序设置资源。 若要确保某一设置在下一设置开始前完成，则添加依赖关系行：

* 在可用性测试资源中：
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 在可用性测试的警报资源中：
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>后续步骤
其他自动化文章：

* [创建 Application Insights 资源](app-insights-powershell-script-create-resource.md) - 不使用模板的快速方法。
* [设置警报](app-insights-powershell-alerts.md)
* [创建 Web 测试](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [将 Azure 诊断发送到 Application Insights](app-insights-powershell-azure-diagnostics.md)
* [从 GitHub 部署到 Azure](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [创建版本注释](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)


