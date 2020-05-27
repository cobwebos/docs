---
title: 适用于 B2B 来宾用户的一次性密码身份验证 - Azure AD
description: 本文介绍如何在不使用 Microsoft 帐户的情况下，使用电子邮件一次性密码对 B2B 来宾用户进行身份验证。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 520f42956a1e096893935b6b7844d67060958829
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585907"
---
# <a name="email-one-time-passcode-authentication-preview"></a>电子邮件一次性密码身份验证（预览）

|     |
| --- |
| 电子邮件一次性密码是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍了如何启用适用于 B2B 来宾用户的电子邮件一次性密码身份验证。 在无法通过 Azure AD、Microsoft 帐户 (MSA) 或 Google 联合身份验证等其他方式对 B2B 来宾用户进行身份验证时，可以使用电子邮件一次性密码功能对其进行身份验证。 使用一次性密码身份验证时，无需创建 Microsoft 帐户。 当来宾用户兑换邀请或访问共享资源时，他们可以请求临时代码，该代码会发送到他们的电子邮件地址。 他们输入此代码后，可以继续登录。

此功能目前处于预览状态（请参阅下面的[选择加入预览](#opting-in-to-the-preview)）。 预览后，会为所有租户默认启用此功能。

> [!NOTE]
> 一次性密码用户必须使用包含租户上下文的链接登录（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，或者经过验证的域 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`）。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 来宾用户目前无法使用不包含租户上下文的终结点登录。 例如，使用 `https://myapps.microsoft.com`、`https://portal.azure.com` 或团队公共终结点会导致错误。 

## <a name="user-experience-for-one-time-passcode-guest-users"></a>一次性密码来宾用户的用户体验
借助一次性密码身份验证，来宾用户可以通过单击直接链接或使用邀请电子邮件来兑换邀请。 在任一情况下，浏览器中的消息都指示代码将发送到来宾用户的电子邮件地址。 来宾用户选择“发送代码”：
 
   ![显示“发送代码”按钮的屏幕截图](media/one-time-passcode/otp-send-code.png)
 
密码将发送到用户的电子邮件地址。 用户从电子邮件检索密码，并在浏览器窗口中输入该密码：
 
   ![显示“输入代码”页的屏幕截图](media/one-time-passcode/otp-enter-code.png)
 
来宾用户现在已通过身份验证，他们可以查看共享资源或继续登录。 

> [!NOTE]
> 一次性密码在 30 分钟内有效。 30 分钟后，该特定的一次性密码不再有效，用户必须申请新密码。 用户会话在 24 小时后过期。 届时，来宾用户在访问资源时会收到新密码。 会话过期可提高安全性，尤其是当来宾用户离开公司或不再需要访问权限时。

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>来宾用户何时收到一次性密码？

当来宾用户兑换邀请或使用指向已与其共享的资源的链接时，将收到一次性密码，前提是符合以下条件：
- 他们没有 Azure AD 帐户 
- 他们没有 Microsoft 帐户 
- 邀请的租户未针对 @gmail.com 和 @googlemail.com 用户设置 Google 联合身份验证 

在邀请时，没有迹象表明你邀请的用户将使用一次性密码身份验证。 但是当来宾用户登录时，如果不能使用其他身份验证方法，则可以使用一次性密码身份验证作为应变方法。 

通过依次转到“Azure Active Directory” > “用户”，可以在 Azure 门户中查看通过一次性密码进行身份验证的来宾用户。

![显示具有 OTP 源值的一次性密码用户的屏幕截图](media/one-time-passcode/otp-users.png)

> [!NOTE]
> 在用户兑换一次性密码并稍后获得 MSA、Azure AD 帐户或其他联合帐户的情况下，他们将继续使用一次性密码进行身份验证。 如果要更新用户的身份验证方法，可以删除其来宾用户帐户并重新邀请用户。

### <a name="example"></a>示例
邀请来宾用户 alexdoe@gmail.com 加入 Fabrikam，后者未设置 Google 联合身份验证。 Alex 没有 Microsoft 帐户。 他将收到用于身份验证的一次性密码。

## <a name="opting-in-to-the-preview"></a>选择加入预览 
选择加入的操作可能需要几分钟的时间才能生效。 之后，只有符合上述条件的新受邀用户才能使用一次性密码身份验证。 之前已兑换邀请的来宾用户将继续使用同一身份验证方法。

### <a name="to-opt-in-using-the-azure-ad-portal"></a>使用 Azure AD 门户选择加入
1.  以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2.  在导航窗格中选择“Azure Active Directory”。
3.  依次选择“外部标识” > “外部协作设置”。
5.  在“为来宾启用电子邮件一次性密码(预览)”下，选择“是”。
 
### <a name="to-opt-in-using-powershell"></a>使用 PowerShell 选择加入

首先，需要安装最新版本的 Azure AD PowerShell for Graph 模块 (AzureADPreview)。 然后，确定 B2B 策略是否已存在，并运行相应的命令。

#### <a name="prerequisite-install-the-latest-azureadpreview-module"></a>先决条件：安装最新的 AzureADPreview 模块
首先，检查已安装了哪些模块。 以已提升的用户身份打开 Windows PowerShell（以管理员身份运行），然后运行以下命令：
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

如果显示了 AzureADPreview 模块，但没有任何指示还有更高版本的消息，请进行设置。 否则，请根据输出，执行以下操作之一：

- 如果未返回任何结果，请运行以下命令来安装 AzureADPreview 模块：
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- 如果结果中仅显示了 AzureAD 模块，请运行以下命令来安装 AzureADPreview 模块： 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- 如果结果中仅显示了 AzureADPreview 模块，但收到消息指出存在更新的版本，请运行以下命令来更新此模块： 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

你可能会收到正在从不受信任的存储库安装模块的提示。 如果之前未将 PSGallery 存储库设置为受信任的存储库，则可能出现此情况。 按 Y 以安装模块。

#### <a name="check-for-existing-policies-and-opt-in"></a>查看现有的策略，并选择加入

接下来，运行以下命令，检查目前 B2BManagementPolicy 是否存在：

```powershell 
$currentpolicy =  Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
$currentpolicy -ne $null
```
- 如果输出为 False，说明该策略目前不存在。 运行以下命令，创建一个新的 B2BManagementPolicy，并选择加入预览：

   ```powershell 
   $policyValue=@("{`"B2BManagementPolicy`":{`"PreviewPolicy`":{`"Features`":[`"OneTimePasscode`"]}}}")
   New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true
   ```

- 如果输出为 True，则目前存在 B2BManagementPolicy 策略。 若要更新策略，并选择加入预览，请运行以下命令：
  
   ```powershell 
   $policy = $currentpolicy.Definition | ConvertFrom-Json
   $features=[PSCustomObject]@{'Features'=@('OneTimePasscode')}; $policy.B2BManagementPolicy | Add-Member 'PreviewPolicy' $features -Force; $policy.B2BManagementPolicy
   $updatedPolicy = $policy | ConvertTo-Json -Depth 3
   Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
   ```

## <a name="opting-out-of-the-preview-after-opting-in"></a>选择加入后选择退出预览
选择退出的操作可能需要几分钟的时间才能生效。 如果禁用预览，已兑换一次性密码的所有来宾用户将无法登录。 可以删除来宾用户，然后重新邀请用户，使他们能够使用另一种身份验证方法重新登录。

### <a name="to-turn-off-the-preview-using-the-azure-ad-portal"></a>使用 Azure AD 门户禁用预览
1.  以 Azure AD 全局管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
2.  在导航窗格中选择“Azure Active Directory”。
3.  依次选择“外部标识” > “外部协作设置”。
5.  在“为来宾启用电子邮件一次性密码(预览)”下，选择“否”。

### <a name="to-turn-off-the-preview-using-powershell"></a>使用 PowerShell 禁用预览
如果尚未安装最新的 AzureADPreview 模块，请安装它，（请参阅上面的[先决条件：安装最新的 AzureADPreview 模块](#prerequisite-install-the-latest-azureadpreview-module)）。 然后，运行以下命令，验证目前是否存在一次性密码预览策略：

```powershell 
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy' -and $_.IsOrganizationDefault -eq $true} | select -First 1
($currentPolicy -ne $null) -and ($currentPolicy.Definition -like "*OneTimePasscode*")
```

如果输出为 True，请运行以下命令选择退出预览：

```powershell 
$policy = $currentpolicy.Definition | ConvertFrom-Json
$policy.B2BManagementPolicy.PreviewPolicy.Features = $policy.B2BManagementPolicy.PreviewPolicy.Features.Where({$_ -ne "OneTimePasscode"})
$updatedPolicy = $policy | ConvertTo-Json -Depth 3
Set-AzureADPolicy -Definition $updatedPolicy -Id $currentpolicy.Id
```

