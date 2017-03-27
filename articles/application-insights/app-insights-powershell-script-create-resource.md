---
title: "用于创建 Application Insights 资源的 PowerShell 脚本 | Microsoft 文档"
description: "自动创建 Application Insights 资源。"
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 4a7a4b719176a1d10bee2fc4f6b65204cc77bee8


---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>用于创建 Application Insights 资源的 PowerShell 脚本


如果想要使用 [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 监视新应用程序（或新的应用程序版本），请在 Microsoft Azure 中设置新资源。 将在该资源中分析和显示应用中的遥测数据。 

可以使用 PowerShell 自动创建新的资源。

例如，如果正在开发移动设备应用，应用很可能有多个已发布版本可供客户随时使用。 你不希望从混合的不同版本中获取遥测结果。 因此，将获取生成过程以便为每个生成创建新资源。

> [!NOTE]
> 如果想要同时创建一组资源，请考虑[使用 Azure 模板创建资源](app-insights-powershell.md)。
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>用于创建 Application Insights 资源的脚本
请参阅相关 cmdlet 规范：

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell 脚本*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName Fabrikam `
  -Tag @{ applicationType = "web", applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  // or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>如何处理 iKey
每个资源都由其检测密钥 (iKey) 标识。 iKey 是资源创建脚本的输出。 生成脚本应该为嵌入应用的 Application Insights SDK 提供 iKey。

有两种方法可以向 SDK 提供 iKey：

* 在 [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) 中： 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 或在[初始化代码](app-insights-api-custom-events-metrics.md)中： 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>另请参阅
* [从模板创建 Application Insights 和 Web 测试资源](app-insights-powershell.md)
* [使用 PowerShell 设置 Azure 诊断的监视](app-insights-powershell-azure-diagnostics.md) 
* [使用 PowerShell 设置警报](app-insights-powershell-alerts.md)




<!--HONumber=Nov16_HO3-->


