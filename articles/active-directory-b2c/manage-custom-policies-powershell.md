---
title: 使用 PowerShell 管理自定义策略
titleSuffix: Azure AD B2C
description: 使用 Azure 活动目录 （Azure AD） PowerShell cmdlet 对 Azure AD B2C 自定义策略进行编程管理。 使用 PowerShell 创建、读取、更新和删除自定义策略。
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187400"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>使用 Azure PowerShell 管理 Azure AD B2C 自定义策略

Azure PowerShell 为 Azure AD B2C 租户中的命令行和脚本自定义策略管理提供了多个 cmdlet。 了解如何使用 Azure AD PowerShell 模块：

* 在 Azure AD B2C 租户中列出自定义策略
* 从租户下载策略
* 通过覆盖现有策略的内容更新现有策略
* 将新策略上载到 Azure AD B2C 租户
* 从租户中删除自定义策略

## <a name="prerequisites"></a>先决条件

* [Azure AD B2C 租户](tutorial-create-tenant.md)，以及具有[B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的目录中用户的凭据
* 上载到租户的[自定义策略](custom-policy-get-started.md)
* [用于图形**预览模块**的 Azure AD 电源外壳](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>将 PowerShell 会话连接到 B2C 租户

要使用 Azure AD B2C 租户中的自定义策略，首先需要使用[Connect-AzureAD][Connect-AzureAD]命令将 PowerShell 会话连接到租户。

执行以下命令，`{b2c-tenant-name}`用 Azure AD B2C 租户的名称替换。 使用在目录中分配[了 B2C IEF 策略管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)角色的帐户登录。

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

发现自定义策略允许 Azure AD B2C 管理员查看、管理和将业务逻辑添加到其操作中。 使用[获取 AzureADMSTrustFrameworkPolicy 命令][Get-AzureADMSTrustFrameworkPolicy]返回 Azure AD B2C 租户中自定义策略的 ID 列表。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

命令输出示例：

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

查看策略标识列表后，可以使用[Get-AzureMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]定位特定策略以下载其内容。

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

在此示例中，将下载具有 ID *B2C_1A_signup_signin*的策略：

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

要在本地编辑策略内容，请将命令输出管道到带有`-OutputFilePath`参数的文件，然后在您最喜爱的编辑器中打开该文件。

将输出发送到文件的示例命令：

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>更新现有策略

编辑已创建或下载的策略文件后，可以使用["设置-AzureADMSTrustFrameworkPolicy"][Set-AzureADMSTrustFrameworkPolicy]命令将更新的策略发布到 Azure AD B2C。

如果使用 Azure `Set-AzureADMSTrustFrameworkPolicy` AD B2C 租户中已存在的策略 ID 发出命令，则该策略的内容将被覆盖。

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

有关其他示例，请参阅[集 AzureADMSTrustFramework策略][Set-AzureADMSTrustFrameworkPolicy]策略策略引用。

## <a name="upload-a-new-policy"></a>上传新策略

当您对在生产中运行的自定义策略进行更改时，您可能希望发布多个版本的策略，以进行回退或 A/B 测试方案。 或者，您可能希望复制现有策略，修改它，进行一些小更改，然后将其上载为新策略供其他应用程序使用。

使用["新建 AzureMSTrustFrameworkPolicy"][New-AzureADMSTrustFrameworkPolicy]命令上载新策略：

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

示例命令：

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>删除自定义策略

为了维护干净的操作生命周期，我们建议您定期删除未使用的自定义策略。 例如，在执行对一组新策略的迁移并验证新策略的功能后，您可能希望删除旧策略版本。 此外，如果您尝试发布一组自定义策略并收到错误，则删除作为失败版本的一部分创建的策略可能有意义。

使用["删除 AzureADMSTrustFrameworkPolicy"命令][Remove-AzureADMSTrustFrameworkPolicy]从租户中删除策略。

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

示例命令：

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>排除策略上载故障

当您尝试发布新的自定义策略或更新现有策略时，不正确的 XML 格式和策略文件继承链中的错误可能会导致验证失败。

例如，下面是尝试更新包含格式错误的 XML 的内容的策略（为了简洁起见，输出被截断）：

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

有关对自定义策略进行故障排除的信息，请参阅[对 Azure AD B2C 自定义策略和标识体验框架进行故障排除](active-directory-b2c-guide-troubleshooting-custom.md)。

## <a name="next-steps"></a>后续步骤

有关使用 PowerShell 作为持续集成/持续传递 （CI/CD） 管道的一部分部署自定义策略的信息，请参阅[从 Azure DevOps 管道部署自定义策略](deploy-custom-policies-devops.md)。

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
