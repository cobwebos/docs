---
title: "云中的 Azure MFA 入门 | Microsoft Docs"
description: "这是 Microsoft Azure 多重身份验证页面，介绍了如何在云中开始使用 Azure MFA。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d7d909184eb129e46ffc350505101e68c1426c46


---
# <a name="getting-started-with-azure-multifactor-authentication-in-the-cloud"></a>云中的 Azure Multi-Factor Authentication 入门
本文介绍如何在云中开始使用 Azure 多重身份验证。

> [!NOTE]
> 以下文档介绍如何通过 **Azure 经典门户**启用用户。 若要了解如何为 O365 用户设置 Azure 多重身份验证，请参阅[为 Office 365 设置多重身份验证](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)。
> 
> 

![云中的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>先决条件
若要为用户启用 Azure 多重身份验证，必须满足以下先决条件。

1. [注册 Azure 订阅](https://azure.microsoft.com/pricing/free-trial/) - 如果没有 Azure 订阅，则需要注册一个订阅。 对于只是在摸索如何使用 Azure MFA 的新手，可以使用试用版订阅
2. [创建多重身份验证提供程序](multi-factor-authentication-get-started-auth-provider.md)并将其分配到目录，或者[将许可证分配给用户](multi-factor-authentication-get-started-assign-licenses.md)

> [!NOTE]
> 许可证将提供给拥有 Azure MFA、Azure AD Premium 或企业移动性套件 (EMS) 的用户。  Azure AD Premium 和 EMS 中包含 MFA。 如果你有足够的许可证，则不需要创建 Auth 提供程序。
> 
> 

## <a name="turn-on-twostep-verification-for-users"></a>为用户开启双重验证
若要开始为用户开启双重验证，请将用户状态从禁用更改为启用。  有关用户状态的详细信息，请参阅 [Azure 多重身份验证中的用户状态](multi-factor-authentication-get-started-user-states.md)

可以使用以下过程为用户启用 MFA。

### <a name="to-turn-on-multifactor-authentication"></a>启用 Multi-Factor Authentication
1. 以管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左侧单击“Active Directory”。
3. 在“目录”下选择要为用户启用的目录。
   ![单击目录](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理多重身份验证”。 此时将打开新浏览器标签。
   ![单击目录](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. 找到要为其启用双重验证的用户。 你可能需要在顶部切换视图。 确保状态为“已禁用”****。
   ![启用用户](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. **勾选** 其名称旁边的框。
8. 在右侧，单击“启用”。
   ![启用用户](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. 单击“启用多重身份验证”。
   ![启用用户](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. 请注意，用户状态已从“已禁用”更改为“已启用”。
    ![启用用户](./media/multi-factor-authentication-get-started-cloud/user.png)

启用用户后，应通过电子邮件通知他们。 下次用户尝试登录时，将需要注册帐户进行双重验证。 一旦开始使用双重验证，还需设置应用密码，以避免被锁在非浏览器应用之外。

## <a name="use-powershell-to-automate-turning-on-twostep-verification"></a>使用 PowerShell 自动开启双重验证
若要使用 [Azure AD PowerShell](../powershell-install-configure.md) 更改[状态](multi-factor-authentication-whats-next.md)，可以使用以下代码。  可以将 `$st.State` 更改为以下状态之一：

* Enabled
* 强制
* 已禁用  

> [!IMPORTANT]
> 不建议直接将用户直接从“禁用”状态移到“已强制执行”状态。 基于非浏览器的应用将停止工作，因为用户尚未通过 MFA 注册并获得[应用密码](multi-factor-authentication-whats-next.md#app-passwords)。 如果具有基于非浏览器的应用并且需要应用密码，建议从“已禁用”状态转到“已启用”。 这样，用户便可以注册并获取其应用密码。 之后，就可将他们移到“已强制执行”状态。
> 
> 

使用 PowerShell 是批量启用用户的选项。 Azure 门户中目前未提供批量启用功能，需要单独选择每个用户。 如果用户数量众多，则这项任务就很繁琐。 使用上述代码创建 PowerShell 脚本可以循环访问用户列表并启用这些用户。

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

下面是一个示例：

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


有关详细信息，请参阅 [Azure 多重身份验证中的用户状态](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>后续步骤
现在已在云中设置 Azure 多重身份验证，然后可以配置和设置部署。 请参阅[配置 Azure 多重身份验证](multi-factor-authentication-whats-next.md)了解更多详细信息。




<!--HONumber=Nov16_HO2-->


