---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: bdc9c8f5a782704ec7f8cbaa31dd4cc27ed376cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468956"
---
若要在 Azure AD B2C 租户中注册应用程序，可以使用当前的“应用程序”体验，或者使用我们新推出的统一“应用注册(预览版)”体验   。 [详细了解预览版体验](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“应用程序”，然后选择“添加”   。
1. 输入应用程序的名称。 例如，“nativeapp1”  。
1. 对于“本机客户端”，请选择“是”   。
1. 输入使用唯一方案的“自定义重定向 URI”  。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 选择重定向 URI 时，有两个重要的注意事项：
    * **唯一**：每个应用程序的重定向 URI 的方案必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户选择不正确，登录会失败。
    * **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//oauth/` 有效而 `//oauth` 失败。 请勿在 URI 中包含特殊字符（例如，下划线）。
1. 选择“创建”  。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 选择“应用注册(预览版)”，然后选择“新建注册”   。
1. 输入应用程序的“名称”  。 例如，“nativeapp1”  。
1. 在“支持的帐户类型”下，选择“任何组织目录中或任何标识提供者中的帐户”   。
1. 在“重定向 URI”下，使用下拉选择“公共客户端/本机(移动和桌面)”   。
1. 输入使用唯一方案的重定向 URI。 例如，`com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 选择重定向 URI 时，有两个重要的注意事项：
    * **唯一**：每个应用程序的重定向 URI 的方案必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户选择不正确，登录会失败。
    * **完整**：重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//oauth/` 有效而 `//oauth` 失败。 请勿在 URI 中包含特殊字符（例如，下划线）。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框   。
1. 选择“注册”  。