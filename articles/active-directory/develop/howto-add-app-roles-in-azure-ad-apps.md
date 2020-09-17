---
title: 添加应用角色并从令牌获取它们 | Azure
titleSuffix: Microsoft identity platform
description: 了解如何将应用程序角色添加到 Azure Active Directory 中注册的应用程序，将用户和组分配到这些角色，并在令牌的 "roles" 声明中接收这些角色。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 02fe3c1ebc893dea259abcda3ea1d13ab96d68d5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90706007"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>如何：在应用程序中添加应用角色并在令牌中接收它们

基于角色的访问控制 (RBAC) 是一种常用的机制，用于在应用程序中强制进行授权。 使用 RBAC 时，管理员将权限授予角色而不是单个用户或组。 然后，管理员再将角色分配给不同的用户和组，以便控制用户对特定内容和功能的访问。

使用 RBAC 和应用程序角色和角色声明，开发人员可以在其应用程序中安全地实施授权。

另一种方法是使用 Azure AD 组和组声明，如 GitHub 上的 [WebApp-webapp-groupclaims-dotnet-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) 代码示例中所示。 Azure AD 组和应用程序角色不相互排斥;它们可一起使用，以提供更精细的访问控制。

## <a name="declare-roles-for-an-application"></a>为应用程序声明角色

应用程序角色在 [Azure 门户](https://portal.azure.com)中定义。  当用户登录到应用程序时，Azure AD 会针对每个角色发出一个 `roles` 声明。这些角色包括单独授予用户的，以及通过组成员身份获得的。  可以通过门户的 UI 为用户或组分配角色，也可以使用 [Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) 以编程方式进行分配。

若要创建应用角色：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 " **目录 + 订阅** " 筛选器，然后选择包含要向其添加应用程序角色的应用注册的 Azure Active Directory 租户。
1. 搜索并选择“Azure Active Directory”  。
1. 在 " **管理**" 下，选择 " **应用注册**"，然后选择要在其中定义应用角色的应用程序。
1. 选择 **应用角色 |预览**"，然后选择" **创建应用角色**"。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure 门户中的应用注册应用程序角色窗格":::
1. 在 " **创建应用角色** " 窗格中，输入角色的设置。 图像后的表描述了每个设置及其参数。

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="应用注册的应用角色在 Azure 门户中创建上下文窗格":::

    | 字段 | 描述 | 示例 |
    |-------|-------------|---------|
    | **显示名称** | 显示在管理员同意和应用程序分配体验中的应用角色的显示名称。 此值可以包含空格。  | `Survey Writer` |
    | **允许的成员类型** | 指定是否可以将此应用角色分配给用户、应用程序或两者。 | `Users/Groups` |
    | **值** | 指定应用程序在令牌中应具有的角色声明的值。 该值应与应用程序代码中引用的字符串完全匹配。 该值不能包含空格。 | `Survey.Create` |
    | **说明** | 在管理应用分配和许可体验过程中显示的应用角色的更详细说明。 | `Writers can create surveys.` |
    | **是否要启用此应用角色？** | 应用角色是否已启用。 若要删除应用角色，请取消选择此复选框，并在尝试删除操作之前应用更改。 | *已选中* |

1. 选择“应用”以保存所做的更改。

> [!NOTE]
> 添加的角色数将计入为应用程序清单定义的限制。 有关这些限制的详细信息，请参阅[Azure Active Directory 应用程序清单参考](reference-app-manifest.md)中的 "[清单限制](./reference-app-manifest.md#manifest-limits)" 部分。

## <a name="assign-users-and-groups-to-roles"></a>将用户和组分配到角色

在应用程序中添加应用角色后，可以将用户和组分配给角色。

1. 在 Azure 门户的 **Azure Active Directory** 中，在左侧导航菜单中选择 " **企业应用程序** "。
1. 选择“所有应用程序”，查看所有应用程序的列表。

     如果你的应用程序未显示在列表中，请使用 " **所有应用程序** " 列表顶部的筛选器限制列表，或向下滚动列表以找到你的应用程序。

1. 选择一个应用程序，以便在其中为角色分配用户或安全组。
1. 在“管理”下，选择“用户和组”。********
1. 选择 " **添加用户** " 以打开 " **添加分配** " 窗格。
1. 在“添加分配”窗格中，选择“用户和组”选择器。

     将显示用户和安全组的列表。 可以搜索特定的用户或组，并选择列表中显示的多个用户和组。

1. 选择用户和组后，选择 " **选择** " 按钮以继续。
1. 在 "**添加分配**" 窗格中选择 "**选择角色**"。 将显示已为应用程序定义的所有角色。
1. 选择一个角色并选择 " **选择** " 按钮。
1. 选择 " **分配** " 按钮，完成将用户和组分配到应用的操作。
1. 确认你添加的用户和组出现在 " **用户和组** " 列表中。

## <a name="receive-roles-in-tokens"></a>接收令牌中的角色

当分配到各种应用角色的用户登录到应用程序时，其令牌会在 `roles` 声明中具有其分配的角色。

## <a name="web-api-application-permissions"></a>Web API 应用程序权限

你可以定义面向**用户/组**和 **/** 或**应用程序**的应用角色。 选择 " **应用程序** " 或 **两者**时，应用程序角色会在客户端应用的 **API 权限**中显示为应用程序权限。

定义以 **应用程序** 为目标的角色后，若要在客户端应用注册中选择应用程序权限， **请执行以下**操作：

1. 在 "Azure 门户 **Azure Active Directory** 中，选择" **应用注册**"，然后选择客户端应用的注册。
1. 在“管理”下选择“API 权限”。
1. 选择 "**添加我的 api 的权限**  >  **"**。
1. 选择要向其添加应用程序角色的应用，然后选择 " **应用程序权限**"。

## <a name="next-steps"></a>后续步骤

详细了解包含以下资源的应用角色：

- 代码示例： [使用应用角色 & 角色声明添加到 ASP.NET Core web 应用 (GitHub 的授权](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)) 
- 视频： [在应用程序中通过 Microsoft 标识平台实现授权 ](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15) 
- [Azure Active Directory 应用清单](./reference-app-manifest.md)
- [Azure AD 访问令牌](access-tokens.md)
- [Azure AD `id_tokens`](id-tokens.md)
