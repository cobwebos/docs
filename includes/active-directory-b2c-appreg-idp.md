---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186347"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，或者使用我们新推出的统一“应用注册(预览版)”体验。 [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如， *testapp1-template.json*。
1. 对于“Web 应用/Web API”，请选择“是”。
1. 对于 "**答复 URL**"，请输入 `https://jwt.ms`
1. 选择“创建”。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册(预览版)”，然后选择“新建注册”。
1. 输入应用程序的“名称”。 例如， *testapp1-template.json*。
1. 选择**任何组织目录或任何标识提供者中的帐户**。
1. 在 "**重定向 URI**" 下，选择 " **Web**"，然后在 "URL" 文本框中输入 `https://jwt.ms`。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”。

完成应用程序注册后，启用隐式授权流：

1. 在“管理”下，选择“身份验证”。
1. 选择“尝试新体验”（如果已显示）。
1. 在“隐式授权”下，选中“访问令牌”和“ID 令牌”复选框。
1. 选择“保存”。