---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2019
ms.locfileid: "58116091"
---
若要在应用程序上启用细化密码重置，请使用**密码重置**用户流。 请注意，租户范围的密码重置选项是在[此处](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)指定的。 此用户流描述了客户在密码重置过程中将经历的体验以及应用程序在成功完成时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在“管理”下，选择“用户流”，单击“+ 新建用户流”。

![选择“新建用户流”](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

在“建议”选项卡上，选择“密码重置”。

输入供你的应用程序引用的用户流名称。 例如，输入 `SSPR`。

在“标识提供者”下，选中“使用电子邮件地址重置密码”。

![输入名称并选择“使用电子邮件地址重置密码”作为标识提供者](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

在“应用程序声明”下，单击“显示更多”并选择需要在授权令牌中返回的声明，该令牌在你成功获得密码重置体验后会发回到应用程序。 例如，选择“用户的对象 ID”。

单击“确定”。

![选择一些应用程序声明，然后单击“确定”按钮](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

单击“创建”以添加用户流。 该用户流将列出为 **B2C_1_SSPR**。 B2C_1_ 前缀追加到名称。

单击“运行用户流”。 验证在表中指定的设置，然后单击“运行用户流”。

![选择用户流并运行它](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| 设置      | 值  |
| ------------ | ------ |
| **应用程序** | Contoso B2C 应用 |
| 选择回复 url | `https://localhost:44316/` |

打开新的浏览器选项卡后，即可在应用程序中验证密码重置使用者体验。

> [!NOTE]
> 策略创建和更新最多需要一分钟才能生效。
>
