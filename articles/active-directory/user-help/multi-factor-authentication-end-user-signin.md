---
title: 使用双重验证的 Azure MFA 登录 | Microsoft 文档
description: 本页将提供有关在何处查看 Azure MFA 支持的各种登录方法的指导。
keywords: 用户身份验证, 登录体验, 使用手机登录, 使用办公电话登录
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.component: user-help
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: e7886d3c8aa1ef9ee1e392030aabf0d187568e24
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343060"
---
# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>使用 Azure 多重身份验证时的登录体验
> [!NOTE]
> 本文的目的是了解典型的登录体验。 有关登录的帮助或者要排查问题，请参阅[使用 Azure 多重身份验证时遇到问题](multi-factor-authentication-end-user-troubleshoot.md)。

## <a name="what-will-your-sign-in-experience-be"></a>登录体验是怎样的？
根据所选择的第二重验证因素（拨打电话、身份验证应用或短信），登录体验将有所不同。 请选择最适当地描述了活动的选项：

| 如何登录？ |
| --- |
| [通过拨打我的手机或办公电话](#signing-in-with-a-phone-call) |
| [通过向我的手机发送短信](#signing-in-with-a-text-message)
| [使用来自 Microsoft 验证器应用的通知](#signing-in-with-the-microsoft-authenticator-app-using-notification) |
| [使用来自 Microsoft 验证器应用的验证代码](#signing-in-with-the-microsoft-authenticator-app-using-verification-code) |
| [使用备用方法，因为我现在无法使用首选方法](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>电话登录
以下信息介绍通过拨打手机或办公电话进行双重验证的体验。

1. 使用用户名和密码登录到 Office 365 等应用程序或服务。  
2. Microsoft 会向你拨打电话。  
3. 请接听电话并按 # 键。  

## <a name="signing-in-with-a-text-message"></a>短信登录
以下信息介绍通过向手机发送短信进行双重验证的体验：

1. 使用用户名和密码登录到 Office 365 等应用程序或服务。
2. Microsoft 会向你发送一条包含数字代码的短信。
3. 在登录页面上提供的框中输入代码。

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>使用 Microsoft 验证器应用登录
以下信息介绍使用 Microsoft Authenticator 应用进行双重验证的体验。 使用应用有两种不同方式。 可以在设备上接收推送通知，也可以打开应用以获取验证代码。

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>使用来自 Microsoft 验证器应用的通知登录
1. 使用用户名和密码登录到 Office 365 等应用程序或服务。
2. Microsoft 将通知发送到设备上的 Microsoft 验证器应用。

  ![Microsoft 发送通知](./media/multi-factor-authentication-end-user-signin/notify.png)

3. 打开手机上的通知，选择“验证”键。 如果公司需要 PIN，请在此处输入。
4. 现在你应已登录。

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>使用验证码通过 Microsoft Authenticator 应用登录

如果使用 Microsoft Authenticator 应用获取验证码，则打开应用时会在帐户名称下看到一个数字。 此数字每隔 30 秒更改一次，以便不会使用同一数字两次。 当系统提示你输入验证码时，请打开应用，并使用当前显示的数字。

1. 使用用户名和密码登录到 Office 365 等应用程序或服务。
2. Microsoft 会提示输入验证码。

  ![输入验证码](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. 打开手机上的 Microsoft Authenticator 应用，并在登录框中输入该代码。

## <a name="signing-in-with-an-alternate-method"></a>使用替代方法登录
有时，没有设为首选验证方法的手机或设备。 由于会有这种情况，因此我们建议为帐户设置备用方法。 以下部分介绍当主要方法不可用时如何使用替代方法进行登录。

1. 使用用户名和密码登录到 Office 365 等应用程序或服务。
2. 选择“使用其他验证选项”。 此时会显示不同的验证选项，具体将取决于设置了多少个选项。
3. 选择一种替代方法并登录。

  ![使用替代方法](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>后续步骤

如果使用双重验证登录时遇到问题，请在[使用 Azure 多重身份验证时遇到问题](multi-factor-authentication-end-user-troubleshoot.md)中获取详细信息。

了解如何[管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)。

了解如何[开始使用 Microsoft 验证器应用](microsoft-authenticator-app-how-to.md)，以便使用通知（而不是短信和电话呼叫）登录。
