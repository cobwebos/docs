---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: b83a6b9185eb4ef127da1dd3b55aba4e8b2945f9
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326309"
---
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择“应用程序”，然后选择“添加”。
1. 输入应用程序的名称。 例如，“nativeapp1”。
1. 对于**Native client**，请选择 **"是"** 。
1. 使用唯一的方案输入**自定义重定向 URI** 。 例如， `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 。 选择重定向 URI 时，有两个重要的注意事项：
    1. **唯一**：重定向 URI 的方案对于每个应用程序都必须是唯一的。 在示例 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 中，`com.onmicrosoft.contosob2c.exampleapp` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户选择不正确，登录会失败。
    1. **完整**：重定向 URI 必须同时具有方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//oauth` 失败时 `//oauth/`。 不要在 URI 中包含特殊字符，例如，下划线。
1. 选择“创建”。
