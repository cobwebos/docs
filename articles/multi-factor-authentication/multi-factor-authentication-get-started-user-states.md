---
title: "Microsoft Azure 多重身份验证用户状态"
description: "在 Azure MFA 中了解用户状态信息。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 1869b7a4ef42536a3cd909ba2983ae0fe97185a9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>如何要求对用户或组进行双重验证

要求双重验证有两种方法。 第一个选项是为 Azure 多重身份验证 (MFA) 启用每个单独用户。 单独启用用户时，他们始终会执行双重验证（会有一些例外情况，如当他们从受信任的 IP 地址登录时，或者被记住的设备功能打开时）。 第二个选项是设置条件性访问策略，可要求在某些情况下进行双重验证。

>[!TIP] 
>可选择两个方法中的一个（而不是选择两者），以要求进行双重验证。 启用 Azure MFA 的用户可以覆盖任何条件性访问策略。

## <a name="which-option-is-right-for-you"></a>哪种选项最适合你

通过更改用户状态启用 Azure MFA 是要求进行双重验证的传统方法。 它适用于云中的 Azure MFA 以及 Azure MFA 服务器。 你启用的所有用户都具有相同体验，即每次登录时都执行双重验证。 启用用户可以覆盖可能影响该用户的任何条件性访问策略。 

使用条件性访问策略启用 Azure MFA 是要求进行双重验证更为灵活的一种方法。 不过，它仅适用于云中的 Azure MFA，而条件性访问是 [Azure Active Directory 的一项付费功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)。 你可以创建应用于组和单个用户的条件性访问策略。 相对于低风险组，可以为高风险组指定更多的限制，或者只要求对高风险云应用进行双重验证，而跳过低风险组。 

这两种选项都会提示用户在启用要求后第一次登录时注册 Azure 多重身份验证。 另外，这两个选项也可用于可配置的 [Azure 多重身份验证设置](multi-factor-authentication-whats-next.md)

## <a name="enable-azure-mfa-by-changing-user-status"></a>通过更改用户状态启用 Azure MFA

Azure 多重身份验证中的用户帐户具有以下三种不同状态：

| 状态 | 说明 | 受影响的非浏览器应用 |
|:---:|:---:|:---:|
| 已禁用 |未加入多重身份验证 (MFA) 的新用户的默认状态。 |否 |
| Enabled |用户已加入 Azure MFA 但尚未注册。 系统会在用户下次登录时提示注册。 |否。  它们继续工作，直到注册过程完成。 |
| 强制 |用户已加入，并已完成 Azure MFA 的注册过程。 |是的。  应用需要应用密码。 |

用户的状态反映管理员是否已在 Azure MFA 中登记用户以及用户是否已完成注册过程。

所有用户的初始状态均为“已禁用”。 在 Azure MFA 中登记用户时，用户的状态将更改为“已启用”。 当已启用的用户登录并完成注册过程时，用户的状态将更改为“强制”。  

### <a name="view-the-status-for-a-user"></a>查看用户状态

使用以下步骤来访问可在其中查看和管理用户状态的页面：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 转到“Azure Active Directory” > “用户和组” > “所有用户”。
3. 选择“多重身份验证”。
   选择“多重身份验证”![](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. 会打开一个显示用户状态的新页面。
   ![多重身份验证用户状态 - 屏幕截图](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>更改用户状态

1. 使用前文的步骤访问多重身份验证用户页面。
2. 找到想要对其启用 Azure MFA 的用户。 可能需要在顶部切换视图。 
   ![查找用户 - 屏幕截图](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 勾选用户名称旁边的框。
4. 在右侧的快速步骤下，选择“启用”或“禁用”。
   ![启用选定的用户 - 屏幕截图](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >启用用户在注册 Azure MFA 时会自动切换到“强制”。 不应手动将用户状态更改为“强制”。 

5. 在打开的弹出窗口中确认你的选择。 

启用用户后，应通过电子邮件通知他们。 告诉他们将需要在下次登录时进行注册。 此外，如果组织使用不支持新式身份验证的非浏览器应用，他们将需要创建应用密码。 还可以包括指向 [Azure MFA 最终用户指南](./end-user/multi-factor-authentication-end-user.md)的链接，以便帮助他们上手。

### <a name="use-powershell"></a>使用 PowerShell
若要更改用户状态，请使用 [Azure AD PowerShell](/powershell/azure/overview)，更改 `$st.State`。 有三种可能的状态：

* Enabled
* 强制
* 已禁用  

不要直接将用户移动到“强制”状态。 基于非浏览器的应用将停止工作，因为用户尚未通过 MFA 注册并获得[应用密码](multi-factor-authentication-whats-next.md#app-passwords)。 

当你需要批量启用用户时，使用 PowerShell 是一个不错的选择。 创建一个 PowerShell 脚本，它会循环访问用户列表并启用它们：

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

下面是一个示例：

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>使用条件性访问策略启用 Azure MFA

条件性访问是 Azure Active Directory 的一项付费功能，其中包含许多可能的配置选项。 以下步骤演示了创建策略的一种方法。 有关详细信息，请参阅 [Azure Active Directory 中的条件访问](../active-directory/active-directory-conditional-access-azure-portal.md)。

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 转到“Azure Active Directory” > “条件性访问”。
3. 选择“新策略”。
4. 在“分配”下，选择“用户和组”。 使用“包括”和“排除”选项卡，指定将由策略管理的用户和组。
5. 在“分配”下，选择“云应用”。 选择以包含“所有云应用”。
6. 在“访问控制”下，选择“授予”。 选择“要求多重身份验证”。
7. 将“启用策略”设置为“启用”，然后选择“保存”。

使用条件性访问策略中的其他选项，可以指定应要求进行双重验证的确切时间。 例如，你可以制定如下策略：当签约方尝试在未加入域的设备上从不受信任的网络访问我们的采购应用时，要求进行双重验证。 

## <a name="next-steps"></a>后续步骤

- 在[条件性访问的最佳做法](../active-directory/active-directory-conditional-access-best-practices.md)中获取提示

- 管理[用户及其设备](multi-factor-authentication-manage-users-and-devices.md)的多重身份验证设置