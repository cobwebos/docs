---
title: "Azure Active Directory 中的应用、权限和许可 | Microsoft 文档"
description: "Azure AD Connect 会将本地目录与 Azure Active Directory 集成。 这样，便可以为集成到 Azure AD 的 Office 365、Azure 和 SaaS 应用程序提供一个通用标识。"
keywords: "Azure AD Connect 介绍, 应用, 什么是 Azure AD Connect, 安装 active directory"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 25897cc4-7687-49b6-b0d5-71f51302b6b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/31/2017
ms.author: billmath
ms.reviewer: jesakowi
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 9f51f46b0dc942bb9749f8e962a2872a8618245a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="apps-permissions-and-consent-in-azure-active-directory"></a>Azure Active Directory 中的应用、权限和许可
在 Azure Active Directory，可以将应用程序添加到目录。  应用程序根据类型的不同而异。  要在经典门户中查看某个应用程序，请选择一个目录，并选择应用程序。

![](media/active-directory-apps-permissions-consent/apps1.png)

> [!IMPORTANT]
> Microsoft 建议使用 Azure 门户而不是本文中引用的 Azure 经典门户通过 [Azure AD 管理中心](https://aad.portal.azure.com)管理 Azure AD。

## <a name="types-of-apps"></a>应用类型

1. **单租户应用** </br>
    - **单租户应用** - 通常称为业务线 (LOB) 应用。 如果组织中的某人想要开发自己的应用，并希望组织中的用户能够登录到该应用，则适合使用多租户应用。
    ![](media/active-directory-apps-permissions-consent/apps2.png)
    - **应用程序代理应用** - 使用 Azure AD 应用代理公开本地应用程序时，单租户应用将注册到租户中（此外，还会注册到应用代理服务中）。 此应用代表本地应用程序进行所有的云交互（例如身份验证）。 （应用代理要求使用 Azure AD 基本版或更高版本。）


2. **多租户应用**
    - **其他人可以许可的多租户应用** - 类似于“组织开发的单租户应用”。 除了应用自身内部的逻辑不同之外，主要差别还在于其他租户中的用户也可以许可并登录到该应用。</br>
    ![](media/active-directory-apps-permissions-consent/apps4.png)
    - **其他人开发的、Contoso 可以许可的多租户应用**。 （简称“已许可的应用”。）此类应用与“组织开发的多租户应用”相反。 当其他组织开发多租户应用时，组织中的用户可以许可和登录到该应用。
    - **Microsoft 第一方应用** - 代表 Microsoft 服务的应用。 注册服务即表示许可该应用。 某些第一方应用有时附带特殊的用户界面 (UX) 和逻辑，围绕应用的访问建立策略时，经常会用到该界面和逻辑。</br>
    ![](media/active-directory-apps-permissions-consent/apps3.png)
    - **预先集成的应用** - Azure AD 应用库中提供的应用，可将其添加到目录，在常用 SaaS 应用中实现单一登录（在某些情况下，还可以提供预配功能）。
    - **Azure AD 单一登录**：通过受支持的登录协议（如 SAML 2.0 或 OpenID Connect）实现“真正的”SSO，适用于可与 Azure AD 集成的应用。 向导将引导完成整个设置过程。
    - **密码单一登录**：Azure AD 可安全存储用户的应用凭据，Azure AD 应用访问浏览器扩展会将这些凭据“注入”登录表单。 也称为“密码保管”。

## <a name="permissions"></a>权限

注册应用时，执行应用注册的用户（即开发人员）将定义应用需要访问哪些权限和资源。 （资源本身的定义方式与其他应用一样。）例如，构建邮件阅读器应用的用户可以指明其应用需要在“Office 365 Exchange Online”资源中拥有“以登录用户的身份访问邮箱”的权限：
    
![](media/active-directory-apps-permissions-consent/apps6.png)

为使一个应用（客户端）能够从另一个应用（资源）请求特定的权限，资源应用的开发人员可定义存在的权限。 在本示例中，“Office 365 Exchange Online”资源应用的所有者 Microsoft 定义了一个名为“以登录用户的身份访问邮箱”的权限。

定义权限时，应用开发人员必须定义是否可以许可该权限，或者该权限是否需要管理员的许可。 这样，开发人员便可以允许用户自行许可仅请求低敏感性权限的应用，但需要管理员许可更高敏感性的权限。 例如，“Azure Active Directory”资源应用在经过定义后，用户可以许可请求受限只读权限的应用。  但是，若要请求完全读取权限和所有写入权限，必须经过管理员的许可。

由于本机客户端未经过身份验证，定义为本机客户端应用的应用只能请求委托的权限。 这意味着，在获取令牌时，始终要有实际用户的干预。 Web 应用和 Web API（机密客户端）在获取访问令牌时，始终必须在 Azure AD 上进行身份验证。 也就是说，它们还有可能会请求仅限应用的权限。 例如，一个后端服务需要在另一个后端服务上进行身份验证。 请求仅限应用的权限的应用程序始终需要经过管理员的许可。

总结：



- 应用（客户端）指明它需要对其他应用（资源）拥有的权限。
- 应用（资源）指明要向其他应用（客户端）公开哪些权限。
- 权限可以是仅限应用的权限，也可以是委托的权限。
- 委托的权限可以标记为“允许用户许可”或“需要管理员许可”。
- 应用可以充当客户端（通过声明它需要对某个资源拥有的权限）和/或资源（通过声明它要公开哪些权限）。

## <a name="controls"></a>控制

下面列出了适用于所有这些行为的不同管理员控制措施。 可以在经典门户中通过目录下面的“配置”来访问管理员控制措施。

![](media/active-directory-apps-permissions-consent/apps7.png)

在 Azure 门户中的“管理”下面，选择“用户设置”。

![](media/active-directory-apps-permissions-consent/apps11.png)



- 可以控制用户能够许可应用：

在经典门户中，选择“用户可以授权应用程序访问其数据”。
![](media/active-directory-apps-permissions-consent/apps8.png)

在 Azure 门户中，选择“用户可以允许应用访问其数据”。
![](media/active-directory-apps-permissions-consent/apps12.png)



- 可以控制用户是否能够注册其自己的单租户 LOB 应用：在经典门户中，选择“用户可添加集成应用程序”。
![](media/active-directory-apps-permissions-consent/apps9.png)

在 Azure 门户中，选择“用户可以允许应用访问其数据”。
![](media/active-directory-apps-permissions-consent/apps13.png)

>[!NOTE]
>尽管可以允许用户注册单租户 LOB 应用，但他们可注册哪些应用却存在限制。  
>例如，不是目录管理员的开发人员会受到限制。
>
>- 用户无法将单租户应用转换为多租户应用。
>- 注册单租户 LOB 应用时，用户无法请求对其他应用的仅限应用的权限。
>- 注册单租户 LOB 应用时，如果委托的权限需要管理员的许可，则用户无法请求对其他应用的此类权限。
>- 对于不是由自己拥有的应用，用户无法进行更改。



- 可以控制用户是否可以自行添加使用密码 SSO（也称为“密码保管”）的预先集成的应用![](media/active-directory-apps-permissions-consent/apps10.png)



- 可以控制在哪些条件下能够访问应用程序（即条件性访问）。 请注意，这同时适用于客户端应用和资源应用。 假设你设置了一个条件性访问策略，指定只能从合规的计算机访问“Office 365 Exchange Online”应用。  当用户尝试使用请求 Exchange Online 权限的客户端应用时，也会触发此策略。



- 可以深入查看已经许可了哪些应用，以及正在使用哪些应用。

1.  当用户许可某个应用时，会在租户中创建一个 ServicePrincipal 对象。 ServicePrincipal 创建操作将包含在审核报告中。
2.  用户登录活动报告将说明用户正在登录哪个应用。 

## <a name="example"></a>示例

例如，发现租户中的用户正在登录“FabrikamMail for Office 365”应用。 “FabrikamMail”是“Fabrikam, Inc.”发布的、适用于 Android 的邮件阅读器应用。 它属于“其他人开发的、Contoso 可以许可的多租户应用”。

如果允许用户许可该应用，则当他们首次登录时，将看到许可提示：![](media/active-directory-apps-permissions-consent/apps14.png)

“访问邮箱”是针对“Office 365 Exchange Online”（即 Exchange）公开的“以登录用户的身份访问邮箱”权限，向用户显示的许可字符串。

可以通过查找 Exchange（资源）的 ServicePrincipal 对象（在注册 Office 365 时添加）来查看权限。 可将 ServicePrincipal 对象视为租户中用于记录不同选项和配置的应用“实例”。  在 PowerShell 中使用 `Get-AzureADServicePrincipal` 即可查看此对象。

    PS C:\> Get-AzureADServicePrincipal -ObjectId 383f7b97-6754-4d3d-9474-3908ebcba1c6 | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : Office 365 Exchange Online
    AppId                     : 00000002-0000-0ff1-ce00-000000000000
    AppOwnerTenantId          : 
    AppRoleAssignmentRequired : False
    AppRoles                  : {...}
    DisplayName               : Microsoft.Exchange
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {...
                                , class OAuth2Permission {
                                  AdminConsentDescription : Allows the app to have the same access to mailboxes as the signed-in user via Exchange Web Services.
                                  AdminConsentDisplayName : Access mailboxes as the signed-in user via Exchange Web Services
                                  Id                      : 3b5f3d61-589b-4a3c-a359-5dd4b5ee5bd5
                                  IsEnabled               : True
                                  Type                    : User
                                  UserConsentDescription  : Allows the app full access to your mailboxes on your behalf.
                                  UserConsentDisplayName  : Access your mailboxes
                                  Value                   : full_access_as_user
                                },
                                ...}
    PasswordCredentials       : {}
    PublisherName             : 
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {00000002-0000-0ff1-ce00-000000000000/outlook.office365.com, 00000002-0000-0ff1-ce00-000000000000/mail.office365.com, 00000002-0000-0ff1-ce00-000000000000/outlook.com, 
                                00000002-0000-0ff1-ce00-000000000000/*.outlook.com...}
    Tags                      : {}

当用户单击“接受”时，将启动许可。 首先会在租户中创建“FabrikamMail for Office 365”的 ServicePrincipal 对象。 ServicePrincipal 如下所示：

    PS C:\> Get-AzureADServicePrincipal -SearchString "FabrikamMail for Office 365" | fl *
    
    DeletionTimeStamp         : 
    ObjectId                  : a8b16333-851d-42e8-acd2-eac155849b37
    ObjectType                : ServicePrincipal
    AccountEnabled            : True
    AppDisplayName            : FabrikamMail for Office 365
    AppId                     : aba7c072-2267-4031-8960-e7a2db6e0590
    AppOwnerTenantId          : 4a4076e0-a70f-41c6-b819-6f9c4a86df89
    AppRoleAssignmentRequired : False
    AppRoles                  : {}
    DisplayName               : FabrikamMail for Office 365
    ErrorUrl                  : 
    Homepage                  : 
    KeyCredentials            : {}
    LogoutUrl                 : 
    Oauth2Permissions         : {}
    PasswordCredentials       : {}
    PublisherName             : Fabrikam, Inc.
    ReplyUrl                  : 
    SamlMetadataUrl           : 
    ServicePrincipalNames     : {aba7c072-2267-4031-8960-e7a2db6e0590}
    Tags                      : {WindowsAzureActiveDirectoryIntegratedApp}

许可某个应用会在以下各项之间创建 Oauth2PermissionGrant 链接：
  
- 用户对象
- 客户端应用的 ServicePrincipalName (SPN)
- 资源应用的 ServicePrincipalName (SPN)
- 资源应用中的权限。  

对于 FabrikamMail，该对象如下所示：

    PS C:\> Get-AzureADUserOAuth2PermissionGrant -ObjectId ddiggle@aadpremiumlab.onmicrosoft.com | fl *
    
    ClientId    : a8b16333-851d-42e8-acd2-eac155849b37
    ConsentType : Principal
    ExpiryTime  : 05/15/2017 07:02:39 AM
    ObjectId    : M2OxqB2F6EKs0urBVYSbN5d7PzhUZz1NlH25COvLocbJjoxkUFfRQauryBKwBWet
    PrincipalId : 648c8ec9-5750-41d1-abab-c812b00567ad
    ResourceId  : 383f7b97-6754-4d3d-9474-3908ebcba1c6
    Scope       : full_access_as_user
    StartTime   : 01/01/0001 12:00:00 AM

（**ClientId** 是 FabrikamMail 的服务主体对象 ID（刚刚创建），**PrincipalId** 是用户对象 ID（属于许可用户），**ResourceId** 是 Exchange 的服务主体对象 ID，Scope 是 Exchange 中已许可的权限）。

如果不允许用户许可，他们将看到一个屏幕，指出必须获取权限。

