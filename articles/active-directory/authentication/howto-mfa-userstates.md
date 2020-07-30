---
title: 启用按用户的多重身份验证 - Azure Active Directory
description: 了解如何通过更改用户状态来启用按用户的 Azure 多重身份验证
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bd688a9e488c1206b0c8531698b061f650e2afe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417924"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>启用按用户的 Azure 多重身份验证来保护登录事件

可以通过两种方式要求在 Azure AD 中执行多重身份验证，以保护用户登录事件。 第一种做法（也是首选做法）是设置条件访问策略，要求在特定的情况下执行多重身份验证。 另一种做法是为每个用户启用 Azure 多重身份验证。 逐个为用户启用此功能后，他们每次登录时都会执行多重身份验证（有一些例外情况，例如，当他们从受信任的 IP 地址登录时，或者开启了“记忆的设备”功能时）。

> [!NOTE]
> 建议的方法是使用条件访问策略启用 Azure 多重身份验证。 除非许可证不包含条件访问，否则我们不再建议更改用户状态，因为这要求用户每次登录时都执行 MFA。 若要开始使用条件访问，请参阅[教程：使用 Azure 多重身份验证保护用户登录事件](tutorial-enable-azure-mfa.md)。
>
> 对于没有条件性访问的 Azure AD 免费租户，可以[使用安全默认值来保护用户](../fundamentals/concept-fundamentals-security-defaults.md)。

## <a name="azure-multi-factor-authentication-user-states"></a>Azure 多重身份验证用户状态

Azure 多重身份验证中的用户帐户具有以下三种不同状态：

> [!IMPORTANT]
> 通过条件访问策略启用 Azure 多重身份验证不会更改用户的状态。 如果用户看似已被禁用，请不要担心。 条件访问不会更改状态。
>
> 如果使用条件访问策略，则不应启用或强制用户。

| 状态 | 说明 | 受影响的非浏览器应用 | 受影响的浏览器应用 | 新式身份验证受影响 |
|:---:| --- |:---:|:--:|:--:|
| 已禁用 | 未在 Azure 多重身份验证中登记的新用户的默认状态。 | 否 | 否 | 否 |
| Enabled | 用户已在 Azure 多重身份验证中登记，但尚未注册身份验证方法。 在用户下次登录时会提示他们进行注册。 | 否。  它们继续工作，直到注册过程完成。 | 是的。 会话过期后，需要进行 Azure 多重身份验证注册。| 是的。 访问令牌过期后，需要进行 Azure 多重身份验证注册。 |
| 强制 | 用户已登记，并已完成 Azure 多重身份验证的注册过程。 | 是的。 应用需要应用密码。 | 是的。 登录时需要执行 Azure 多重身份验证。 | 是的。 登录时需要执行 Azure 多重身份验证。 |

用户的状态反映管理员是否已在 Azure 多重身份验证中登记用户以及用户是否已完成注册过程。

所有用户的初始状态均为“已禁用”。 在 Azure 多重身份验证中登记用户后，其状态将更改为“已启用”。 当已启用的用户登录并完成注册过程后，用户的状态将更改为“强制”。

> [!NOTE]
> 如果在已有注册详细信息（如电话号码或电子邮件）的用户对象上重新启用 MFA，则管理员需要通过 Azure 门户或 PowerShell 重新注册 MFA。 如果用户不重新注册，其 MFA 状态在 MFA 管理 UI 中不会从“已启用”转换为“已强制” 。

## <a name="view-the-status-for-a-user"></a>查看用户状态

