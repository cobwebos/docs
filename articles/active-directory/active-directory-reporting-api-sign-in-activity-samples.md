---
title: "Azure Active Directory 登录活动报告 API 示例 | Microsoft 文档"
description: "如何开始使用 Azure Active Directory 报告 API"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 7fc2b59fe37ed2ffe85925c457300ef8fd83c3c7
ms.contentlocale: zh-cn
ms.lasthandoff: 07/17/2017

---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory 登录活动报告 API 示例
本主题包含在有关 Azure Active Directory 报告 API 的主题集合中。  
Azure AD 报告向你提供了一个允许你使用代码或相关工具访问登录活动数据的 API。  
本主题用于向你提供**登录活动 API** 的示例代码。

请参阅：

* [审核日志](active-directory-reporting-azure-portal.md#activity-reports)，获取更多概念性信息
* [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)，获取有关报告 API 的详细信息。


## <a name="prerequisites"></a>先决条件
需要先完成[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)，然后才能使用本主题中的示例。  

## <a name="powershell-script"></a>PowerShell 脚本
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>执行脚本
完成脚本编辑后，运行该脚本并确认从审核日志报告返回预期的数据。

此脚本以 JSON 格式从登录报告返回输出。 它还将创建具有相同输出的 `SigninActivities.json` 文件。 你可通过修改脚本以从其他报告返回数据来进行实验，并注释掉你不需要的输出格式。

## <a name="next-steps"></a>后续步骤
* 是否要自定义本主题中的示例？ 请查看 [Azure Active Directory 登录活动 API 参考](active-directory-reporting-api-sign-in-activity-reference.md)。 
* 如果你想要查看使用 Azure Active Directory 报告 API 的完整概述，请参阅 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。
* 如果你要查找有关 Azure Active Directory 报告的详细信息，请参阅 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)。  


