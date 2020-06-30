---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 65fb406fa4f52f19b4d8e9dc4e3895207ffe6336
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2020
ms.locfileid: "84793680"
---
要在 Azure AD B2C 租户中注册应用程序，可以使用新的统一“应用注册”体验或旧版“应用程序(旧版)”体验 。 [详细了解此新体验](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用注册”，然后选择“新建注册” 。
1. 输入应用程序的“名称”。 例如，“nativeapp1”。
1. 在“支持的帐户类型”下，选择“任何组织目录中或任何标识提供者中的帐户” 。
1. 在“重定向 URI”下，使用下拉选择“公共客户端/本机(移动和桌面)” 。
1. 输入使用唯一方案的重定向 URI。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`。 选择重定向 URI 时，有以下重要的注意事项：
    * **开发**：对于开发用途，可将重定向 URI 设置为 `http://localhost`，Azure AD B2C 将遵循请求中的任何端口。 如果注册的 URI 包含端口，Azure AD B2C 将仅使用该端口。 例如，如果注册的重定向 URI 为 `http://localhost`，则请求中的重定向 URI 可为 `http://localhost:<randomport>`。 如果注册的重定向 URI 为 `http://localhost:8080`，则请求中的重定向 URI 必须为 `http://localhost:8080`。
    * **唯一**：每个应用程序的重定向 URI 的方案必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户选择不正确，登录会失败。
    * **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//oauth/` 有效而 `//oauth` 失败。 请勿在 URI 中包含特殊字符（例如，下划线）。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
2. 选择“注册”。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 选择“应用程序(旧版)”，然后选择“添加” 。
1. 输入应用程序的名称。 例如，“nativeapp1”。
1. 对于“本机客户端”，请选择“是” 。
1. 输入使用唯一方案的“自定义重定向 URI”。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`。 选择重定向 URI 时，有两个重要的注意事项：
    * **唯一**：每个应用程序的重定向 URI 的方案必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户选择不正确，登录会失败。
    * **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//oauth/` 有效而 `//oauth` 失败。 请勿在 URI 中包含特殊字符（例如，下划线）。
1. 选择“创建”。