---
title: 将办公室电话设置为双因素验证方法 - Azure Active Directory | Microsoft Docs
description: 了解如何将办公室电话设置为双因素验证方法。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adbb913fc8ec6376f1c5f47708da4fd7df740ff
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619848"
---
# <a name="set-up-an-office-phone-as-your-two-factor-verification-method"></a>将办公室电话设置为双因素验证方法

可以设置办公室电话，使其充当双因素验证方法。

>[!Note]
> 如果“办公室电话”选项灰显，则可能是你的组织不允许你使用办公室电话号码进行验证。 在这种情况下，需选择另一种方法，或与管理员联系以获取进一步帮助。

## <a name="set-up-your-office-phone-number-as-your-verification-method"></a>将办公室电话号码设置为验证方法

1. 在“其他安全验证”  页上，选择“办公室电话”  （从“第 1 步:  我们如何与您联系”区域），从下拉列表中选择你所在的国家或地区，键入办公室电话号码，然后键入分机（如果有）。

    ![“其他安全验证”页，其中显示身份验证电话和电话呼叫](media/multi-factor-authentication-verification-methods/multi-factor-authentication-office-phone.png)

2. 你将收到来自 Microsoft 的电话呼叫，要求你按办公室电话上的井号 (#) 来验证你的身份。

    ![测试指定的电话号码](media/multi-factor-authentication-verification-methods/multi-factor-authentication-office-phone-test.png)

3. 从“第 3 步:  继续使用您的现有应用程序”区域，复制提供的应用密码，并将其粘贴到安全位置。

    ![“其他安全验证”页的“应用密码”区域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >有关如何将应用密码用于旧应用的信息，请参阅[管理应用密码](multi-factor-authentication-end-user-app-passwords.md)。 如果要继续使用不支持双因素验证的旧应用，只需要使用应用密码。

4. 选择“完成”  。

## <a name="next-steps"></a>后续步骤

设置双因素验证方法后，可以添加其他方法、管理设置和应用密码、进行登录，或获得一些常见双因素验证相关问题的帮助。

- [管理双因素验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [管理应用密码](multi-factor-authentication-end-user-app-passwords.md)

- [使用双因素验证进行登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)
