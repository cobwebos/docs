---
title: 使用主领域发现配置登录自动加速
description: 了解如何为联合用户配置用于 Azure Active Directory 身份验证的主领域发现策略，包括自动加速和域提示。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 340cf77ae6b4c5677ed91f6a0626b73d259e5fd2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690509"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>使用主领域发现策略为应用程序配置 Azure Active Directory 登录行为

本文介绍了如何为联合用户配置 Azure Active Directory 身份验证行为。 其中介绍了自动加速的配置，以及联合域中用户的身份验证限制。

## <a name="home-realm-discovery"></a>主领域发现
主领域发现 (HRD) 过程允许 Azure Active Directory (Azure AD) 确定登录时用户需要在何处进行身份验证。  登录 Azure AD 租户访问资源或 Azure AD 公共登录页时，用户需键入用户名 (UPN)。 Azure AD 以此来发现用户需要在何处登录。 

用户可能需要转到以下某个位置进行身份验证：

- 用户的主租户（可能是与用户尝试访问的资源相同的租户）。 

- Microsoft 帐户。  用户是资源租户中的来宾。

-  本地标识提供者，例如 Active Directory 联合身份验证服务 (AD FS)。

- 与 Azure AD 租户联合的其他标识提供者。

## <a name="auto-acceleration"></a>自动加速 
某些组织在其 Azure Active Directory 租户中配置域，以便与其他 IdP（例如 AD FS）联合进行用户身份验证。  

当用户登录到应用程序时，首先会看到 Azure AD 登录页。 键入 UPN 后，如果用户位于联合域中，则会转到为该域提供服务的 IdP 的登录页。 某些情况下，当用户尝试登录特定应用程序时，管理员可能希望将其定向到登录页。 

因此，用户可以跳过 Azure Active Directory 初始页。 这个过程称为“登录自动加速”。

在租户被联合到另一个 IdP 以进行登录的情况下，自动加速可进一步简化用户登录。  可以为单个应用程序配置自动加速。

>[!NOTE]
>如果为某个应用程序配置自动加速，则来宾用户不能登录。 如果将用户直接转到联合 IdP 进行身份验证，他们将无法返回到 Azure Active Directory 登录页。 来宾用户可能需要进入其他租户或外部 IdP（如 Microsoft 帐户），而不能登录该应用程序，因为他们会跳过主领域发现步骤。  

可通过两种方法控制联合 IdP 的自动加速：   

- 在应用程序的身份验证请求中使用域提示。 
- 配置主领域发现策略，启用自动加速。

### <a name="domain-hints"></a>域提示    
域提示是应用程序的身份验证请求中包含的指令。 它们可用于加速将用户转到其联合的 IdP 登录页。 或者可由多租户应用程序用来帮助用户更快地直接转到其租户的品牌 Azure AD 登录页。  

例如，应用程序“largeapp.com”可使其客户通过 URL“contoso.largeapp.com”访问应用程序。 该应用可能在身份验证请求中包含了 Contoso.com 的域提示。 

域提示语法因所用协议而异，通常在应用程序中配置。

**WS 联合身份验证**：查询字符串中的里瓦

**SAML**：包含域提示的 SAML 身份验证请求，或查询字符串 whr=contoso.com。

**OPEN ID Connect**：查询字符串 domain_hint = contoso .com。 

如果域提示包含在应用程序的身份验证请求中，且租户与该域联合，则 Azure AD 尝试将登录重定向到为该域配置的 IdP。 

如果域提示未引用已验证的联合域，则将忽略该域，并调用常规主领域发现。

有关使用 Azure Active Directory 支持的域提示实现自动加速的详细信息，请参阅[企业移动性 + 安全性博客](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)。

>[!NOTE]
>如果身份验证请求中包含域提示，则其会替代针对 HRD 策略中的应用程序设置的自动加速。

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>自动加速的主领域发现策略
某些应用程序不提供配置其发出的身份验证请求的方法。 在此情况下，域提示不能用来控制自动加速。 可以通过策略配置自动加速，以实现相同的行为。  

