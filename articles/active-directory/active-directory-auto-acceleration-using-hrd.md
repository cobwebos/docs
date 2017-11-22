---
title: "使用主领域发现策略为应用程序配置登录自动加速 | Microsoft Docs"
description: "介绍什么是 Azure AD 租户，以及如何通过 Azure Active Directory 管理 Azure。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>使用主领域发现 (HRD) 策略为应用程序配置登录自动加速

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>主领域发现和自动加速简介
以下文档简单介绍了主领域发现和自动加速。

### <a name="home-realm-discovery"></a>主领域发现
主领域发现过程允许 Azure Active Directory 确定登录时用户需要在何处进行身份验证。  登录 Azure AD 租户以访问资源或 Azure AD 公共登录页时，用户需键入用户名 (UPN)。  Azure AD 以此来发现用户需要在何处登录。   用户可能需要转到以下位置之一进行身份验证：

- 用户的主租户（可能是与用户尝试访问的资源相同的租户）。 
- Microsoft 帐户。   用户是资源租户中的来宾
- 与 Azure AD 租户联合的其他标识提供者。  本地标识提供者，如 AD FS。

### <a name="auto-acceleration"></a>自动加速 
某些组织将其 Azure Active Directory 租户配置为与其他 IdP（如 AD FS）联合进行用户身份验证。  在这些情况下，登录应用程序时，用户首先会看到 Azure AD 登录页，并在键入其 UPN 后转到 IdP 登录页。  在合理情况下，管理员可能希望用户在登录特定应用程序时跳过初始 Azure Active Directory 页，直接进入登录页。 这被称为“登录自动加速”。

在租户与其他 IdP 联合的情况下登录时，如果已知每个登录的用户可以通过该 IdP 进行身份验证，则启用自动加速可使用户更加快速地登录。  可以为单个应用程序配置自动加速。

>[!NOTE]
>如果配置了应用程序自动加速，则来宾用户不能登录。 使用户直接进入联合 IdP 进行身份验证是不可逆选项，因为无法返回到 Azure Active Directory 登录页。  来宾用户可能需要进入其他租户或外部 IdP（如 Microsoft 帐户）进行身份验证，而不能登录该应用程序，因为将跳过主领域发现步骤。  

可通过两种方法控制联合 IdP 的自动加速。  以下两个因素中的任一个：   

- 在应用程序的身份验证请求中使用域提示 
- 配置 HomeRealmDiscovery 策略以启用自动加速

## <a name="domain-hints"></a>域提示 
域提示是应用程序的身份验证请求中包含的指令。  它们可用于加快用户转到其联合 IdP 登录页的过程，或者可由多租户应用程序用来帮助用户更快地直接转到其租户的品牌 Azure AD 登录页。  例如，应用程序 largeapp.com 可使其客户通过 URL contoso.largeapp.com 访问应用程序，并在身份验证请求中包含 Contoso.com 的域提示。 域提示语法因所用协议而异，通常在应用程序中配置。

WS 联合身份验证：查询字符串中的 whr=contoso.com

SAML：包含域提示的 SAML 身份验证请求，或查询字符串 whr=contoso.com

Open ID Connect：查询字符串 domain_hint=contoso.com 

如果域提示包含在应用程序的身份验证请求中，且租户与该域联合，则 Azure AD 尝试将登录重定向到为该域配置的 IdP。  如果域提示未引用已验证的联合域，则将忽略该域，并调用常规主领域发现。

请参阅此[博客文章](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)，详细了解使用受 Azure Active Directory 支持的域提示的自动加速。

>[!NOTE]
>如果身份验证请求中包含域提示，则其会替代为应用程序设置的任意 HRD 策略。

## <a name="home-realm-discovery-hrd-policy"></a>主领域发现 (HRD) 策略
某些应用程序未提供配置它们发出的身份验证请求的方法，在此情况下，无法使用域提示来控制自动加速。   可以通过策略配置自动加速，以实现相同的行为。  

### <a name="setting-hrd-policy"></a>设置 HRD 策略
可通过三个步骤设置应用程序登录自动加速


1. 创建自动加速的 HRD 策略
2. 查找要附加策略的服务主体
3. 将策略附加到服务主体。  可能已在租户中创建策略，但在附加到实体后才生效。  HRD 策略可以附加到服务主体，并且在给定实体上一次仅可有一个 HRD 策略处于活动状态。  

可直接使用 Microsoft Azure Active Directory 图形 API，也可通过使用 HRD，借助 Azure Active Directory PowerShell Cmdlet 来设置自动加速

[此处](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)描述了操纵策略的图形 API。

下面是 HRD 策略定义示例：
    
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

