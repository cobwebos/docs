---
title: Azure Active Directory B2C 中的使用情况报表 API 示例和定义 | Microsoft Docs
description: 获取有关 Azure AD B2C 租户用户、身份验证和多重身份验证报表的指南和示例。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 544b0618f9135b684846c42bb7edeb37cf599883
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445528"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>通过报告 API 访问 Azure AD B2C 中的使用情况报表

Azure Active Directory B2C (Azure AD B2C) 提供了基于用户登录名的身份验证和 Azure 多重身份验证。 跨标识提供程序为应用程序系列的最终用户提供身份验证。 如果了解租户中注册的用户数、这些用户用于注册的提供程序以及按类型分类的身份验证数，便可回答以下类似问题：
* 过去 10 天内，每种类型的标识提供程序（例如，Microsoft 或 LinkedIn 帐户）中有多少用户注册？
* 上个月成功完成了多少使用多重身份验证的身份验证？
* 本月完成了多少基于登录名的身份验证？ 每天？ 每个应用程序？
* 如何估计 Azure AD B2C 租户活动的月预计成本？

本文重点介绍与计费活动有关的报表，计费活动以用户数、基于登录名的可计费身份验证数和多重身份验证数为基础。


## <a name="prerequisites"></a>先决条件
开始前，需要先完成[访问 Azure AD 报表 API 的先决条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)中的步骤。 创建应用程序、获取它的密码，并授予它访问 Azure AD B2C 租户报表的权限。 此处还提供 *Bash 脚本*和 *Python 脚本*示例。 

## <a name="powershell-script"></a>PowerShell 脚本
此脚本演示了使用 `TimeStamp` 参数和 `ApplicationId` 筛选器创建四个使用情况报表的过程。

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
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
* **tenantUserCount**：过去 30 天，每天租户中的用户数计数（按标识提供者的类型分类）。 （或者，`TimeStamp` 筛选器提供从指定日期到当前日期的用户计数）。 报表提供：
  * **TotalUserCount**：所有用户对象数。
  * **OtherUserCount**：Azure Active Directory 用户（而非 Azure AD B2C 用户）数。
  * **LocalUserCount**：使用 Azure AD B2C 租户的本地凭据创建的 Azure AD B2C 用户帐户数。

* **AlternateIdUserCount**：使用外部标识提供程序（例如，Facebook、Microsoft 帐户或其他 Azure Active Directory 租户，也称为 `OrgId`）注册的 Azure AD B2C 用户数。

* **b2cAuthenticationCountSummary**：过去 30 天内，可计费身份验证的日计数总和（按天和身份验证流的类型分类）。

* **b2cAuthenticationCount**：某个时间段内的身份验证数。 默认值为过去 30 天。  （可选：`TimeStamp` 参数的开头和结尾定义特定时间段。）输出包括 `StartTimeStamp`（此租户活动的最早日期）和 `EndTimeStamp`（最新更新）。

* **b2cMfaRequestCountSummary**：多重身份验证的日计数总和，按天和类型（短信或语音）分类。


## <a name="limitations"></a>限制
每 24 到 48 小时将刷新一次用户计数数据。 每天将多次更新身份验证数。 使用 `ApplicationId` 筛选器时，空报表响应可能由下列条件之一导致：
  * 租户中不存在该应用程序 ID。 请确保 ID 正确无误。
  * 应用程序 ID 存在，但报表期间内未找到数据。 查看日期/时间参数。


## <a name="next-steps"></a>后续步骤
### <a name="monthly-bill-estimates-for-azure-ad"></a>Azure AD 的月账单估计数目
如果与[可用的最新 Azure AD B2C 定价](https://azure.microsoft.com/pricing/details/active-directory-b2c/)结合使用，可估计每日、每周和每月的 Azure 消耗。  计划更改可能会影响总成本的租户行为时，进行估计特别有用。 可在[链接的 Azure 订阅](active-directory-b2c-how-to-enable-billing.md)中查看实际成本。

### <a name="options-for-other-output-formats"></a>用于其他输出格式的选项
下面的代码显示了将输出发送至 JSON、名称值列表和 XML 的示例：
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
