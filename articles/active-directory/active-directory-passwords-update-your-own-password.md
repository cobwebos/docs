---
title: "如何使用 Azure Active Directory 更新自己的密码 | Microsoft Docs"
description: "了解如何通过注册进行密码重置、如何更改自己的密码，以及如何在忘记密码的情况下重置自己的密码。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 47bd039ce61351b18e19408679b1d847cc70eef3
ms.lasthandoff: 03/28/2017


---
# <a name="update-your-own-password"></a>更新自己的密码
如果不确定如何管理自己的工作或学校帐户密码，来这里就对了！ 了解如何通过注册进行密码重置、如何更改密码、如何重置密码以及如何解锁帐户。

## <a name="dont-lose-access-to-your-account"></a>不要失去对帐户的访问权限！

> [!IMPORTANT]
> **为何会出现以下消息？** 如果你打开链接到达此页，则可能会看到此消息，因为管理员要求你通过注册进行密码重置，然后才能访问应用。 可能会要求你提供电话或电子邮件信息，或者设置安全问题。 不必担心！ 我们只会使用此信息来提高你帐户的安全性。 此处介绍的步骤应帮助你达到目标。
>

若要通过注册进行密码重置，最快的方法是转到[密码重置注册页](http://aka.ms/ssprsetup)。  

1. 转到[密码重置注册页](http://aka.ms/ssprsetup)。
2. 输入你的用户名和密码。
3. 单击“立即设置”，选择注册选项。 此示例演示如何使用“身份验证电话”来注册。 管理员可能要求你在注册时选择多个选项。 

   ![安装程序选项][101]
4. 从下拉列表中选择国家/地区代码，输入完整电话号码和区号，然后选择“向我发送短信”或“呼叫我”。 选择任一选项都可获得代码。 

   ![电话验证][102]
5. 输入收到的代码，然后单击“验证”。 

   ![验证代码示例][103]
6. 单击“完成”完成密码重置。 现在，你可以使用注册时选择的选项随时在[用户身份页](https://passwordreset.microsoftonline.com)上重置密码。

   ![恢复信息][104]

> [!IMPORTANT]
> 如果管理员允许你注册时选择多个选项，我们强烈建议你注册一个备用选项，以便在丢失电话或访问电子邮件时使用。
   >
   >

## <a name="change-your-password-in-office-365"></a>在 Office 365 中更改密码
请执行以下步骤，在 Office 365 中更改工作或学校帐户密码。 如果忘记了密码并想要重置，请执行[重置密码](#how-to-reset-your-password)中所述的步骤。

1. 使用工作或学校帐户登录到 Office 365。
2. 转到“设置” > “Office 365 设置” > “密码” > “更改密码”。
3. 输入旧密码，然后输入新密码并确认。
4. 单击“保存” 。

若要阅读更多有关如何在 Office 365 中更改密码的内容，可访问 [Office 365 文档中心](https://support.office.com/article/Change-my-password-in-Office-365-for-business-d1efbaee-63a7-4c08-ab1d-71bf932bbb5d)。

## <a name="change-your-password-from-the-access-panel"></a>从访问面板更改密码
请执行以下步骤，从[访问面板](https://myapps.microsoft.com)更改工作或学校帐户密码。 如果忘记了密码并想要重置，请执行[重置密码](#how-to-reset-your-password)中所述的步骤。

1. 使用工作或学校帐户登录 https://myapps.microsoft.com。
2. 单击“配置文件”选项卡。 
3. 单击“更改我的密码”。
4. 输入旧密码，然后输入新密码并确认。
5. 单击“提交”。

更改密码时是否遇到问题？ 请阅读[常见问题及其解决方案](#common-problems-and-their-solutions)。

## <a name="reset-your-password"></a>重置密码
请执行以下步骤，从任何工作或学校帐户登录页重置工作或学校帐户密码。

> [!IMPORTANT]
> 仅当管理员启用了此功能时，你才可以使用此功能。 如果未启用，将会显示一条消息，指出没有为帐户启用此功能。 使用“与管理员联系”链接与管理员取得联系，以便解锁你的帐户。
>
> 如果管理员已为帐户启用了此功能，你需要先注册，然后才能使用此功能。 可以在[密码重置注册页](http://aka.ms/ssprsetup)上执行该操作。
>
>

1. 在任何工作或学校帐户登录页上，单击“无法访问帐户?”链接，或者转到[用户身份页](https://passwordreset.microsoftonline.com)。

   ![帐户登录][110]

2. 输入工作或学校帐户 ID，并通过回答 CAPTCHA 质询来证明你不是机器人。 

   ![标识确认][111]

3. 单击“资源组名称” 的 Azure 数据工厂。
4. 选择重置密码的选项。 依据管理员配置系统的方式，你可能会看到以下一个或多个选项：

   * **向我的备用电子邮件发送电子邮件**：将包含 6 位数代码的电子邮件发送到你的**备用电子邮件**或**身份验证电子邮件**（由你选择）。
   * **向我的移动电话发送短信**：将包含 6 位数代码的短信发送到你的**移动电话**或**身份验证电子邮件**（由你选择）。
   * **拨打我的移动电话**：拨打你的**移动电话**或**身份验证电话**（由你选择）。 按 # 键确认呼叫。
   * **拨打我的办公电话**：拨打你的**办公电话**。 按 # 键确认呼叫。
   * **回答我的安全问题**：显示预先注册的需要你回答的安全问题。

   ![密码重置选项][109]

5. 此示例显示“向我的移动电话发送短信”选项已选中。 对于基于电话的选项，请输入完整的电话号码，然后单击“短信”验证号码是否正确。 然后，系统就会向你的手机发送包含 6 位数代码的短信。

6. 收到短信后，请确保使用短信正文中的验证码，而不是发送验证码的号码。 可能需要等几分钟才能收到短信。

7. 输入收到的 6 位数代码，然后单击“下一步”。

8. 管理员可能会要求你执行其他验证步骤。 如果是这样，请根据所选的其他选项重复步骤 4。

9. 在“选择新密码”页上，输入新密码并确认所做的选择，然后单击“完成”。

   ![新密码确认][107]

10. 系统接受你的密码后，你可以使用新密码登录。

    ![密码重置确认][108]

重置密码时遇到问题？ 请阅读[常见问题及其解决方案](#common-problems-and-their-solutions)。

## <a name="unlock-your-account"></a>解锁帐户
请执行以下步骤，从任何工作或学校帐户登录页解锁本地帐户。 

> [!NOTE]
> 只能解锁本地锁定的帐户。

> [!IMPORTANT]
> 仅当管理员启用了此功能时，你才可以使用此功能。 如果未启用，将会显示一条消息，指出没有为帐户启用此功能。 使用“与管理员联系”链接与管理员取得联系，以便解锁你的帐户。
>
> 如果管理员已为帐户启用了此功能，你需要先注册，然后才能使用此功能。 可以在[密码重置注册页](http://aka.ms/ssprsetup)上执行该操作。
>
>

1. 在任何工作或学校帐户登录页上，单击“无法访问帐户?”链接，或者转到[用户身份页](https://passwordreset.microsoftonline.com)。

   ![帐户登录][110]
2. 在“用户身份”页上，输入工作或学校帐户 ID，并通过回答 CAPTCHA 质询来证明你不是机器人。 
3. 单击“资源组名称” 的 Azure 数据工厂。

   ![标识确认][111]
4. 选择解锁帐户的选项。 根据管理员配置系统的方式，可能会出现以下一个或多个选项：

   * **向我的备用电子邮件发送电子邮件**：将包含 6 位数代码的电子邮件发送到你的**备用电子邮件**或**身份验证电子邮件**（由你选择）。
   * **向我的移动电话发送短信**：将包含 6 位数代码的短信发送到你的**移动电话**或**身份验证电子邮件**（由你选择）。
   * **拨打我的移动电话**：拨打你的**移动电话**或**身份验证电话**（由你选择）。 按 # 键确认呼叫。
   * **拨打我的办公电话**：拨打你的**办公电话**。 按 # 键确认呼叫。
   * **回答我的安全问题**：显示预先注册的需要你回答的安全问题。

   ![标识验证选项][112]
5. 此示例显示“回答我的安全问题”选项已选中。 填写安全问题的答案，然后单击“下一步”验证你的身份。

6. 管理员可能会要求你执行其他验证步骤。 如果是这样，请根据所选的其他选项重复步骤 4。
7. 如果显示成功页，则表明你的本地帐户已解锁，现在你又可以登录了。

   ![解锁帐户登录][113]

> [!IMPORTANT]
> 请确保使用新密码更新所有设备。 使用旧密码的恶意应用（例如手机电子邮件客户端）通常是帐户最初遭到锁定的罪魁祸首。
   >
   >


## <a name="common-problems-and-their-solutions"></a>常见问题及其解决方法
以下是一些常见的错误案例及其解决方法：

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>错误案例</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>你看到了什么错误消息？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>解决方案</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>在输入我的用户 ID 后，出现了“请联系管理员”页面。</p>
            </td>
            <td>
              <p>请联系你的管理员。 <br><br>我们检测到你的用户帐户密码不受 Microsoft 管理。 因此，我们无法自动重置密码。 <br><br>请联系管理员或客服人员，以寻求进一步的帮助。 </p>
            </td>
            <td>
              <p>你之所以看到此消息，是因为管理员在本地环境中管理你的密码，而不允许你从“无法访问帐户”链接<b></b>重置密码。 <br><br> 若要重置密码，请直接与管理员联系以获取帮助。 告知管理员你想要从 Office 365 重置密码，让其为你启用此功能。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>在输入我的用户 ID 后，出现“你的帐户未启用密码重置”错误。</p>
            </td>
            <td>
              <p>你的帐户未启用密码重置。<br><br>你的管理员尚未将你的帐户设置为可使用此服务。<br><br> 如果你愿意，我们可以联系你所在组织的管理员，让其为你重置密码。</p>
            </td>
            <td>
              <p>你之所以看到此消息，是因为管理员尚未通过“无法访问帐户”<b></b>链接启用密码重置功能，或未授权你使用该功能。 <br><br> 若要重置密码，请单击“联系管理员”<b></b>链接，向组织管理员发送电子邮件。 告知管理员你想要在 Office 365 中重置密码，让其为你启用此功能。</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>在输入我的用户 ID 后，出现了“我们无法验证你的帐户”错误。</p>
            </td>
            <td>
              <p>我们无法验证你的帐户。<br><br>如果你愿意，我们可以联系你所在组织的管理员，让其为你重置密码。 </p>
            </td>
            <td>
              <p>你之所以看到此消息，是因为已经为你启用了密码重置，但你并未注册使用此服务。 若要通过注册进行密码重置，请在重新获取帐户访问权限后转到[密码重置注册页](http://aka.ms/ssprsetup)。 <br><br> 若要重置密码，请单击“联系管理员”<b></b>链接，向组织管理员发送电子邮件。</p>
            </td>
          </tr>
        </tbody></table>

## <a name="next-steps"></a>后续步骤
如果对自助密码重置 (SSPR) 有其他疑问，请与管理员联系，或单击下面的链接：

* [需要注册 SSPR 信息？](http://aka.ms/ssprsetup)
* [无法访问你的帐户？](https://passwordreset.microsoftonline.com)
* [Office 365 密码重置信息](https://support.office.com/en-us/article/Reset-user-passwords-in-Office-365-3254c031-04c9-44f1-8fda-2563847a6b31?ui=en-US&rs=en-US&ad=US)
* [访问面板](https://myapps.microsoft.com)

[101]: ./media/active-directory-passwords-update-your-own-password/password-1-dont-lose-access.png "password-1-dont-lose-access.png"
[102]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-response.png "password-2-verification-response.png"
[103]: ./media/active-directory-passwords-update-your-own-password/password-2-verification-text.png "password-2-verification-text.png"
[104]: ./media/active-directory-passwords-update-your-own-password/password-3-registration-complete.png "password-3-registration-complete.png"
[105]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-cant-access.png "password-4-reset-cant-access.png"
[106]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-captcha.png "password-4-reset-captcha.png"
[107]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-change.png "password-4-reset-change.png"
[108]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-finished.png "password-4-reset-finished.png"
[109]: ./media/active-directory-passwords-update-your-own-password/password-4-reset-verification.png "password-4-reset-verification.png"
[110]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-cant-access.png "password-5-unlock-cant-access.png"
[111]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-captcha.png "password-5-unlock-captcha.png"
[112]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-verification.png "password-5-unlock-verification.png"
[113]: ./media/active-directory-passwords-update-your-own-password/password-5-unlock-finished.png "password-5-unlock-finished.png"

