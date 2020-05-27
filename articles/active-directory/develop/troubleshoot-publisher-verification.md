---
title: 发布者验证故障排除 - Microsoft 标识平台 | Azure
description: 说明如何通过调用 Microsoft Graph API 对 Microsoft 标识平台的发布者验证（预览版）进行故障排除。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: cf886b7b43280e542f1941e7c0edb570868525d9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595746"
---
# <a name="troubleshoot-publisher-verification-preview"></a>发布者验证（预览版）故障排除
如果无法完成这个过程，或者在[发布者验证（预览版）](publisher-verification-overview.md)过程中遇到意外的行为，则在收到错误或看到意外行为时，应该首先执行以下操作： 

1. 查看 [要求](publisher-verification-overview.md#requirements)，并确保已满足这些要求。

1. 查看说明以[将应用标记为“发布者已验证”](mark-app-as-publisher-verified.md)，并确保已成功执行所有步骤。

1. 参阅此[常见问题](#common-issues)列表。

1. 使用 [Graph 浏览器](#making-microsoft-graph-api-calls)重现请求，以收集其他信息并排除 UI 中的任何问题。

## <a name="common-issues"></a>常见问题
下面介绍在此过程中可能会出现的一些常见问题。 

- 我不知道我的 Microsoft 合作伙伴网络 ID (MPN ID)，也不知道谁是该帐户的主要联系人 
    1. 导航到 [MPN 注册页](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. 使用组织的主要 Azure AD 租户中的用户帐户登录 
    1. 如果 MPN 帐户已存在，则会识别此帐户，并将你添加到该帐户 
    1. 导航到[“合作伙伴配置文件”页面](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)，该页面上列出了 MPN ID 和主要帐户联系人

- 我不知道谁是我的 Azure AD 全局管理员（也称为公司管理员或租户管理员），如何找到他们？应用管理员呢？或其他管理员角色呢？
    1. 使用组织的主租户中的用户帐户登录到 [Azure AD 门户](https://aad.portal.azure.com)
    1. 导航到[“角色管理”](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. 单击“全局管理员”或所需的管理员角色
    1. 此时会显示分配了该角色的用户列表

- 我不知道谁是我的 MPN 帐户管理员。转到 [MPN 用户管理页面](https://partner.microsoft.com/en-us/pcv/users) 并筛选用户列表，以查看具有各种管理员角色的用户。

- 我收到一个错误，显示我的 MPN ID 无效，或我无权访问它。
    1. 转到[合作伙伴配置文件](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile)并验证： 
        - MPN ID 正确无误。 
        - 未显示错误或“挂起的操作”，并且“法律业务配置文件”和“合作伙伴信息”下的验证状态均显示为“已授权”或“成功”。
    1. 转到[“MPN 租户管理”页](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement)，确认该应用所注册的租户，并确认登录所使用的用户帐号位于关联租户列表中。
    1. 转到[“MPN 用户管理”页](https://partner.microsoft.com/en-us/pcv/users)，确认登录的用户是全局管理员、MPN 管理员或帐户管理员。

- 当我登录到 Azure AD 门户时，我看不到任何已注册的应用程序。**为什么？** 
    应用注册可能是使用其他用户帐户或在其他租户中创建的。 确保在创建应用注册的租户中使用正确的帐户登录。

- 我如何知道谁是 Azure AD 中应用注册的所有者？ 
    登录注册应用的租户后，导航到“应用注册”边栏选项卡，单击应用，然后单击“所有者”。

## <a name="making-microsoft-graph-api-calls"></a>进行 Microsoft Graph API 调用 

如果遇到问题，但无法根据在 UI 中看到的内容理解出现问题的原因，可能有帮助的做法是，使用 Microsoft Graph 调用执行可在应用注册门户中执行的相同操作来进行进一步的故障排除。 在预览阶段，这些 API 仅可在 Microsoft Graph 的 /beta 终结点上使用。  

要实现这些请求，最简单的方法是使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)。 还可以考虑使用其他选项，例如，使用 [Postman](https://www.postman.com/)，或使用 PowerShell 来[调用 Web 请求](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)。  

可以使用 Microsoft Graph 来设置和取消设置应用的已验证发布者，并在执行其中一个操作后查看结果。 在与应用注册相对应的[应用程序](/graph/api/resources/application?view=graph-rest-beta)对象和从该应用实例化的任何[服务主体](/graph/api/resources/serviceprincipal?view=graph-rest-beta)上都可以看到该结果。 有关这些对象之间的关系的详细信息，请参阅：[Azure Active Directory 中的应用程序对象和服务主体对象](app-objects-and-service-principals.md)。  

下面是一些有用请求的示例：  

### <a name="set-verified-publisher"></a>设置已验证的发布者 

请求

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
响应 
```
204 No Content 
```
> [!NOTE]
> verifiedPublisherID 是你的 MPN ID。 

### <a name="unset-verified-publisher"></a>取消设置已验证的发布者 

请求：  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
响应 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>从应用程序获取已验证的发布者 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>从服务主体获取已验证的发布者 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>错误引用 

以下是在使用 Microsoft Graph 进行故障排除或在应用注册门户中完成此过程时，可能会收到的潜在错误代码的列表。

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

提供的 MPN ID (<MPNID>) 不存在，或你无权访问它。 提供有效的 MPN ID，然后重试。 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

提供的 MPN ID (<MPNID>) 无效。 提供有效的 MPN ID，然后重试。 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

提供的 MPN ID (<MPNID>) 无效。 提供有效的 MPN ID，然后重试。 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

提供的 MPN ID (<MPNID>) 未完成审核过程。 请在合作伙伴中心中完成此过程，然后重试。 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

提供的 MPN ID (<MPNID>) 无效。 提供有效的 MPN ID，然后重试。 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

提供的 MPN ID (<MPNID>) 无效。 提供有效的 MPN ID，然后重试。 

### <a name="applicationnotfound"></a>ApplicationNotFound  

找不到目标应用程序 (<AppId>)。 提供有效的应用程序 ID，然后重试。 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Azure AD B2C 租户不支持此功能。 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

通过电子邮件验证的租户不支持此功能。 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

目标应用程序 (<AppId>) 必须具有发布者域集。 设置发布者域，然后重试。 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

目标应用程序的发布者域 (<publisherDomain>) 不是此租户中已验证的域。 使用 DNS 验证来验证租户域，然后重试。 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

目标应用程序的发布者域 (<publisherDomain>) 与用于在合作伙伴中心内执行电子邮件验证的域 (<pcDomain>) 不一致。 请确保这两个域一致，然后重试。 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

你无权设置此应用程序 (<AppId>) 的已验证发布者属性 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

请求正文中未提供 MPN ID，或者请求内容类型不是“application/json”。 

### <a name="msanotsupported"></a>MSANotSupported  

Microsoft 使用者帐户不支持此功能。 仅支持由 Azure AD 用户在 Azure AD 中注册的应用程序。 

## <a name="next-steps"></a>后续步骤

如果已查看之前的所有信息，但仍然收到来自 Microsoft Graph 的错误信息，请尽可能多地收集以下与故障请求相关的信息，并[联系 Microsoft 支持部门](developer-support-help-options.md#open-a-support-request)。

- 时间戳 
- CorrelationId 
- 登录用户的 ObjectID 或 UserPrincipalName 
- 目标应用程序的 ObjectId
- 目标应用程序的 AppId
- 注册应用的 TenantId
- MPN ID
- 进行 REST 请求 
- 返回错误代码和消息 