如果 AccelerateToFederatedDomain 为 false，则策略无效。PreferredDomain 应指示要加速的域，在租户有且仅有一个联合域的情况下，可将其省略。  如果在有多个已验证的联合域的情况下省略，则策略无效。

如果已指定 PreferredDomain 则其必须与租户的已验证联合域匹配，且应用程序的所有相关用户必须能够在该域登录。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 策略的优先级和评估
可以创建 HRD 策略，并将其分配到特定组织和服务主体。 这意味着，可将多个策略应用到特定的应用程序。 生效的 HRD 策略遵循以下规则：


- 如果身份验证请求中包含域提示，则忽略任意 HRD 策略，并使用由域提示指定的行为。
- 或者，如果向服务主体显式分配了某个策略，则强制实施该策略。 
- 如果没有域提示，也未向服务主体显式分配策略，则强制实施向服务主体的父组织显式分配的策略。 
- 如果没有域提示，也未向服务主体或组织显式分配策略，则使用默认 HRD 行为。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>使用 HRD 策略通过 Azure Active Directory PowerShell cmdlet 设置应用程序登录自动加速的教程
逐步讲解的方案包括：


- 为具有单个联合域的租户设置应用程序自动加速
- 针对已针对租户进行验证的多个域之一，为应用程序设置自动加速
- 列出围棋配置了策略的应用程序

### <a name="prerequisites"></a>先决条件
以下示例在 Azure AD 中的应用程序服务主体上创建、更新、链接和删除策略。

1.  首先请下载最新的 Azure AD PowerShell Cmdlet 预览版。 
2.  获取 Azure AD PowerShell Cmdlet 后，运行 Connect 命令，使用管理员帐户登录 Azure AD。

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  运行以下命令，查看组织中的所有策略。

    ``` powershell
    Get-AzureADPolicy
    ```

如果未返回任何内容，则租户中未创建任何策略

### <a name="example-setting-auto-acceleration-for-an-application"></a>示例：为应用程序设置自动加速 
在此示例中创建的策略可自动加快登录应用程序时，用户转到 AD FS 登录屏幕的过程。  无需预先输入 Azure AD 登录页的用户名即可完成此操作。 

#### <a name="step-1-create-an-hrd-policy"></a>步骤 1：创建 HRD 策略
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

如果使用单个联合域对应用程序的用户进行身份验证，则只需创建一个 HRD 策略。  
若要查看新策略并获取其 ObjectID，请运行以下命令。

``` powershell
Get-AzureADPolicy
```


拥有 HRD 策略后，可将其分配到多个应用程序服务主体，启用自动加速。

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>步骤 2：查找要向其分配策略的服务主体。  
需要要向其分配策略的服务主体的 ObjectId。 可通过多种方法查找服务主体的对象 ID。    

可以使用门户查询 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity)，或转到 [Graph Explorer 工具](https://graphexplorer.cloudapp.net/)并登录 Azure AD 帐户查看所有组织的服务主体，或者在使用 Powershell 时，使用 get-AzureADServicePrincipal cmdlet 列出服务主体和其 ID。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>步骤 3：向服务主体分配策略  
如果已知道要为其配置自动加速的应用程序的服务主体的 ObjectId，可运行以下命令来将在步骤 1 中创建的 HRD 策略与在步骤 2 中查找的服务主体关联。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

可以为要向其添加策略的每个服务主体重复此命令。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>步骤 4：检查为其分配了自动加速策略的应用程序服务主体
若要检查为其配置了自动加速策略的应用程序，请使用 Get-AzureADPolicyAppliedObject cmdlet，并向其传递要检查的策略的 objectId。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>步骤 5：操作完成！
尝试运行应用程序，以检查新策略是否有效。

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>示例：列出为其配置了自动加速策略的应用程序

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>步骤 1：列出在组织中创建的所有策略 

``` powershell
Get-AzureADPolicy
```

记下要列出其分配情况的策略的对象 ID。

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>步骤 2：列出向其分配了策略的服务主体。  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>示例：删除应用程序的自动加速策略
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>步骤 1：使用前一个示例获取策略的 ObjectId，以及希望从中删除策略的应用程序服务主体的 ObjectId
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>步骤 2：从应用程序服务主体中删除策略分配。  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>步骤 3：通过列出向其分配了策略的服务主体来检查删除情况 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>后续步骤
- 有关 Azure AD 中的身份验证工作原理的详细信息，请参阅 [Azure AD 的身份验证方案](develop/active-directory-authentication-scenarios.md)。
- 有关用户单一登录的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录](active-directory-enterprise-apps-manage-sso.md)
- 请访问 [Active Directory 开发人员指南](develop/active-directory-developers-guide.md)，了解与所有开发人员相关内容的概述。
