---
title: "Microsoft Authenticator 手机登录 - Azure 和 Microsoft 帐户 | Microsoft Docs"
description: "使用手机登录 Microsoft 帐户，而不是键入密码。 本文提供有关此功能的常见问题解答。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: librown
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: kgremban
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: ec52f719f33db0d893d707b59293fa75a8ff4314
ms.openlocfilehash: 033392783fd98cbdb23fd0254c93687754808286
ms.lasthandoff: 02/10/2017

---
# <a name="sign-in-with-your-phone-not-your-password"></a>使用手机（而不是密码）登录

通过在输入密码后执行双重验证，Microsoft Authenticator 应用可帮助你确保帐户安全。 但你是否知道，它能完全取代你的 Microsoft 个人帐户密码？ 

iOS 和 Android 设备提供此功能，此功能适用于 Microsoft 个人帐户。 

## <a name="how-it-works"></a>工作原理

在登录 Microsoft 帐户时，许多人会将 Microsoft Authenticator 应用用于双重验证。 键入密码，然后转到应用以批准通知或获取验证码。 使用手机登录时，会跳过密码，并在手机上完成所有身份验证。 这与双重验证的原理相同，手机登录将请求你提供某条你知道的信息和某件你拥有的物品。 你拥有的物品仍是手机，但此时请求你输入手机的 PIN 或生物识别密钥作为你知道的信息。 

## <a name="how-to-get-started"></a>如何入门

要使用手机登录 Microsoft 个人帐户，请执行以下步骤： 

1. 为帐户启用手机登录。 

  - 如果还没有 Microsoft Authenticator 应用，请按照 [Microsoft Authenticator 页](microsoft-authenticator-app-how-to.md)中步骤安装和添加 Microsoft 个人帐户。 新添加的帐户将自动启用，因此请放心执行后续操作。

  - 如果已将 Microsoft Authenticator 用于双重验证，请从应用主页选择帐户，并选择“从下拉菜单启用手机登录”。

  >[!NOTE] 
  >为保护帐户，需要在设备上安装 PIN 或生物识别锁。 如果手机保持未锁定状态，应用将弹出请求，请求你在启用手机登录前设置锁。 

3. 大多数通常用于输入 Microsoft 帐户密码的页面都有一个链接，显示“改用应用”。 选择此选项可使用手机登录。 

4. Microsoft 向手机发送通知。 批准该通知可登录到你的帐户。   

## <a name="faq"></a>常见问题 

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>为什么使用手机登录比键入密码更安全？  

当今，大多数人在登录网站或应用时，使用用户名和密码。  遗憾的是，密码常常会丢失、被盗或被黑客猜中。 当你设置 Microsoft Authenticator 应用来登录时，我们在你的手机上生成一个秘钥，此密钥可解锁你的帐户。 通过已用于你的手机的 PIN 或生物识别来保护此秘钥。  使用手机登录时，此秘钥通过以下两个因素安全地证明你的身份：手机本身和你解锁手机的能力。 

所使用的秘钥与在 Windows Hello 和 FIDO Alliance UAF 规范中使用的秘钥类似。 你的生物数据仅用于在本地保护秘钥，绝不会被发送或存储到云。 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>哪些地方可以用手机取代密码，哪些地方仍需要使用密码？  

目前手机登录功能仅适用于由 Microsoft 个人帐户提供支持的 Web 应用和服务、使用 Microsoft 个人帐户的 iOS 或 Android 应用以及使用 Microsoft 个人帐户的 Windows 10 应用。 登录这些网站或应用时，通常输入密码的页面上会有显示为“改用应用”的链接。 

目前，无法使用手机登录解锁 Windows 电脑、XBOX 或任何 Microsoft 应用的桌面版（如 Office 应用）。 
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>这是否会取代双重验证？ 我是否应将其关闭？   

有时。 我们正努力扩展手机登录的适用范围，但目前在 Microsoft 生态系统中仍有不支持它的情景。 在这些情景中，仍需要使用双重验证进行安全登录。 因此，不应关闭帐户的双重验证。 
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-will-i-have-to-approve-two-notifications"></a>如果启用帐户的双重验证，是否必须批准两个通知？

不必。 使用手机登录到 Microsoft 帐户被视为双重验证。 可通过解锁手机然后批准通知来证明身份，无需键入密码然后批准通知。 不会向你发送要求批准的第二条通知。

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>如果手机遗失或未随身携带，应如何访问我的帐户？  

可以单击登录页中的“改用密码”，切换回使用密码登录。 请记住，如果使用双重验证，则仍需验证登录的第二种方法。 因此，强烈建议确保你的帐户具有最新的额外安全信息。 可在 https://account.live.com/proofs/manage 管理安全信息。 
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>如何停用此功能，并切换回通过输入密码登录？

在登录时单击“改用密码”。 我们将记住你最近的选择，在你下次登录时默认提供此选项。 若要切换回使用手机登录，单击“改用应用”。 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>是否可用此应用登录我所有的 Microsoft 帐户？   
此功能目前仅适用于 Microsoft 个人帐户。 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>是否可使用手机登录我的电脑？  
对于电脑，建议使用 Windows 10 中的 Windows Hello，通过人脸、指纹或 PIN 进行登录。   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>可否使用 Windows Phone 登录？  
目前，没有为 Windows Phone 上的 Microsoft Authenticator 开发此功能。 

## <a name="next-steps"></a>后续步骤
如果尚未下载 Microsoft Authenticator 应用，请查看。 此应用适用于 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)，用于 [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 的 Microsoft Authenticator 应用提供手机登录功能。

如果你有关于此应用的常见问题，请参阅 [Microsoft Authenticator 常见问题解答](microsoft-authenticator-app-faq.md)

