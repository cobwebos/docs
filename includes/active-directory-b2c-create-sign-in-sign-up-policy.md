---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742530"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在“管理”下，选择“用户流”，单击“+ 新建用户流”。

![选择“新建用户流”](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

在“建议”选项卡上，选择“注册和登录”。

![选择“注册和登录”用户流](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

输入供你的应用程序引用的用户流名称。 例如，输入 `SiUpIn`。

在“标识提供者”下，选中“电子邮件注册”。 或者，也可以选择社交标识提供者（如果已配置）。

在“多重身份验证”下，选择“启用”或“禁用”。

![输入一个名称并选择“电子邮件注册”作为标识提供者](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

在“用户特性和声明”下，选择“显示更多”以查看你可以从中进行选择的特性和声明的完整列表。

在“收集特性”列中，选择要在注册期间从客户收集的特性。 例如，选中“国家/地区”、“显示名称”和“邮政编码”。

在“返回声明”列中选择需要在授权令牌中返回的声明，该令牌在成功获得注册或登录体验后会发回到应用程序。 例如，选择“显示名称”、“标识提供者”、“邮政编码”、“用户是新用户”和“用户的对象 ID”。

单击“确定”。

![选择一些用户特性和声明，然后单击“确定”按钮](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

单击“创建”以添加用户流。 该用户流将列出为 **B2C_1_SiUpIn**。 B2C_1_ 前缀追加到名称。

选择“运行用户流”。 验证在表中指定的设置，然后单击“运行用户流”。

![选择“运行用户流”](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| 设置      | 值  |
| ------------ | ------ |
| **应用程序** | Contoso B2C 应用 |
| 回复 URL | `https://localhost:44316/` |

此时会打开新的浏览器选项卡，可以根据配置验证“注册或登录”用户体验。

> [!NOTE]
> 用户流创建和更新最多需要一分钟才能生效。
>