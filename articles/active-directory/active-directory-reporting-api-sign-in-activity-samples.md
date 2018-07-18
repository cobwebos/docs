---
title: Azure Active Directory 登录活动报告 API 示例 | Microsoft 文档
description: 如何开始使用 Azure Active Directory 报告 API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 52d49770014a5fb6a5eec644868e702c8a8d9ef3
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36224892"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory 登录活动报告 API 示例
本文包含在有关 Azure Active Directory 报告 API 的文章集中。  
Azure AD 报告向你提供了一个允许使用代码或相关工具访问登录活动数据的 API。  
本文用于提供**登录活动 API** 的示例代码。

请参阅：

* [审核日志](active-directory-reporting-azure-portal.md#activity-reports)，获取更多概念性信息
* [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)，获取有关报告 API 的详细信息。


## <a name="prerequisites"></a>先决条件
需要先完成[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)，才能使用本文中的示例。  

## <a name="powershell-script"></a>PowerShell 脚本

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>执行脚本
完成脚本编辑后，运行该脚本并确认从登录日志报告返回预期的数据。

此脚本以 JSON 格式从登录报告返回输出。 它还将创建具有相同输出的 `SignIns.json` 文件。 可通过修改脚本以从其他报告返回数据来进行实验，并注释掉不需要的输出格式。

## <a name="next-steps"></a>后续步骤
* 是否要自定义本文中的示例？ 请查看 [Azure Active Directory 登录活动 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)。 
* 如果想要查看使用 Azure Active Directory 报告 API 的完整概述，请参阅 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。
* 如果要查找有关 Azure Active Directory 报告的详细信息，请参阅 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)。  

