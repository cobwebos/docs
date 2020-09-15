---
title: 快速入门：注册并公开 Web API | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门将向 Microsoft 标识平台注册 Web API 并配置其范围，再将其公开到客户端，以便对 API 的资源进行基于权限的访问。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 72d66bd4c738ed60bbaefc123daae90ecc0db163
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442119"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>快速入门：配置应用程序以公开 Web API

本快速入门将向 Microsoft 标识平台注册 Web API 并通过添加示例范围将其公开到客户端应用。 通过注册 Web API 并通过范围将其公开，你可以向可访问 API 的已授权用户和客户端应用提供对其资源的基于权限的访问权限。

## <a name="prerequisites"></a>先决条件

* 具备有效订阅的 Azure 帐户 - [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入门：设置租户](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>注册 Web API

若要提供对 Web API 中资源的范围访问权限，首先需要向 Microsoft 标识平台注册该 API。

1. 执行以下文章的“注册应用程序”部分中的步骤：[快速入门：将应用注册到 Microsoft 标识平台](quickstart-register-app.md)。
1. 跳过“添加重定向 URI”和“配置平台设置”部分 。 无需为 Web API 配置重定向 URI，因为没有用户以交互方式进行登录。
1. 暂时跳过“添加凭据”部分。 仅当你的 API 访问下游 API 时，它才需要其自己的凭据，这是本文未涵盖的方案。

注册 Web API 后，便可以添加范围，API 的代码可使用这些范围向 API 的使用者提供具体权限。

## <a name="add-a-scope"></a>添加作用域

客户端应用程序中的代码通过将访问令牌及其请求传递到受保护的资源 (Web API) 来请求执行由 Web API 定义的操作的权限。 然后，仅当 Web API 接收的访问令牌包含操作所需的范围时，Web API 才会执行请求的操作。

首先，请按照以下步骤创建一个名为 `Employees.Read.All` 的示例范围：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false":::，以选择包含客户端应用的注册的租户。
1. 选择“Azure Active Directory” > “应用注册”，然后选择 API 的应用注册 。
1. 依次选择“公开 API” > “添加范围” 。

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Azure 门户中应用注册的“公开 API”窗格":::

1. 如果尚未配置应用程序 ID URI，系统会提示你设置一个。

   应用 ID URI 充当你将在 API 代码中引用的范围的前缀，它必须是全局唯一的。 你可以使用提供的默认值（格式为 `api://<application-client-id>`）或指定更具可读性的 URI，例如 `https://contoso.com/api`。

1. 接下来，在“添加范围”窗格中指定范围的属性。 对于本演练，你可以使用示例值或指定自己的值。

    | 字段 | 说明 | 示例 |
    |-------|-------------|---------|
    | **范围名称** | 范围的名称。 常用的范围命名约定为 `resource.operation.constraint`。 | `Employees.Read.All` |
    | **谁可以许可** | 此范围是由用户同意，还是由管理员同意。 若要获得更高特权，请选择“仅管理员”。  | 管理员和用户 |
    | **管理员许可显示名称** | 仅管理员可以看到的范围用途的简短说明。 | `Read-only access to Employee records` |
    | **管理员许可说明** | 仅管理员可以看到的范围所授予权限的更详细说明。 | `Allow the application to have read-only access to all Employee data.` |
    | **用户许可显示名称** | 范围用途的简短说明。 仅当你将“谁可以同意”设置为“管理员和用户”时才向用户显示 。 | `Read-only access to your Employee records` |
    | **用户许可说明** | 范围所授予权限的更详细说明。 仅当你将“谁可以同意”设置为“管理员和用户”时才向用户显示 。 | `Allow the application to have read-only access to your Employee data.` |

1. 将“状态”设置为“启用”，然后选择“添加范围”  。

1. （可选）若要取消向应用用户提示同意你定义的范围，可以预授权客户端应用程序访问 Web API。 请仅预授权所信任的客户端应用程序，因为用户不会有机会拒绝同意。
    1. 在“授权客户端应用程序”下，选择“添加客户端应用程序”
    1. 输入要预授权的客户端应用程序的“应用程序(客户端) ID”。 例如，以前注册的 Web 应用程序的 ID。
    1. 在“授权的范围”下，选择要取消显示许可提示的范围，然后选择“添加应用程序”。

    如果遵循此可选步骤，则客户端应用现在是预授权的客户端应用 (PCA)，系统在用户登录时不会提示用户同意。

## <a name="add-a-scope-requiring-admin-consent"></a>添加需要管理员同意的范围

接下来，添加另一个名为 `Employees.Write.All` 的示例范围，只有管理员才能同意该范围。 需要管理员同意的范围通常用于提供对更高特权的操作的访问权限，并且通常由作为后端服务运行的客户端应用程序或不以交互方式登录用户的后台程序使用。

若要添加 `Employees.Write.All` 示例范围，请按照 [添加范围](#add-a-scope) 部分中的步骤进行操作，并在“添加范围”窗格中指定以下值：

| 字段                          | 示例值                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **范围名称**                 | `Employees.Write.All`                                              |
| **谁可以许可**            | **仅管理员**                                                    |
| **管理员许可显示名称** | `Write access to Employee records`                                 |
| **管理员许可说明**  | `Allow the application to have write access to all Employee data.` |
| **用户许可显示名称**  | 无（留空）                                               |
| **用户许可说明**   | 无（留空）                                               |

## <a name="verify-the-exposed-scopes"></a>验证公开的范围

如果你已成功添加先前部分中所介绍的两个示例范围，则它们将显示在 Web API 的应用注册的“公开 API”窗格中，类似于以下内容：

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="“公开 API”窗格的屏幕截图，其中显示了两个公开的范围。":::

如图所示，范围的完整字符串由 Web API 的“应用程序 ID URI”与范围的“范围名称”串联而成 。

例如，如果 Web API 的应用程序 ID URI 为 `https://contoso.com/api`，范围名称为 `Employees.Read.All`，则完整范围为：

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>使用公开的范围

在本系列的下一篇文章中，你将按照本文中的步骤配置客户端应用的注册，其中包含对 Web API 的访问权限和你所定义的范围。

向客户端应用注册授予访问 Web API 的权限后，Microsoft 标识平台可以向客户端颁发 OAuth 2.0 访问令牌。 当客户端调用 Web API 时，它会提供一个访问令牌，令牌的范围 (`scp`) 声明设置为你在客户端的应用注册中所指定的权限。

以后可以根据需要公开其他范围。 请考虑 Web API 可以公开与多个操作关联的多个范围。 在运行时，资源可以通过评估其收到的 OAuth 2.0 访问令牌中的范围 (`scp`) 声明，来控制对 Web API 的访问。

## <a name="next-steps"></a>后续步骤

现在，你已通过配置 Web API 范围对其进行了公开，请使用访问范围的权限来配置客户端应用的注册。

> [!div class="nextstepaction"]
> [配置应用注册以访问 Web API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
