---
title: 使用 PowerShell 管理自定义策略
titleSuffix: Azure AD B2C
description: 使用 Azure Active Directory (Azure AD) PowerShell cmdlet 对 Azure AD B2C 自定义策略进行编程式管理。 使用 PowerShell 创建、读取、更新和删除自定义策略。
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78187400"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure AD B2C 自定义策略

Azure PowerShell 提供了多个 cmdlet 来基于命令行和脚本对 Azure AD B2C 租户中的自定义策略进行管理。 了解如何使用 Azure AD PowerShell 模块执行以下操作：

* 列出 Azure AD B2C 租户中的自定义策略
* 从租户中下载策略
* 通过覆盖现有策略的内容来更新现有策略
* 将新策略上传到 Azure AD B2C 租户
* 从租户中删除自定义策略

## <a name="prerequisites"></a>必备条件

* [Azure AD B2C 租户](tutorial-create-tenant.md)，以及目录中具有 [B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的用户的凭据
* 已上传到租户的[自定义策略](custom-policy-get-started.md)
* [用于 Graph 的 Azure AD PowerShell **预览版模块**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>将 PowerShell 会话连接到 B2C 租户

若要处理 Azure AD B2C 租户中的自定义策略，首先需要使用 [Connect-AzureAD][Connect-AzureAD] 命令将 PowerShell 会话连接到租户。

执行以下命令，并将 `{b2c-tenant-name}` 替换为你的 Azure AD B2C 租户的名称。 使用目录中分配有 [B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的帐户进行登录。

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

显示了成功登录的示例命令输出：

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>列出租户中的所有自定义策略

通过发现自定义策略，Azure AD B2C 管理员可以查看、管理业务逻辑以及将其添加到操作中。 使用 [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 命令返回 Azure AD B2C 租户中的自定义策略的 ID 列表。

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

查看策略 ID 的列表后，可以使用 [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 指定特定策略来下载其内容。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此示例中，将下载 ID 为 *B2C_1A_signup_signin* 的策略：

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

若要在本地编辑策略内容，请使用 `-OutputFilePath` 参数通过管道将命令输出传递给文件，然后在你喜欢使用的编辑器中打开该文件。

将输出发送到文件的示例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新现有策略

编辑你已创建或下载的策略文件后，可以使用 [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 命令将更新的策略发布到 Azure AD B2C。

如果你使用 Azure AD B2C 租户中已存在的策略的 ID 发出 `Set-AzureADMSTrustFrameworkPolicy` 命令，则将覆盖该策略的内容。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

有关其他示例，请参阅 [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 命令参考。

## <a name="upload-a-new-policy"></a>上传新策略

当更改在生产环境中运行的自定义策略时，你可能想要发布策略的多个版本以用于回退或 A/B 测试方案。 或者，你可能想要创建现有策略的副本，对其进行少量更改，然后将其上传为新策略，供其他应用程序使用。

使用 [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] 命令上传新策略：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>删除自定义策略

为了保持干净的操作生命周期，建议你定期删除不使用的自定义策略。 例如，你可能想要在迁移到一组新策略并验证新策略的功能之后删除旧策略版本。 此外，如果你尝试发布一组自定义策略并收到错误，则删除作为失败版本的一部分创建的策略可能会起作用。

可以使用 [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] 命令从租户中删除策略。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

示例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>排查策略上传问题

当你尝试发布新的自定义策略或更新现有策略时，策略文件继承链中不正确的 XML 格式设置和错误可能会导致验证失败。

例如，在更新策略时尝试使用格式不正确的 XML 内容（为简洁起见，截断了输出）：

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
