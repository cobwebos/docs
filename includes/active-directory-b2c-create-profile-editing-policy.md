---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
origin.date: 11/30/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456026"
---
如果希望在应用程序上启用配置文件编辑，请使用“配置文件编辑”用户流。 此用户流描述了客户在编辑配置文件的过程中将经历的体验以及应用程序在成功完成时会接收到的令牌内容。

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

在“管理”下，选择“用户流”，单击“+ 新建用户流”。

![选择“新建用户流”](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

在“建议”选项卡上，选择“配置文件编辑”。

输入供应用程序引用的用户流名称。 例如，输入 `SiPe`。

在“标识提供者”下，选中“本地帐户登录”。 或者，也可以选择社交标识提供者（如果已配置）。

![以标识提供者身份选择“本地帐户登录”，然后单击“确定”按钮](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

在“用户属性”下单击“显示更多”。 在“收集属性”列中，选择使用者可以在其配置文件中查看和编辑的属性。 例如，选中“国家/地区”、“显示名称”和“邮政编码”。

在“返回声明”列中，选择需要在成功获得配置文件编辑体验后发回到应用程序的授权令牌中返回的声明。 例如，选择“显示名称”、“邮政编码”。

单击“确定”。

![选择一些应用程序声明，然后单击“确定”按钮](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

单击“创建”以添加用户流。 该用户流将列出为 **B2C_1_SiPe**。 B2C_1_ 前缀追加到名称。

选择“运行用户流”。 验证表中指定的设置，然后单击“运行用户流”。

![选择用户流并运行它](./media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| 设置      | Value  |
| ------------ | ------ |
| **应用程序** | Contoso B2C 应用 |
| 回复 URL | `https://localhost:44316/` |

此时会打开新的浏览器选项卡，可以根据配置验证“配置文件编辑”用户体验。

> [!NOTE]
> 用户流创建和更新最多需要一分钟才能生效。
>

