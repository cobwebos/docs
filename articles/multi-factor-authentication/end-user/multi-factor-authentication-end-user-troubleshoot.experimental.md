---
title: "双重验证疑难解答 |Microsoft 文档"
description: "本文档向用户提供有关如何解决 Azure 多重身份验证问题的信息。"
services: multi-factor-authentication
keywords: "多重身份验证客户端, 身份验证问题, 相关性 ID"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: end-user
ms.openlocfilehash: 59a24c8dbcf740f9154f1c9e18f318265f58481c
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2017
---
# <a name="get-help-with-two-step-verification"></a>获取有关双重验证的帮助
本文解答有关双重验证的最常见问题。 

## <a name="why-do-i-have-to-perform-two-step-verification-can-i-turn-it-off"></a>为何需要执行双重验证？ 是否可以关闭它？

双重验证是组织选择用来保护帐户的一项安全功能。 它比单纯使用密码更安全，因为它依赖于两种形式的身份验证：你知道的事情和拥有的物品。 你知道的事情就是你的密码。 你拥有的物品就是你经常携带的手机或设备。 如果使用双重验证保护帐户，则恶意黑客无法以你的身份登录，即使获取了你的密码也是如此。 他们无法登录是因为他们无法访问你的手机。 

Microsoft 提供双重验证，但组织可以选择性地使用该功能。 如果公司支持人员要求使用该功能，则你就不能放弃使用，就好像不能放弃使用密码来保护帐户一样。 

如果为个人 Microsoft 帐户启用了双重验证，但想要更改设置，请参阅[关于双重验证](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)。 

## <a name="i-dont-have-my-phone-with-me-today"></a>我今天没有带手机

有时，我们的手机可能会遗忘在家里，但工作时仍然需要登录。 在这种情况下，首先应该尝试使用其他验证方法登录。 注册双重验证时，是否设置了多个电话号码？ 若要尝试使用另一种方法登录，请执行以下步骤：

1. 如往常一样登录。
2. 双重验证页面打开时，请选择“使用其他验证选项”。

   ![其他验证方法](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. 选择要使用的验证选项。 
  - 如果无法访问任何一种替代验证方法，请联系公司支持人员，请求帮助登录到帐户。
  - 如果可以访问替代方法，请继续执行双重验证。

如果看不到“使用其他验证选项”链接，则意味着首次注册双重验证时未设置替代方法。 请联系公司支持人员，请求帮助登录到帐户。 登录后，请确保[管理设置](multi-factor-authentication-end-user-manage-settings.md)以添加其他验证方法供下一次使用。 

## <a name="i-lost-my-phone-or-got-a-new-number"></a>手机遗失或换了号码
有两种方法可以恢复帐户。 第一种方法是使用备用身份验证电话号码登录（前提是已设置此号码）。 第二种方法是请求公司支持人员清除设置。

如果手机已丢失或被盗，我们还建议你告知公司支持人员。 他们将需要重置你的应用密码，并清除任何已记住这些密码的设备。 

### <a name="use-an-alternate-phone-number"></a>使用备用手机号码
如果已在其他设备上设置多个验证选项（如辅助电话号码或验证器应用），可以使用其中一个验证选项登录。

若要使用备用电话号码登录，请执行以下步骤：

1. 如往常一样登录。
2. 当系统提示进一步验证帐户时，选择“使用其他验证选项”。
   
   ![其他验证方法](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

3. 选择可以访问的电话号码或设备。
4. 恢复帐户后，通过[管理设置](multi-factor-authentication-end-user-manage-settings.md)更改身份验证电话号码。

### <a name="clear-your-settings"></a>清除设置
如果未配置辅助身份验证电话号码，则需要联系公司支持人员获取帮助。 请他们清除设置，以便在下次登录时重新收到[注册双重验证](multi-factor-authentication-end-user-first-time.md)的提示。

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>我的手机上未收到短信或来电
尝试登录时未收到短信或来电的原因有多种。 如果过去在手机上成功收到短信或来电，则此问题可能是手机提供商的问题，而不是帐户出现问题。 请确保手机信号良好。 如果尝试接收短信，请确保能够收到短信。 请求好友给你拨打电话或发送短信，以测试接收状况。 

如果在等待几分钟后仍未收到短信或来电，进入帐户的最快方式是尝试其他选项。

1. 在等待验证的页面上选择“使用其他验证方法”
   
    ![其他验证方法](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 选择要使用的电话号码或传递方法。
   
    如果收到多个验证码，请使用最新的一个。

如果未配置其他方法，请联系公司支持人员让其清除设置。 下次登录时，系统将重新提示[设置多重身份验证](multi-factor-authentication-end-user-first-time.md)。

如果经常由于手机信号不佳导致延迟，建议在智能手机上使用 [Microsoft Authenticator 应用](microsoft-authenticator-app-how-to.md)。 应用可以生成用于登录的随机安全代码，这些代码可在没有任何手机信号或 Internet 连接的情况下生成。

## <a name="app-passwords-are-not-working"></a>应用密码不起作用
首先，请确保正确输入应用密码。 生成的应用密码会替换普通的密码，但这只适用于不支持双重验证的旧式桌面应用程序。 如果仍然不起作用，请尝试登录并[创建新的应用密码](multi-factor-authentication-end-user-app-passwords.md)。  如果还是不起作用，请联系公司支持人员，让其[删除现有应用密码](../multi-factor-authentication-manage-users-and-devices.md)，然后便可以新建密码。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答。
如果已尝试这些故障排除步骤但仍遇到问题，请联系公司支持人员。 他们应该能够提供帮助。

## <a name="related-topics"></a>相关主题
* [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator 应用程序常见问题](microsoft-authenticator-app-faq.md)

