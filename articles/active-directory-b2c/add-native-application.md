---
title: 添加本机客户端应用程序 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何向 Active Directory B2C 租户添加本机客户端应用程序。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 3f988e61c152be820e7e490e13908fb4ab54e6c5
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673386"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>向 Azure Active Directory B2C 租户添加本机客户端应用程序

需要先在租户中注册本机客户端资源，然后应用程序才能与 Azure Active Directory B2C 通信。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
1. 选择“应用程序”，然后选择“添加”。
2. 输入应用程序的名称。 例如，“nativeapp1”。
3. 对于“包括 Web 应用/Web API”，请选择“否”。
4. 对于“包括本机客户端”，请选择“是”。
5. 对于“重定向 URI”，请使用自定义方案输入有效的重定向 URI。 选择重定向 URI 时，有两个重要的注意事项：

    - **唯一** - 每个应用程序的重定向 URI 方案应是唯一的。 在示例 `com.onmicrosoft.contoso.appname://redirect/path` 中，`com.onmicrosoft.contoso.appname` 为方案。 应遵循此模式。 如果两个应用程序共享同一方案，则用户应选择一个应用程序。 如果用户的选择不正确，登录会失败。
    - **完整** - 重定向 URI 必须同时包含方案和路径。 路径必须在域之后包含至少一个正斜杠。 例如，`//contoso/` 有效，而 `//contoso` 会失败。 确保重定向 URI 不包含特殊字符，例如下划线。

6. 单击“创建”。
7. 在属性页上，记录在配置本机客户端应用程序时要使用的应用程序 ID。
