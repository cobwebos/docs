---
title: 启用按用户多重身份验证-Azure Active Directory
description: 了解如何通过更改用户状态来启用每用户 Azure 多重身份验证
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309780"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>启用每用户 Azure 多重身份验证以保护登录事件

可以通过两种方式来保护用户登录事件，方法是在 Azure AD 中要求多重身份验证。 第一种方法是设置条件性访问策略，该策略在某些情况下需要多重身份验证。 第二种方法是为每个用户启用 Azure 多重身份验证。 当单独启用用户时，它们会在每次登录时执行多重身份验证（包括一些例外，例如，当用户从受信任的 IP 地址登录时，或打开 "_记住的设备_" 功能时）。

> [!NOTE]
> 建议使用条件性访问策略启用 Azure 多重身份验证。 不建议更改用户状态，除非你的许可证不包含条件性访问，因为你的许可证会要求用户在每次登录时执行 MFA。
>
> 若要开始使用条件性访问，请参阅[教程：使用 Azure 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure 多重身份验证用户状态

Azure 多重身份验证中的用户帐户具有以下三种不同状态：

> [!IMPORTANT]
> 通过条件性访问策略启用 Azure 多重身份验证不会更改用户的状态。 如果用户显示为禁用状态，则不会发出警报。 条件性访问不会更改状态。
>
> **如果使用条件访问策略，则不应启用或强制使用用户。**

| 状态 | 说明 | 受影响的非浏览器应用 | 受影响的浏览器应用 | 新式身份验证受影响 |
|:---:| --- |:---:|:--:|:--:|
| 禁用 | 未在 Azure 多重身份验证中注册的新用户的默认状态。 | 否 | 否 | 否 |
| 已启用 | 用户已在 Azure 多重身份验证中注册，但尚未注册。 在用户下次登录时会提示他们进行注册。 | 否。  它们继续工作，直到注册过程完成。 | 是的。 会话过期后，需要进行 Azure 多重身份验证注册。| 是的。 访问令牌过期后，需要进行 Azure 多重身份验证注册。 |
| 强制 | 用户已注册，并已完成 Azure 多重身份验证的注册过程。 | 是的。 应用需要应用密码。 | 是的。 登录时需要 Azure 多重身份验证。 | 是的。 登录时需要 Azure 多重身份验证。 |

用户的状态反映管理员是否已在 Azure 多重身份验证中注册了这些用户，以及他们是否完成了注册过程。

所有用户的初始状态均为“已禁用”**。 在 Azure 多重身份验证中注册用户时，用户的状态将更改为 "*已启用*"。 当已启用的用户登录并完成注册过程后，用户的状态将更改为“强制”**。

> [!NOTE]
> 如果在已有注册详细信息的用户对象（如电话号码或电子邮件）上重新启用 MFA，则管理员需要通过 Azure 门户或 PowerShell 重新注册 MFA。 如果用户不重新注册，则其 MFA 状态不会从 "*已启用*" 转换为 "在 MFA 管理用户界面中*强制实施*"。

## <a name="view-the-status-for-a-user"></a>查看用户状态

使用以下步骤来访问 "Azure 门户" 页，您可以在其中查看和管理用户状态：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 " *Azure Active Directory*"，然后选择 "**用户** > " "**所有用户**"。
1. 选择 "**多重身份验证**"。 可能需要向右滚动才能看到此菜单选项。 选择以下示例屏幕截图，查看完整的 Azure 门户窗口和菜单位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "从 "用户" 窗口中选择 "多重身份验证" Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 此时会打开一个显示用户状态的新页面，如下面的示例中所示。
   ![显示 Azure 多重身份验证的示例用户状态信息的屏幕截图](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>更改用户状态

若要更改用户的 Azure 多重身份验证状态，请完成以下步骤：

1. 使用前文的步骤访问 Azure 多重身份验证“用户”**** 页面。
1. 找到要为其启用 Azure 多重身份验证的用户。 可能需要将顶部的视图更改为 "**用户**"。
   ![从“用户”选项卡选择要更改状态的用户](./media/howto-mfa-userstates/enable1.png)
1. 选中要更改其状态的用户的名称旁边的框。
1. 在右侧的 "**快速步骤**" 下，选择 "**启用**" 或 "**禁用**"。 在下面的示例中，用户*John Smith*在其名称旁边有一个复选标记，并且正在启用使用： ![通过单击 "快速步骤" 菜单上的 "启用" 启用选定用户](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > 当已启用的用户注册 Azure 多重身份验证*时，会*自动切换到 "*已启用*"。 不要手动将用户状态更改为 "*强制*"。

1. 在打开的弹出窗口中确认你的选择。

启用用户后，通过电子邮件通知他们。 告诉用户显示提示，要求他们在下次登录时注册。 此外，如果你的组织使用不支持新式身份验证的非浏览器应用，则他们需要创建应用密码。 有关详细信息，请参阅[Azure 多重身份验证最终用户指南](../user-help/multi-factor-authentication-end-user.md)，帮助他们入门。

## <a name="change-state-using-powershell"></a>使用 PowerShell 更改状态

若要使用[Azure AD PowerShell](/powershell/azure/overview)更改用户状态，请更改用户帐户`$st.State`的参数。 用户帐户有三种可能的状态：

* *Enabled*
* *强制*
* *已禁用*  

不要直接将用户移动到“强制”** 状态。 如果这样做，则非基于浏览器的应用将停止工作，因为用户没有通过 Azure 多重身份验证注册并获得[应用密码](howto-mfa-mfasettings.md#app-passwords)。

若要开始，请使用[安装模块](/powershell/module/powershellget/install-module)安装*MSOnline*模块，如下所示：

```PowerShell
Install-Module MSOnline
```

接下来，使用[connect-msolservice](/powershell/module/msonline/connect-msolservice)连接：

```PowerShell
Connect-MsolService
```

下面的示例 PowerShell 脚本为名为*bsimon@contoso.com*的单个用户启用 MFA：

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

当需要批量启用用户时，使用 PowerShell 是一个不错的选择。 以下脚本将遍历用户列表，并对其帐户启用 MFA。 在的第一行`$users`中定义用户帐户，如下所示：

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

若要禁用 MFA，下面的示例将获取具有[set-msoluser](/powershell/module/msonline/get-msoluser)的用户，然后使用[set-msoluser](/powershell/module/msonline/set-msoluser)： *StrongAuthenticationRequirements*

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

还可以使用[set-msoluser](/powershell/module/msonline/set-msoluser)按如下所示直接为用户禁用 MFA：

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从每用户 MFA 转换为基于条件访问的 MFA

以下 PowerShell 可帮助你进行基于 Azure 多重身份验证的条件性访问。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 我们最近更改了行为和此 PowerShell 脚本。 以前，该脚本保存在 MFA 方法之外，禁用了 MFA，并还原了这些方法。 现在不再需要此操作，因为默认情况下禁用不会清除这些方法。
>
> 如果在已有注册详细信息的用户对象（如电话号码或电子邮件）上重新启用 MFA，则管理员需要通过 Azure 门户或 PowerShell 重新注册 MFA。 如果用户不重新注册，则其 MFA 状态不会从 "*已启用*" 转换为 "在 MFA 管理用户界面中*强制实施*"。

## <a name="next-steps"></a>后续步骤

若要配置 Azure 多重身份验证设置，如受信任的 Ip、自定义语音消息和欺诈警报，请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)。 若要管理 Azure 多重身份验证的用户设置，请参阅[通过 Azure 多重身份验证管理用户设置](howto-mfa-userdevicesettings.md)。

若要了解提示用户执行 MFA 的原因，请参阅[Azure 多重身份验证报告](howto-mfa-reporting.md#azure-ad-sign-ins-report)。
