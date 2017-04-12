---
title: "双重验证疑难解答 |Microsoft 文档"
description: "本文档向用户提供有关如何解决 Azure Multi-Factor Authentication 问题的信息。"
services: multi-factor-authentication
keywords: "multifactor authentication 客户端, 身份验证问题, 相关性 ID"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: kgremban
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 2eab76ba42955616ae4d6909c1568f03da0c1819
ms.lasthandoff: 12/22/2016

---
# <a name="having-trouble-with-two-step-verification"></a>使用双重验证时遇到问题
本文讨论用户在使用双重验证时可能遇到的一些问题。 如果此处未包含你所遇到的问题，请在评论部分提供详细反馈，以便我们改进。

## <a name="i-lost-my-phone-or-it-was-stolen"></a>我的手机已丢失或被盗
有两种方法可以恢复帐户。 第一种方法是使用备用身份验证电话号码登录（前提是已设置此号码）。 第二种方法是请求管理员清除设置。

如果手机已丢失或被盗，我们还建议你请管理员重置应用密码并清除所有已记住的设备。 如果管理员不确定如何实现此目的，请他们参阅此文：[管理用户和设备](../multi-factor-authentication-manage-users-and-devices.md)。

### <a name="use-an-alternate-phone-number"></a>使用备用手机号码
如果已在其他设备上设置多个验证选项（包括辅助电话号码或身份验证应用），可以使用其中一种登录。

若要使用备用电话号码登录，请执行以下步骤：

1. 如往常一样登录。
2. 当系统提示你进一步验证帐户时，选择“使用其他验证选项”。
   
    ![其他验证方法](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. 选择有权访问的电话号码。
   
    ![备用号码](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. 恢复帐户后，通过[管理设置](multi-factor-authentication-end-user-manage-settings.md)更改身份验证电话号码。

> [!IMPORTANT]
> 必须配置辅助身份验证电话号码。 如果主电话号码和移动应用在同一手机上，在手机丢失或被盗后，需要使用第三种选项。   

### <a name="clear-your-settings"></a>清除设置
如果未配置辅助身份验证电话号码，需要联系管理员获取帮助。 请管理员清除设置，以便在下次登录时重新收到[设置帐户](multi-factor-authentication-end-user-first-time.md)的提示。

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>我的手机上未收到短信或来电
尝试登录时未收到短信或来电的原因有多种。 如果过去在手机上成功收到短信或来电，则可能是手机提供商的问题，而不是帐户出现问题。 请确保手机信号良好，如果尝试接收短信，请确保手机和服务计划支持短信。

如果在等待几分钟后仍未收到短信或来电，进入帐户的最快方式是尝试其他选项。

1. 在等待验证的页面上选择“使用其他验证方法”
   
    ![其他验证方法](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. 选择要使用的电话号码或传递方法。
   
    如果收到多个验证码，只有最新的一个验证码有效。

如果未配置其他方法，请联系管理员清除设置。 下次登录时，系统将重新提示[设置多重身份验证](multi-factor-authentication-end-user-first-time.md)。

如果经常由于手机信号不佳导致延迟，建议在智能手机上使用 [Microsoft Authenticator 应用](microsoft-authenticator-app-how-to.md)。 应用可以生成用于登录的随机安全代码，这些代码可在没有任何手机信号或 Internet 连接的情况下生成。

## <a name="app-passwords-are-not-working"></a>应用密码不起作用
首先，请确保正确输入应用密码。  如果仍然无法解决问题，请尝试登录并[创建新的应用密码](multi-factor-authentication-end-user-app-passwords.md)。  如果还是不起作用，请联系管理员[删除现有应用密码](../multi-factor-authentication-manage-users-and-devices.md)，然后便可以新建密码。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到问题的解答。
如果已尝试执行这些疑难解答步骤，但仍遇到问题，请联系管理员或设置多重身份验证的人员。 它们应该能够提供帮助。

此外，还可以在 [Azure AD 论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)上发布问题，或[联系支持人员](https://support.microsoft.com/contactus)，我们将尽快答复。

如果联系支持人员，请包含以下信息：

* **用户 ID** – 尝试登录时使用的电子邮件地址。
* **错误的一般描述** – 实际看到的错误消息。  如果没有任何错误消息，请详细描述你所发现的意外行为。
* **页面** – 看到错误时所在的页面（包括 URL）。
* **错误代码** - 收到的特定错误代码。
* **会话 ID** - 收到的特定会话 ID。
* **相关性 ID** – 用户看到错误时所生成的相关性 ID 代码。
* **时间戳** – 看到错误时的精确日期和时间（包括时区）。

其中许多信息都位于登录页面。 如果未及时验证登录，请选择“查看详细信息”。

![登录错误详细信息](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

包含这些信息有助于我们尽快解决问题。

## <a name="related-topics"></a>相关主题
* [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)  
* [Microsoft Authenticator 应用程序常见问题](microsoft-authenticator-app-faq.md)


