---
title: 快速入门：配置应用以公开 Web API | Azure
titleSuffix: Microsoft identity platform
description: 本快速入门介绍如何将应用程序配置为公开新的权限/范围和角色，使该应用程序可供客户端应用程序使用。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830285"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>快速入门：配置应用程序以公开 Web API

可以开发一个 Web API，并通过公开[权限/范围](developer-glossary.md#scopes)和[角色](developer-glossary.md#roles)，使其可供客户端应用程序使用。 可以像提供其他 Microsoft Web API（包括图形 API 和 Office 365 API）一样提供正确配置的 Web API。

本快速入门介绍如何将应用程序配置为公开新的范围，使其可供客户端应用程序使用。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 完成[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登录到 Azure 门户，并选择应用

在配置应用之前，请执行以下步骤：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  ，然后选择“应用注册”。 
1. 找到并选择要配置的应用程序。 选择应用以后，会看到应用程序的“概览”页或主注册页。 
1. 选择要使用哪个方法、UI 或应用程序清单来公开新的范围：
    * [通过 UI 公开新的范围](#expose-a-new-scope-through-the-ui)
    * [通过应用程序清单公开新的范围或角色](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>通过 UI 公开新的范围

[![演示如何使用 UI 公开 API](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

若要通过 UI 公开新的范围，请执行以下操作：

1. 在应用的“概览”页中，选择“公开 API”部分。  

1. 选择“添加范围”。 

1. 如果尚未设置“应用程序 ID URI”，则会看到一个提示你输入它的提示。  输入应用程序 ID URI 或使用已提供的，然后选择“保存并继续”。 

1. 出现“添加范围”页时，输入范围的信息： 

    | 字段 | 说明 |
    |-------|-------------|
    | **范围名称** | 为范围输入一个有意义的名称。<br><br>例如，`Employees.Read.All` 。 |
    | **谁可以许可** | 选择是让用户许可此范围，还是让管理员许可。 若要获得更高特权，请选择“仅管理员”。  |
    | **管理员许可显示名称** | 为范围输入管理员会看到的有意义说明。<br><br>例如： `Read-only access to Employee records` |
    | **管理员许可说明** | 为范围输入管理员会看到的有意义说明。<br><br>例如： `Allow the application to have read-only access to all Employee data.` |

    如果用户可以许可你的范围，另请添加以下字段的值：

    | 字段 | 说明 |
    |-------|-------------|
    | **用户许可显示名称** | 为范围输入一个用户会看到的有意义名称。<br><br>例如： `Read-only access to your Employee records` |
    | **用户许可说明** | 为范围输入用户会看到的有意义说明。<br><br>例如： `Allow the application to have read-only access to your Employee data.` |

1. 完成后，设置“状态”并选择“添加范围”。  

1. （可选）若要取消向应用用户提示同意你定义的范围，可以“预授权”客户端应用程序访问 Web API。 应该仅预授权所信任的客户端应用程序，因为用户不会有机会拒绝同意。
    1. 在“授权客户端应用程序”下，选择“添加客户端应用程序”
    1. 输入要预授权的客户端应用程序的“应用程序(客户端) ID”。 例如，以前注册的 Web 应用程序的 ID。
    1. 在“授权的范围”下，选择要取消显示许可提示的范围，然后选择“添加应用程序”。

    客户端应用现在是预授权的客户端应用 (PCA)，系统在用户登录时不会提示用户同意。

1. 按步骤[验证 Web API 是否已公开给其他应用程序](#verify-the-web-api-is-exposed-to-other-applications)。

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>通过应用程序清单公开新的范围或角色

应用程序清单充当了用于更新应用程序实体的机制，它定义了 Azure AD 应用注册的属性。

[![使用清单中的 oauth2Permissions 集合公开新的范围](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

若要通过编辑应用程序清单公开新的范围，请执行以下操作：

1. 在应用的“概览”页中，选择“清单”部分。  此时会打开一个基于 Web 的清单编辑器，可在其中**编辑**门户中的清单。 （可选）可以选择“下载”并在本地编辑清单，然后使用“上传”将清单重新应用到应用程序。 

    以下示例介绍通过将以下 JSON 元素添加到 `oauth2Permissions` 集合，在资源/API 中公开一个名为 `Employees.Read.All` 的新范围。

    通过编程方式或 GUID 生成工具（例如 [guidgen](https://www.microsoft.com/download/details.aspx?id=55984)）来生成 `id` 值。

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. 完成后，单击“保存”。 现在，Web API 已配置为可供目录中的其他应用程序使用。
1. 按步骤[验证 Web API 是否已公开给其他应用程序](#verify-the-web-api-is-exposed-to-other-applications)。

有关应用程序实体及其架构的详细信息，请参阅 Microsoft Graph 的[应用程序][ms-graph-application]资源类型参考文档。

有关应用程序清单（包括其架构引用）的详细信息，请参阅[了解 Azure AD 应用清单](reference-app-manifest.md)。

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>验证 Web API 是否已公开给其他应用程序

1. 返回到 Azure AD 租户，选择“应用注册”，然后找到并选择要配置的客户端应用程序。
1. 重复[将客户端应用程序配置为访问 Web API](quickstart-configure-app-access-web-apis.md) 中概述的步骤。
1. 执行到[选择 API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) 这一步时，请选择资源（Web API 应用注册）。
    * 如果使用 Azure 门户创建了 Web API 应用注册，则 API 资源会在“我的 API”选项卡中列出。
    * 如果允许 Visual Studio 在项目创建过程中创建 Web API 应用注册，则 API 资源会在“我的组织使用的 API”选项卡中列出。

在选择了 Web API 资源后，应会看到客户端权限请求可用的新范围。

## <a name="using-the-exposed-scopes"></a>使用公开的范围

为客户端配置访问 Web API 的适当权限后，Azure AD 可为它颁发 OAuth 2.0 访问令牌。 当客户端调用 Web API 时，会出示该访问令牌，其中的范围 (`scp`) 声明设置为客户端应用程序注册中请求的权限。

以后可以根据需要公开其他范围。 请考虑 Web API 可能要公开与各种不同功能关联的多个范围。 在运行时，资源可以通过评估所收到的 OAuth 2.0 访问令牌中的范围 (`scp`) 声明，来控制对 Web API 的访问。

在应用程序中，完整范围值是 Web API 的应用程序 ID URI（资源）和范围名称的串联 。

例如，如果 Web API 的应用程序 ID URI 是 `https://contoso.com/api`，范围名称为 `Employees.Read.All`，则完整范围为：

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>后续步骤

现在，你已通过配置其范围公开了 Web API，请使用访问这些范围的权限来配置客户端应用的注册。

> [!div class="nextstepaction"]
> [配置应用注册以访问 Web API](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
