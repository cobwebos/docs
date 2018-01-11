---
title: "使用主领域发现策略为应用程序配置登录自动加速 | Microsoft Docs"
description: "介绍什么是 Azure AD 租户，以及如何通过 Azure Active Directory 管理 Azure。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: e2e6e5c40dc4a9f67f94c45f8394512db3f777f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>使用主领域发现策略为应用程序配置登录自动加速

以下文档简单介绍了主领域发现和自动加速。

## <a name="home-realm-discovery"></a>主领域发现
主领域发现 (HRD) 过程允许 Azure Active Directory (Azure AD) 确定登录时用户需要在何处进行身份验证。  登录 Azure AD 租户访问资源或 Azure AD 公共登录页时，用户需键入用户名 (UPN)。 Azure AD 以此来发现用户需要在何处登录。 

用户可能需要转到以下某个位置进行身份验证：

- 用户的主租户（可能是与用户尝试访问的资源相同的租户）。 

- Microsoft 帐户。  用户是资源租户中的来宾。

- 与 Azure AD 租户联合的其他标识提供者。

-  本地标识提供者，例如 Active Directory 联合身份验证服务 (AD FS)。

## <a name="auto-acceleration"></a>自动加速 
某些组织将其 Azure Active Directory 租户配置为与其他 IdP（如 AD FS）联合进行用户身份验证。  

在这些情况下，用户登录某个应用程序时，首先会看到 Azure AD 登录页。 他们键入 UPN 后，将转到 IdP 登录页。 某些情况下，当用户尝试登录特定应用程序时，管理员可能希望将其定向到登录页。 

这表示用户可以跳过 Azure Active Directory 初始页。 这个过程称为“登录自动加速”。

在租户被联合到另一个 IdP 以进行登录的情况下，自动加速可进一步简化用户登录。  可以为单个应用程序配置自动加速。

>[!NOTE]
>如果为某个应用程序配置自动加速，则来宾用户不能登录。 如果将用户直接转到联合 IdP 进行身份验证，他们将无法返回到 Azure Active Directory 登录页。 来宾用户可能需要进入其他租户或外部 IdP（如 Microsoft 帐户），而不能登录该应用程序，因为他们会跳过主领域发现步骤。  

可通过两种方法控制联合 IdP 的自动加速：   

- 在应用程序的身份验证请求中使用域提示。 
- 配置主领域发现策略，启用自动加速。

## <a name="domain-hints"></a>域提示 
域提示是应用程序的身份验证请求中包含的指令。 它们可用于加速将用户转到其联合的 IdP 登录页。 或者可由多租户应用程序用来帮助用户更快地直接转到其租户的品牌 Azure AD 登录页。  

例如，应用程序“largeapp.com”可使其客户通过 URL“contoso.largeapp.com”访问应用程序。 该应用可能在身份验证请求中包含了 Contoso.com 的域提示。 

域提示语法因所用协议而异，通常在应用程序中配置。

**WS-Federation**：查询字符串中的 whr=contoso.com。

**SAML**：包含域提示的 SAML 身份验证请求，或查询字符串 whr=contoso.com。

**Open ID Connect**：查询字符串 domain_hint=contoso.com。 

如果域提示包含在应用程序的身份验证请求中，且租户与该域联合，则 Azure AD 尝试将登录重定向到为该域配置的 IdP。 

如果域提示未引用已验证的联合域，则将忽略该域，并调用常规主领域发现。

有关使用 Azure Active Directory 支持的域提示实现自动加速的详细信息，请参阅[企业移动性 + 安全性博客](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)。

>[!NOTE]
>如果身份验证请求中包含域提示，则其会替代为应用程序设置的任意 HRD 策略。

## <a name="home-realm-discovery-policy"></a>主领域发现策略
某些应用程序不提供配置其发出的身份验证请求的方法。 在此情况下，域提示不能用来控制自动加速。 可以通过策略配置自动加速，以实现相同的行为。  

### <a name="set-hrd-policy"></a>设置 HRD 策略
可通过三个步骤设置应用程序登录自动加速：


1. 创建自动加速的 HRD 策略。

2. 查找要附加策略的服务主体。

3. 将策略附加到服务主体。 可能策略已在租户中创建，但要在附加到实体后才会生效。 

HRD 策略可以附加到服务主体，并且在给定实体上一次仅可有一个 HRD 策略处于活动状态。  

可直接使用 Microsoft Azure Active Directory 图形 API，也可通过使用 HRD 策略，借助 Azure Active Directory PowerShell Cmdlet 来设置自动加速。

MSDN 中的[策略操作](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)一文介绍了用于操作策略的图形 API。

以下是 HRD 策略定义示例：
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

策略类型为“HomeRealmDiscoveryPolicy”。

如果“AccelerateToFederatedDomain”为 false，该策略无效。

