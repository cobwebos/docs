---
title: include 文件
description: include 文件
services: active-directory-b2c
author: davidmu1
ms.service: active-directory-b2c
ms.topic: include
ms.date: 04/09/2018
ms.author: davidmu
ms.custom: include file
ms.openlocfilehash: 8363d023e89c77aabc0d123f19264c9a0758a656
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

若要注册移动或本机应用程序，请使用表中指定的设置。

![新的移动或本机应用程序的注册设置示例](./media/active-directory-b2c-register-mobile-native-app/b2c-new-mobile-native-app-settings.png)

| 设置      | 示例值  | 说明                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Contoso B2C 应用 | 输入应用程序的“名称”，用于向使用者描述应用程序。 |
| 本机客户端 | 是 | 对于移动或本机应用程序，请选择“是”。 |
| 自定义重定向 URI | `com.onmicrosoft.contoso.appname://redirect/path` | 输入使用自定义方案的重定向 URI。 请确保选择[正确的重定向 URI ](../articles/active-directory-b2c/active-directory-b2c-app-registration.md)，且不能包含下划线等特殊字符。 |

单击“ **创建** ”以注册应用程序。

新注册的应用程序显示在 B2C 租户的应用程序列表中。 从列表中选择移动或本机应用。 此时会显示应用程序的属性窗格。

![应用程序属性](./media/active-directory-b2c-register-mobile-native-app/b2c-mobile-native-app-properties.png)

请记下全局唯一的应用程序客户端 ID。 该 ID 用在应用程序代码中。

如果本机应用程序调用受 Azure AD B2C 保护的 Web API，请执行以下步骤：
   1. 创建应用程序机密，方法是：转到“密钥”边栏选项卡，并单击“生成密钥”按钮。 记下“应用密钥”值。 在应用程序的代码中将该值用作应用程序机密。
   2. 单击“API 访问权限”，再单击“添加”，然后选择 Web API 和作用域（权限）。

> [!NOTE]
> **应用程序密钥** 是一个重要的安全凭据，应进行适当地保护。
> 