## <a name="enable-direct-authentication-for-legacy-applications"></a>为旧式应用程序启用直接身份验证
最佳做法是让应用程序使用 AAD 库和交互式登录对用户进行身份验证。 库会处理联合用户流。  有时，旧式应用程序无法识别联合。 它们不会执行主领域发现，并且不会通过与正确的联合终结点交互来对用户进行身份验证。 如果需要，可以使用 HRD 策略来启用特定的旧式应用程序，以提交用于在 Azure Active Directory 中直接进行身份验证的用户名/密码凭据。 必须启用密码哈希同步。 

> [!IMPORTANT]
> 仅当已启用密码哈希同步，并且知道可对此应用程序进行身份验证，且本地 IdP 未实施任何策略时，才启用直接身份验证。 如果禁用密码哈希同步，或出于任何原因禁用使用 AD Connect 的目录同步，则应删除此策略，以防止使用过期的密码哈希进行直接身份验证。

## <a name="set-hrd-policy"></a>设置 HRD 策略
需要执行三个步骤在应用程序中设置 HRD 策略，以启用联合登录自动加速或基于云的直接应用程序：

1. 创建 HRD 策略

2. 找到要将策略附加到的服务主体。

3. 将策略附加到服务主体。 

只有在将策略附加到服务主体后，策略才会对特定的应用程序生效。 

服务主体中每次只有一个 HRD 策略处于活动状态。  

你可以使用 Azure Active Directory PowerShell cmdlet 来创建和管理 HRD 策略。

以下是 HRD 策略定义示例：
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

策略类型为“HomeRealmDiscoveryPolicy”。

**AccelerateToFederatedDomain** 是可选的。 如果 **AccelerateToFederatedDomain** 为 false，则策略对自动加速不起作用。 如果 **AccelerateToFederatedDomain** 为 true 并且租户中只有一个已验证的联合域，则用户将直接转到联合 IdP 进行登录。 如果此属性为 true 并且租户中有多个已验证的域，则必须指定 **PreferredDomain**。

**PreferredDomain** 是可选的。 “PreferredDomain”应指示需要加速的域****。 如果租户仅有一个联合域，可省略此项。  如果在有多个已验证的联合域的情况下省略，则策略无效。

 如果指定“PreferredDomain”，必须与租户的某个已验证的联合域匹配****。 应用程序的所有用户都必须能够登录到该域。

**AllowCloudPasswordValidation** 是可选的。 如果 **AllowCloudPasswordValidation** 为 true，则允许应用程序通过直接向 Azure Active Directory 令牌终结点提供用户名/密码凭据，来对联合用户进行身份验证。 仅当已启用密码哈希同步时，才可以做到这一点。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD 策略的优先级和评估
可以创建 HRD 策略，并将其分配到特定组织和服务主体。 这意味着，可将多个策略应用到特定的应用程序。 生效的 HRD 策略遵循以下规则：


- 如果身份验证请求中包含域提示，则忽略用于自动加速的任何 HRD 策略。 使用由域提示指定的行为。

- 或者，如果向服务主体显式分配了某个策略，则强制实施该策略。 

- 如果没有域提示，也未向服务主体显式分配策略，则强制实施向服务主体的父组织显式分配的策略。 

- 如果没有域提示，也未向服务主体或组织显式分配策略，则使用默认 HRD 行为。

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>有关在应用程序中设置 HRD 策略的教程 
将使用 Azure AD PowerShell cmdlets 浏览几种方案，包括：


- 在包含单个联合域的租户中为应用程序设置 HRD 策略来执行自动加速。

- 针对已针对租户进行验证的多个域之一，为应用程序设置 HRD 策略以执行自动加速。

- 设置 HRD 策略，使旧式应用程序能够在 Azure Active Directory 中对联合用户执行直接的用户名/密码身份验证。

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

