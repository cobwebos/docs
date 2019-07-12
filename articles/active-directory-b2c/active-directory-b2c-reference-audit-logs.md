---
title: Azure Active Directory B2C 中的审核日志示例和定义 | Microsoft Docs
description: 访问 Azure AD B2C 审核日志的指南和示例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 216f5413ce3dae1f2d040643a30a4d7db4a879b8
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835412"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>访问 Azure AD B2C 审核日志

Azure Active Directory B2C (Azure AD B2C) 发出审核日志，其中包含有关 B2C 资源、颁发的令牌和管理员访问权限的活动信息。 本文简要概述了通过审核日志可获取的信息并介绍了如何为 Azure AD B2C 租户访问此数据。

> [!IMPORTANT]
> 审核日志仅保留七天。 如果需要保留更长时间，请使用下面所示的方法计划下载并存储日志。

> [!NOTE]
> 您不能看到用户登录为单个 Azure AD B2C 应用程序下**用户**一部分**Azure Active Directory**或**Azure AD B2C**边栏选项卡。 登录名存在将显示用户活动，但无法返回到该用户登录到 B2C 应用程序相关联。 作为这篇文章中所述，必须为此，请使用审核日志。

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>审核日志的 B2C 类别中提供的活动的概述
审核日志中的“B2C”类别包含以下类型的活动  ：

|活动类型 |描述  |
|---------|---------|
|Authorization |涉及授权用户访问 B2C 资源（例如，管理员访问 B2C 策略列表）的活动         |
|Directory |目录属性时管理员身份登录时使用 Azure 门户检索到相关的活动 |
|应用程序 | 与 B2C 应用程序相关的 CRUD 操作 |
|Key |与 B2C 密钥容器中存储的密钥相关的 CRUD 操作 |
|Resource |与 B2C 资源（如策略和标识提供者）相关的 CRUD 操作
|身份验证 |用户凭据和令牌颁发的验证|

> [!NOTE]
> 有关用户对象 CRUD 活动，请参阅“核心目录”类别  。

## <a name="example-activity"></a>示例活动
下面的示例显示了用户使用外部标识提供者登录时捕获的数据：![在 Azure 门户中的审核日志活动详细信息页示例](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

活动详细信息面板包含以下相关信息：

|部分|字段|描述|
|-------|-----|-----------|
| activities | 名称 | 哪个活动发生。 例如，"颁发 id_token 到应用程序"（最后实际用户单一登录）。 |
| 发起者（参与者） | ObjectId | **对象 ID**的用户登录到 B2C 应用程序 （此标识符不是在 Azure 门户中可见，但它是可通过图形 API 进行访问。 例如）。 |
| 发起者（参与者） | Spn | **应用程序 ID**的用户登录到 B2C 应用程序。 |
| 按目标划分 | ObjectId | **对象 ID**签名中的用户。 |
| 更多详细信息 | TenantId | **租户 ID**的 Azure AD B2C 租户。 |
| 更多详细信息 | PolicyId | **策略 ID**的用于将用户登录的用户流 （策略）。 |
| 更多详细信息 | ApplicationId | **应用程序 ID**的用户登录到 B2C 应用程序。 |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>通过 Azure 门户访问审核日志
1. 转到 [Azure 门户](https://portal.azure.com)。 请确保位于 B2C 目录。
2. 单击左侧收藏夹栏中的“Azure Active Directory” 

    ![在左侧门户菜单中突出显示的 azure Active Directory 按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. 在“活动”下，单击“审核日志”  

    ![突出显示的菜单中的活动部分中的审核日志按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. 在“类别”下拉列表中，选择“B2C”  
3. 单击“应用” 

    ![类别和审核日志筛选器中突出显示应用按钮](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

可看到最近七天记录的活动列表。
- 使用“活动资源类型”下拉列表，按上述活动类型进行筛选 
- 使用“日期范围”下拉列表，筛选所示活动的日期范围 
- 如果单击列表中的特定行，右侧的上下文框会显示与该活动关联的其他属性
- 单击“下载”可将活动下载为 csv 文件 

> [!NOTE]
> 此外可以通过导航到查看审核日志**Azure AD B2C**而非**Azure Active Directory**在左侧的收藏夹栏中。 下**活动**，单击**审核日志**，您将找到类似的筛选功能使用相同的日志。

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>通过 Azure AD 报告 API 访问审核日志
审核日志将发布到与 Azure Active Directory 其他活动相同的管道，因此可通过 [Azure Active Directory 报告 API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)进行访问。

### <a name="prerequisites"></a>先决条件
若要向 Azure AD 报告 API 进行身份验证，首先需要注册应用程序。 请确保遵循[访问 Azure AD 报告 API 的先决条件](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)中的步骤。

### <a name="accessing-the-api"></a>访问 API
若要通过 API 下载 Azure AD B2C 审核日志，需筛选“B2C”类别的日志  。 若要按类别筛选，请在调用 Azure AD 报告 API 终结点时使用查询字符串参数，如下所示：

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>PowerShell 脚本
以下脚本提供了使用 PowerShell 查询 Azure AD 报告 API 并将结果存储为 JSON 文件的示例：

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
