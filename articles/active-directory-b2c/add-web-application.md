---
title: 添加 Web 应用程序 - Azure Active Directory B2C | Microsoft Docs
description: 了解如何向 Active Directory B2C 租户添加 Web 应用程序。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757302"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>向 Azure Active Directory B2C 租户添加 Web API 应用程序

Web API 资源需要先在租户中注册，然后才能接受并响应通过提供访问令牌的客户端应用程序所提出的受保护资源请求。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称。 例如，“webapi1”。
6. 对于“包括 Web 应用/Web API”和“允许隐式流”，请选择“是”。
7. 对于“回复 URL”，请输入 Azure AD B2C 要将应用程序请求的任何令牌返回到的终结点。 在本教程中，示例在本地运行并在 `https://localhost:44332` 上进行侦听。
8. 对于“应用 ID URI”，请输入 Web API 使用的标识符。 包括域在内的完整标识符 URI 是为你生成的。 例如，`https://contosotenant.onmicrosoft.com/api`。
9. 单击“创建”。
10. 在属性页上，记录在配置 Web 应用程序时要使用的应用程序 ID。

## <a name="configure-scopes"></a>配置范围

可通过范围控制对受保护资源的访问。 Web API 使用作用域实施基于作用域的访问控制。 例如，可以让 Web API 用户拥有读取和写入访问权限，或者只拥有读取访问权限。 在本教程中，请使用作用域为 Web API 定义读取和写入权限。

1. 选择“应用程序”，然后选择“webapi1”。
2. 选择“已发布的范围”。
3. 在“范围”中输入 `Hello.Read`，在“说明”中输入 `Read access to hello`。
4. 在“范围”中输入 `Hello.Write`，在“说明”中输入 `Write access to hello`。
5. 单击“ **保存**”。

可以使用已发布的范围向客户端应用程序授予访问 Web API 的权限。

## <a name="grant-permissions"></a>授予权限

若要从应用程序调用受保护的 Web API，需授予应用程序访问该 API 的权限。 在先决条件教程中，已在 Azure AD B2C 中创建名为 *webapp1* 的 Web 应用程序。 使用此应用程序调用 Web API。

1. 选择“应用程序”，然后选择你的 Web 应用程序。
2. 选择“API 访问”，然后选择“添加”。
3. 在“选择 API”下拉列表中，选择“webapi1”。
4. 在“选择范围”下拉列表中，选择之前定义的“Hello.Read”和“Hello.Write”范围。
5. 单击“确定”。

注册应用程序以调用受保护的 Web API。 用户通过 Azure AD B2C 进行身份验证，以便使用该应用程序。 应用程序从 Azure AD B2C 获取授权，以访问受保护的 Web API。