### <a name="example-set-an-hrd-policy-for-an-application"></a>示例：为应用程序设置 HRD 策略 

此示例创建一个策略。将此策略分配到应用程序后，它会： 
- 当租户中包含单个域时，在用户登录到应用程序期间，自动加速将用户转到 AD FS 登录屏幕的过程。 
- 当租户中包含多个联合域时，自动加速将用户转到 AD FS 登录屏幕的过程。
- 使策略分配到的应用程序的联合用户能够使用用户名/密码以非交互方式直接登录到 Azure Active Directory。

#### <a name="step-1-create-an-hrd-policy"></a>步骤 1：创建 HRD 策略

当租户中包含单个域时，在用户登录到应用程序期间，以下策略自动加速将用户转到 AD FS 登录屏幕的过程。

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
当租户中包含多个联合域时，以下策略自动加速将用户转到 AD FS 登录屏幕的过程。 如果有多个联合域对应用程序的用户进行身份验证，则需要指定要自动加速的域。

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

若要创建一个策略，使特定应用程序的联合用户能够在 Azure Active Directory 中进行用户名/密码身份验证，请运行以下命令：

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


若要查看新策略并获取其**ObjectID**，请运行以下命令：

``` powershell
Get-AzureADPolicy
```


若要在创建 HRD 策略后启用该策略，可将它分配到多个应用程序服务主体。

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>步骤 2：查找要向其分配策略的服务主体  
需要要向其分配策略的服务主体的“ObjectID”****。 可通过多种方法查找服务主体的“ObjectID”****。    

可以使用门户，或查询 [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)。 还可以转到 [Graph 浏览器工具](https://developer.microsoft.com/graph/graph-explorer)，并登录到 Azure AD 帐户，查看组织的所有服务主体。 

因为你使用的是 PowerShell，你可以使用以下 cmdlet 列出服务主体及其 Id。

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>步骤 3：向服务主体分配策略  
在获取了要配置自动加速的应用程序的服务主体的“ObjectID”后，请运行以下命令****。 该命令将步骤 1 中创建的 HRD 策略与步骤 2 中找到的服务主体关联起来。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

可以为要向其添加策略的每个服务主体重复此命令。

如果已经为应用程序分配了 HomeRealmDiscovery 策略，则无法添加另一个策略。  在这种情况下，请更改分配到应用程序的主领域发现策略的定义，以添加其他参数。

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>步骤 4：检查 HRD 策略分配到的应用程序服务主体
若要检查为其配置了 HRD 策略的应用程序，请使用 **Get-AzureADPolicyAppliedObject** cmdlet。 并向其传递要检查的策略的“ObjectID”****。

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>步骤 5：操作完成！
尝试运行应用程序，以检查新策略是否有效。

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>示例：列出为其配置了 HRD 策略的应用程序

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>步骤 1：列出在组织中创建的所有策略 

``` powershell
Get-AzureADPolicy
```

记下要列出其分配情况的策略的“ObjectID”****。

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>步骤 2：列出向其分配了策略的服务主体  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>示例：从应用程序中删除 HRD 策略
#### <a name="step-1-get-the-objectid"></a>步骤 1：获取 ObjectID
使用前一个示例获取策略的“ObjectID”，以及希望从中删除策略的应用程序服务主体的“ObjectID”****。 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>步骤 2：从应用程序服务主体中删除策略分配  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>步骤 3：通过列出向其分配了策略的服务主体检查删除情况 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>后续步骤
- 有关 Azure AD 中的身份验证工作原理的详细信息，请参阅 [Azure AD 的身份验证方案](../develop/authentication-scenarios.md)。
- 有关用户单一登录的详细信息，请参阅[对应用程序的单一登录 Azure Active Directory](what-is-single-sign-on.md)。
- 有关与开发人员相关的所有内容的概述，请访问[Microsoft 标识平台](../develop/v2-overview.md)。