“PreferredDomain”应指示需要加速的域。 如果租户仅有一个联合域，可省略此项。  如果在有多个已验证的联合域的情况下省略，则策略无效。

如果指定“PreferredDomain”，必须与租户的某个已验证的联合域匹配。 应用程序的所有用户都必须能够登录到该域。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 策略的优先级和评估
可以创建 HRD 策略，并将其分配到特定组织和服务主体。 这意味着，可将多个策略应用到特定的应用程序。 生效的 HRD 策略遵循以下规则：


- 如果身份验证请求中包含域提示，则忽略任意 HRD 策略。 使用由域提示指定的行为。

- 或者，如果向服务主体显式分配了某个策略，则强制实施该策略。 

- 如果没有域提示，也未向服务主体显式分配策略，则强制实施向服务主体的父组织显式分配的策略。 

- 如果没有域提示，也未向服务主体或组织显式分配策略，则使用默认 HRD 行为。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>有关使用 HRD 策略为应用程序设置登录自动加速的教程
将使用 Azure AD PowerShell cmdlets 浏览几种方案，包括：


- 为具有单个联合域的租户设置应用程序自动加速。

- 针对已针对租户进行验证的多个域之一，为应用程序设置自动加速。

- 列出为其配置了策略的应用程序。

### <a name="prerequisites"></a>先决条件
以下示例在 Azure AD 中的应用程序服务主体上创建、更新、链接和删除策略。

1.  首先请下载最新的 Azure AD PowerShell Cmdlet 预览版。 

2.  下载 Azure AD PowerShell Cmdlet 后，运行 Connect 命令，使用管理员帐户登录 Azure AD：

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  运行以下命令，查看组织中的所有策略：

    ``` powershell
    Get-AzureADPolicy
    ```

如果未返回任何内容，则表示租户中未创建任何策略。

### <a name="example-set-auto-acceleration-for-an-application"></a>示例：为应用程序设置自动加速 
在此示例中创建的策略自动加快登录应用程序时将用户转到 AD FS 登录屏幕的过程。 用户无需先在 Azure AD 登录页输入用户名，即可登录到 AD FS。 

#### <a name="step-1-create-an-hrd-policy"></a>步骤 1：创建 HRD 策略
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

如果使用单个联合域对应用程序的用户进行身份验证，则只需创建一个 HRD 策略。  

若要查看新策略并获取其“ObjectID”，请运行以下命令：

``` powershell
Get-AzureADPolicy
```


若要在拥有 HRD 策略后启用自动加速，可将其分配到多个应用程序服务主体。

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>步骤 2：查找要向其分配策略的服务主体  
需要要向其分配策略的服务主体的“ObjectID”。 可通过多种方法查找服务主体的“ObjectID”。    

可以使用门户，或查询 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)。 还可以转到 [Graph 浏览器工具](https://graphexplorer.cloudapp.net/)，并登录到 Azure AD 帐户，查看组织的所有服务主体。 由于正在使用 PowerShell，可使用 get-AzureADServicePrincipal cmdlet 列出服务主体及其 ID。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>步骤 3：向服务主体分配策略  
在获取了要配置自动加速的应用程序的服务主体的“ObjectID”后，请运行以下命令。 该命令将步骤 1 中创建的 HRD 策略与步骤 2 中找到的服务主体关联起来。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

可以为要向其添加策略的每个服务主体重复此命令。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>步骤 4：检查为其分配了自动加速策略的应用程序服务主体
若要检查为其配置了自动加速策略的应用程序，请使用“Get-AzureADPolicyAppliedObject”cmdlet。 并向其传递要检查的策略的“ObjectID”。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>步骤 5：操作完成！
尝试运行应用程序，以检查新策略是否有效。

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>示例：列出为其配置了自动加速策略的应用程序

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>步骤 1：列出在组织中创建的所有策略 

``` powershell
Get-AzureADPolicy
```

记下要列出其分配情况的策略的“ObjectID”。

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>步骤 2：列出向其分配了策略的服务主体  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>示例：删除应用程序的自动加速策略
#### <a name="step-1-get-the-objectid"></a>步骤 1：获取 ObjectID
使用前一个示例获取策略的“ObjectID”，以及希望从中删除策略的应用程序服务主体的“ObjectID”。 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>步骤 2：从应用程序服务主体中删除策略分配  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>步骤 3：通过列出向其分配了策略的服务主体检查删除情况 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>后续步骤
- 有关 Azure AD 中的身份验证工作原理的详细信息，请参阅 [Azure AD 的身份验证方案](develop/active-directory-authentication-scenarios.md)。
- 有关用户单一登录的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录](active-directory-enterprise-apps-manage-sso.md)。
- 请访问 [Active Directory 开发人员指南](develop/active-directory-developers-guide.md)，了解与所有开发人员相关内容的概述。