使用以下步骤来访问可在其中查看和管理用户状态的 Azure 门户页：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后选择“用户” > “所有用户” 。
1. 选择“多重身份验证”。 可能需要向右滚动才能看到此菜单选项。 选择以下示例屏幕截图，查看完整的 Azure 门户窗口和菜单位置： [ ![ 从 Azure AD 中的 "用户" 窗口中选择 "多重身份验证"。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 此时会打开一个显示用户状态的新页，如以下示例中所示。
   ![显示了 Azure 多重身份验证的示例用户状态信息的屏幕截图](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>更改用户状态

若要更改用户的 Azure 多重身份验证状态，请完成以下步骤：

1. 使用前文的步骤访问 Azure 多重身份验证“用户”页面。
1. 找到要为其启用 Azure 多重身份验证的用户。 可能需要在顶部将视图更改为“用户”。
   ![从用户选项卡中选择要更改其状态的用户](./media/howto-mfa-userstates/enable1.png)
1. 选中要更改其状态的用户的名称旁边的框。
1. 在右侧的“快速步骤”下，选择“启用”或“禁用”  。 在以下示例中，用户 John Smith 的名称旁边有一个勾选标记，表示将为该用户启用：![通过单击快速步骤菜单上的“启用”来启用选定用户](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > “已启用”的用户在注册 Azure 多重身份验证后会自动切换为“已强制”。 不要手动将用户状态更改为“已强制”。

1. 在打开的弹出窗口中确认你的选择。

启用用户后，通过电子邮件通知他们。 告诉用户显示了提示，要求他们在下次登录时注册。 此外，如果你的组织使用不支持新式身份验证的非浏览器应用，则他们需要创建应用密码。 请参阅 [Azure 多重身份验证最终用户指南](../user-help/multi-factor-authentication-end-user.md)来了解详细信息以帮助他们开始使用。

## <a name="change-state-using-powershell"></a>使用 PowerShell 更改状态

若要使用 [Azure AD PowerShell](/powershell/azure/) 更改用户状态，请更改用户帐户的 `$st.State` 参数。 用户帐户有三种可能的状态：

* *Enabled*
* *已强制*
* *已禁用*  

不要直接将用户移动到“强制”状态。 否则，非基于浏览器的应用将停止工作，因为用户尚未完成 Azure 多重身份验证注册且未获得[应用密码](howto-mfa-app-passwords.md)。

若要开始操作，请使用 [Install-Module](/powershell/module/powershellget/install-module) 安装 MSOnline 模块，如下所示：

```PowerShell
Install-Module MSOnline
```

接下来，使用 [Connect-MsolService](/powershell/module/msonline/connect-msolservice) 进行连接：

```PowerShell
Connect-MsolService
```

以下示例 PowerShell 脚本为名为 *bsimon@contoso.com* 的个人用户启用 MFA：

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

当需要批量启用用户时，使用 PowerShell 是一个不错的选择。 以下脚本循环访问用户列表并在其帐户上启用 MFA。 定义用户帐户，并在 `$users` 的第一行中对其进行设置，如下所示：

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

为禁用 MFA，以下示例使用 [Get-MsolUser](/powershell/module/msonline/get-msoluser) 获取用户，然后使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) 删除为所定义的用户设置的任何 StrongAuthenticationRequirements：

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

还可以使用 [Set-MsolUser](/powershell/module/msonline/set-msoluser) 直接为用户禁用 MFA，如下所示：

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从按用户的 MFA 转换为基于条件访问的 MFA

以下 PowerShell 可帮助你转换到基于条件访问的 Azure 多重身份验证。

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
> 如果在已有注册详细信息（如电话号码或电子邮件）的用户对象上重新启用 MFA，则管理员需要通过 Azure 门户或 PowerShell 重新注册 MFA。 如果用户不重新注册，其 MFA 状态在 MFA 管理 UI 中不会从“已启用”转换为“已强制” 。

## <a name="next-steps"></a>后续步骤

若要配置 Azure 多重身份验证设置，请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)。

若要管理 Azure 多重身份验证的用户设置，请参阅[管理 Azure 多重身份验证的用户设置](howto-mfa-userdevicesettings.md)。

若要了解提示用户执行 MFA 与否的原因，请参阅 [Azure 多重身份验证报告](howto-mfa-reporting.md)。
