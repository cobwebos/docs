---
title: "Azure AD B2C 使用情况报表 API 示例和定义 | Microsoft Docs"
description: "如何获取 B2C 租户用户、身份验证和 MFA 相关报表的指南和示例。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 4b2cbf5f62ee63bada42b2a06506b793d4349fdb
ms.openlocfilehash: 7db9a45a4c80ea8d01937837dfa7a15c171fb66b
ms.contentlocale: zh-cn
ms.lasthandoff: 02/10/2017


---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>通过报告 API 访问 Azure AD B2C 中的使用情况报表

Azure Active Directory B2C 跨标识提供程序为一系列应用程序的所有最终用户提供基于登录名和 MFA 的身份验证。  了解租户中注册的用户数、这些用户用于注册的提供程序以及按类型分类的身份验证数，回答以下类似问题：
* 过去 10 天内，每种类型的标识提供程序（例如，Microsoft 帐户、LinkedIn）中有多少用户注册？
* 上个月成功完成了多少多重身份验证？
* 本月完成了多少基于登录名的身份验证？ 每天？ 每个应用程序？
* 如何粗略估计 B2C 租户活动的月预计成本？

本文重点介绍与计费活动关系最密切的报表，计费活动以用户数、基于登录名的可计费身份验证数和多重身份验证数为基础。


## <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>访问 Azure AD 报告 API 的先决条件
开始前，需要先完成[访问 Azure AD 报表 API 的先决条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)。  创建应用程序、获取它的密码，并授予它访问 Azure AD B2C 租户报表的权限。 此处还提供 *Bash 脚本*和 *Python 脚本*示例。

## <a name="powershell-script"></a>PowerShell 脚本
此脚本演示了使用 **TimeStamp** 参数和 **-ApplicationId** 筛选器的四个使用情况报表。

```
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.windows.net"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
       $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>使用情况报表定义
**tenantUserCount** - 过去 30 天，每天租户中的用户数计数（按标识提供程序的类型）。 （或者，时间戳筛选器提供从指定日期到当前日期的用户计数）。 报表提供：
 * TotalUserCount = 所有用户对象的计数
 * OtherUserCount = AAD 目录用户（非 B2C 用户）的计数
 * LocalUserCount = 使用 B2C 租户的本地凭据创建的 B2C 用户帐户计数

**AlternateIdUserCount** = 向外部标识提供程序（例如，facebook、Microsoft 帐户、其他 AAD 租户 - 又称 OrgId）注册的 B2C 用户计数

**b2cAuthenticationCountSummary** - 过去 30 天内，可计费身份验证的日计数总和（按天和身份验证流的类型）

**b2cAuthenticationCount** - 某个时间段内身份验证数的计数。 默认值为过去 30 天。  （可选：开始和结束时间戳定义所需特定时段的计数）输出包括 StartTimeStamp（此租户的最早活动日期）和 EndTimeStamp（最后更新）

**b2cMfaRequestCountSummary** - 多重身份验证的日计数总和，按天和 MFA 的类型（短信或语音）


## <a name="limitations"></a>限制
* 每 24 到 48 小时将刷新一次用户计数数据。  每天将多次更新身份验证数。
* 使用 ApplicationId 筛选器时，空报表响应可能由下列条件之一导致：
 * 租户中不存在该应用程序 ID。 请确保 ID 正确无误。
 * 应用程序 ID 存在，但报表期间内未找到数据。 查看日期时间参数。


## <a name="next-steps"></a>后续步骤
### <a name="estimating-your-azure-ad-monthly-bill"></a>估计 Azure AD 每月帐单。
如果与[可用的最新 Azure AD B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)结合使用，可估计每日、每周和每月的 Azure 消耗。  计划更改可能会影响总成本的租户行为时，进行估计特别有用。  可在[链接的 Azure 订阅](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)下查看实际成本。

### <a name="options-for-other-output-formats"></a>用于其他输出格式的选项
```
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath b2cUserJourneySummaryEvents.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    

