---
title: 通过 PowerShell 管理自定义策略
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory （Azure AD） PowerShell cmdlet 对 Azure AD B2C 自定义策略进行编程管理。 通过 PowerShell 创建、读取、更新和删除自定义策略。
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e29bb245ed0fc79a6f72688dc6e4d044f2828c45
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463123"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>通过 Azure PowerShell 管理 Azure AD B2C 自定义策略

Azure PowerShell 为 Azure AD B2C 租户中的命令行和基于脚本的自定义策略管理提供多个 cmdlet。 了解如何使用 Azure AD PowerShell 模块来执行以下操作：

* 列出 Azure AD B2C 租户中的自定义策略
* 从租户下载策略
* 通过覆盖现有策略的内容来更新现有策略
* 将新策略上传到 Azure AD B2C 租户
* 从租户中删除自定义策略

## <a name="prerequisites"></a>必备条件

* 使用[B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色在目录中为用户[Azure AD B2C 租户](tutorial-create-tenant.md)和凭据
* 已上传到租户的[自定义策略](custom-policy-get-started.md)
* [Graph**预览模块**Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>将 PowerShell 会话连接到 B2C 租户

若要在 Azure AD B2C 租户中使用自定义策略，首先需要使用[AzureAD][Connect-AzureAD]命令将 PowerShell 会话连接到租户。

执行以下命令，将 `{b2c-tenant-name}` 替换为 Azure AD B2C 租户的名称。 使用在目录中分配有[B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的帐户登录。

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

显示成功登录的示例命令输出：

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>列出租户中的所有自定义策略

发现自定义策略后，Azure AD B2C 管理员可以查看、管理业务逻辑并将其添加到其操作中。 使用[AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]命令返回 Azure AD B2C 租户中自定义策略的 id 列表。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

示例命令输出：

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>下载策略

查看策略 Id 列表后，可以使用[AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]的特定策略来下载其内容。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此示例中，下载了 ID 为*B2C_1A_signup_signin*的策略：

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

若要在本地编辑策略内容，请通过管道将命令输出传递给具有 `-OutputFilePath` 参数的文件，然后在最喜爱的编辑器中打开该文件。

将输出发送到文件的示例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新现有策略

编辑创建或下载的策略文件后，可以使用[AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy]命令将更新后的策略发布到 Azure AD B2C。

如果你使用 Azure AD B2C 租户中已存在的策略的 ID 发出 `Set-AzureADMSTrustFrameworkPolicy` 命令，则将覆盖该策略的内容。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

有关其他示例，请参阅[AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy]命令参考。

## <a name="upload-a-new-policy"></a>上传新策略

当你更改在生产环境中运行的自定义策略时，你可能想要为回退或 A/B 测试方案发布多个版本的策略。 或者，你可能想要创建一个现有策略的副本，对其进行少量的更改，然后将其上传为其他应用程序使用的新策略。

使用[AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy]命令上传新策略：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>删除自定义策略

为了保持干净的操作生命周期，建议你定期删除未使用的自定义策略。 例如，你可能想要在执行迁移到一组新策略并验证新策略的功能之后删除旧策略版本。 此外，如果你尝试发布一组自定义策略并收到错误，则删除作为失败的发布的一部分而创建的策略可能有意义。

使用[AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy]命令从租户中删除策略。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

示例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>排查策略上传问题

当你尝试发布新的自定义策略或更新现有策略时，策略文件继承链中不正确的 XML 格式和错误可能会导致验证失败。

例如，在使用包含格式不正确的 XML 的内容更新策略时，会尝试使用以下方式：

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

有关自定义策略疑难解答的信息，请参阅[排查 Azure AD B2C 自定义策略和标识体验框架](active-directory-b2c-guide-troubleshooting-custom.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何使用 PowerShell 将自定义策略部署为持续集成/持续交付（CI/CD）管道的一部分，请参阅[从 Azure DevOps 管道部署自定义策略](deploy-custom-policies-devops.md)。

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
