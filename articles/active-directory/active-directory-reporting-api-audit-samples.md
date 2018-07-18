---
title: Azure Active Directory 报告审核 API 示例 | Microsoft 文档
description: 如何开始使用 Azure Active Directory 报告 API
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 240095b64f4b7b37a71f84ce8d7a3521e0ab7c03
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223879"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory 报告审核 API 示例
本文包含在有关 Azure Active Directory 报告 API 的文章集中。  
Azure AD 报告向你提供了一个允许使用代码或相关工具访问审核数据的 API。
本文提供**审核 API** 的示例代码。

请参阅：

* [审核日志](active-directory-reporting-azure-portal.md#activity-reports)，获取更多概念性信息
* [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)，获取有关报告 API 的详细信息。

有关疑问、问题或反馈，请联系 [AAD 报告帮助](mailto:aadreportinghelp@microsoft.com)。


## <a name="prerequisites"></a>先决条件
需要先完成[访问 Azure AD 报告 API 的先决条件](active-directory-reporting-api-prerequisites.md)，才能使用本文中的示例。  

## <a name="known-issue"></a>已知问题
如果租户位于欧盟区域，将无法执行应用身份验证。 在我们解决此问题之前，请使用“用户身份验证”来访问审核 API。 

## <a name="powershell-script"></a>PowerShell 脚本


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
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

### <a name="executing-the-powershell-script"></a>执行 PowerShell 脚本
完成脚本编辑后，运行该脚本并确认从审核日志报告返回预期的数据。

该脚本以 JSON 格式从审核报告返回输出。 它还将创建具有相同输出的 `Audits.json` 文件。 可通过修改脚本以从其他报告返回数据来进行实验，并注释掉不需要的输出格式。





## <a name="next-steps"></a>后续步骤
* 是否要自定义本文中的示例？ 请查看 [Azure Active Directory 审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)。 
* 如果想要查看使用 Azure Active Directory 报告 API 的完整概述，请参阅 [Azure Active Directory 报告 API 入门](active-directory-reporting-api-getting-started.md)。
* 如果要查找有关 Azure Active Directory 报告的详细信息，请参阅 [Azure Active Directory 报告指南](active-directory-reporting-guide.md)。  

