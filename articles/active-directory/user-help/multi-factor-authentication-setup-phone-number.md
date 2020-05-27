---
title: 将移动设备设置为双因素验证方法 - Azure Active Directory | Microsoft Docs
description: 了解如何将移动设备设置为双因素验证方法。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 76809a41400502ec48c2dd674f8976bb168ec9f9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83735984"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>将移动设备设置为双因素验证方法

可以设置移动设备，使其充当双因素验证方法。 你的移动电话可以接收带有验证码的短信，或接收电话呼叫。

>[!Note]
> 如果“身份验证电话”选项灰显，则可能是你的组织不允许你使用电话号码或短信进行验证。 在这种情况下，需选择另一种方法，或与管理员联系以获取进一步帮助。

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>将移动设备设置为使用短信作为验证方法

1. 在“其他安全验证”  页上，选择“身份验证电话”  （从“第 1 步:  我们如何与您联系”区域），从下拉列表中选择你所在的国家或地区，然后键入移动设备电话号码。

2. 从“方法”  区域中选择“通过文本消息向我发送代码”  ，然后选择“下一步”  。

    ![“其他安全验证”页，其中显示身份验证电话和文本消息](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. 将 Microsoft 发送的文本消息中的验证码键入“第 2 步:  我们已将文本消息发送至您的手机”区域，然后选择“验证”  。

    ![“其他安全验证”页，其中显示身份验证电话和文本消息](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. 从“第 3 步:  继续使用你的现有应用程序”区域，复制提供的应用密码，并将其粘贴到安全位置。

    ![“其他安全验证”页的“应用密码”区域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >有关如何将应用密码用于旧应用的信息，请参阅[管理应用密码](multi-factor-authentication-end-user-app-passwords.md)。 如果要继续使用不支持双重验证的旧应用，只需使用应用密码即可。

5. 选择“完成”  。

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>设置移动设备以接收电话呼叫

1. 在“其他安全验证”  页上，选择“身份验证电话”  （从“第 1 步:  我们如何与您联系”区域），从下拉列表中选择你所在的国家或地区，然后键入移动设备电话号码。

2. 从“方法”  区域选择“呼叫我”  ，然后选择“下一步”  。

    ![“其他安全验证”页，其中显示身份验证电话和电话呼叫](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. 你将收到来自 Microsoft 的电话呼叫，要求你按移动设备上的井号 (#) 来验证你的身份。

    ![测试指定的电话号码](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. 从“第 3 步:  继续使用你的现有应用程序”区域，复制提供的应用密码，并将其粘贴到安全位置。

    ![“其他安全验证”页的“应用密码”区域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >有关如何将应用密码用于旧应用的信息，请参阅[管理应用密码](multi-factor-authentication-end-user-app-passwords.md)。 如果要继续使用不支持双重验证的旧应用，只需使用应用密码即可。

5. 选择“完成”  。

## <a name="next-steps"></a>后续步骤

设置双重验证方法后，可以添加其他方法、管理设置和应用密码、登录，或获得一些常见双重验证相关问题的帮助。

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [管理应用密码](multi-factor-authentication-end-user-app-passwords.md)

- [使用双重验证登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)
