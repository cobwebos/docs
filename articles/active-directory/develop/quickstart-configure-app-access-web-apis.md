---
title: 快速入门：配置应用以访问 Web API | Azure
titleSuffix: Microsoft identity platform
description: 在本快速入门中，你将在 Microsoft 标识平台中配置一个表示 Web API 的应用注册，以便对客户端应用程序启用范围内的资源访问（权限）。
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
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: fc2f3202ac88e3ee6c24db21dd9072a13a8deef9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442243"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>快速入门：配置客户端应用程序以访问 Web API

在本快速入门中，针对向 Microsoft 标识平台注册的客户端应用，你将提供对你自己的 Web API 的范围内基于权限的访问。 你还将向客户端应用提供访问 Microsoft Graph 的权限。

通过在客户端应用的注册中指定 Web API 的范围，客户端应用可从 Microsoft 标识平台获取包含这些范围的访问令牌。 然后在其代码内，Web API 可基于访问令牌中的范围提供对其资源的基于权限的访问。

## <a name="prerequisites"></a>先决条件

* 具备有效订阅的 Azure 帐户 - [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 完成[快速入门：注册应用程序](quickstart-register-app.md)
* 完成[快速入门：配置应用程序以公开 Web API](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>添加用于访问 Web API 的权限

在第一个方案中，向客户端应用授予访问你自己的 Web API 的权限 - 这两者都应作为先决条件的一部分进行注册。 如果尚未注册客户端应用和 Web API，请完成两篇[先决条件](#prerequisites)文章中的步骤。

此图显示了两个应用注册如何相互关联。 在本部分中，将向客户端应用的注册添加权限。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="一个显示 Web API 的线形图，其中右侧有公开的范围，左侧有客户端应用程序，且这些范围选为权限" border="false":::

注册客户端应用和 Web API，并通过创建范围公开 API 后，可按照以下步骤来配置客户端对 API 的权限：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::，以选择包含客户端应用的注册的租户。
1. 选择“Azure Active Directory” > “应用注册”，然后选择客户端应用程序（而不是 Web API） 。
1. 选择“API 权限” > “添加权限” > “我的 API”。  
1. 选择作为先决条件一部分注册的 Web API。

    默认情况下，选择“委托的权限”。 委托的权限适用于这样的客户端应用，它们以登录用户身份访问 Web API，且其访问权限应仅限于在下一步中选择的权限。 在本例中，请选择“委托的权限”。

    应用程序权限适用于服务类型或守护程序类型的应用程序，这些应用需要以自身身份访问 Web API，而无需用户交互来进行登录或同意。 除非已为 Web API 定义了应用程序角色，否则禁用此选项。
1. 在“选择权限”下，展开为 Web API 定义其范围的资源，并选择客户端应用代表已登录用户应具有的权限。

    如果使用了上一个快速入门中指定的示例范围名称，则应会看到 Employees.Read.All 和 Employees.Write.All 。
    完成先决条件时，请选择 Employees.Read.All 或可能已创建的其他权限。
1. 选择“添加权限”以完成此过程。

将权限添加到 API 后，应在“配置的权限”下看到所选权限。 下图显示了示例 Employees.Read.All 委托的权限已添加到客户端应用的注册。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Azure 门户中的“配置的权限”窗格，其中显示了新添加的权限":::

你可能还会注意到 Microsoft Graph API 的 User.Read 权限。 在 Azure 门户中注册应用时，会自动添加此权限。

## <a name="add-permissions-to-access-microsoft-graph"></a>添加用于访问 Microsoft Graph 的权限

除了代表已登录用户访问你自己的 Web API 外，应用程序可能还需要访问或修改 Microsoft Graph 中存储的用户的（或其他）数据。 或者，你可能有需要自行访问 Microsoft Graph 的服务或守护程序应用，无需任何用户交互即可执行操作。

### <a name="delegated-permission-to-microsoft-graph"></a>Microsoft Graph 的委托权限

配置 Microsoft Graph 的委派权限，使客户端应用程序可代表已登录用户执行操作，例如读取其电子邮件或修改其个人资料。 默认情况下，在客户端应用的用户登录时，会询问他们是否同意你为其配置的委派权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::，以选择包含客户端应用的注册的租户。
1. 选择“Azure Active Directory” > “应用注册”，然后选择客户端应用程序 。
1. 选择“API 权限” > “添加权限” > “Microsoft Graph”  
1. 选择“委托的权限”。 Microsoft Graph 公开了许多权限，最常用的权限显示在列表顶部。
1. 在“选择权限”下，选择以下权限：

    | 权限       | 说明                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | 查看用户的电子邮件地址                           |
    | `offline_access` | 维护对已授予访问权限的数据的访问权限 |
    | `openid`         | 登录用户                                       |
    | `profile`        | 查看用户的基本配置文件                           |
1. 选择“添加权限”以完成此过程。

无论何时配置权限，都将在登录时请求应用用户同意允许你的应用代表他们访问资源 API。

作为管理员，你还可代表所有用户授予同意，以便不提示这些用户执行此操作。 稍后会在本文的[详细介绍 API 权限和管理员同意](#more-on-api-permissions-and-admin-consent)部分讨论管理员同意。

### <a name="application-permission-to-microsoft-graph"></a>Microsoft Graph 的应用程序权限

为需要进行身份验证的应用程序配置应用程序权限，而无需用户交互或同意。 应用程序权限通常由以“无外设”方式访问 API 的后台服务或守护程序应用，以及访问另一个（下游）API 的 Web API 使用。

在以下步骤中，例如将授予 Microsoft Graph 的 Files.Read.All 权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false":::，以选择包含客户端应用的注册的租户。
1. 选择“Azure Active Directory” > “应用注册”，然后选择客户端应用程序 。
1. 选择“API 权限” > “添加权限” > “Microsoft Graph” > “应用程序权限”   。
1. Microsoft Graph 公开的所有权限都显示在“选择权限”下。
1. 选择要授予应用程序的一项或多项权限。 例如，你可能有一个守护程序应用，它用于扫描组织中的文件，并对特定文件类型或名称发出警报。

    在“选择权限”下展开“文件”，然后选择“Files.Read.All”权限 。
1. 选择“添加权限”。

某些权限（例如 Microsoft Graph 的 Files.Read.All 权限）需要管理员同意。 可通过选择“授予管理员同意”按钮来授予此类同意，这一点稍后将在[管理员同意按钮](#admin-consent-button)部分中进行讨论。

### <a name="configure-client-credentials"></a>配置客户端凭据

使用应用程序权限的应用通过使用自己的凭据自行进行身份验证，无需任何用户交互。 在应用程序（或 API）可通过使用应用程序权限访问 Microsoft Graph、你自己的 Web API 或其他任何 API 之前，需要配置该客户端应用的凭据。

若要详细了解如何配置应用的凭据，请参阅[添加凭据](quickstart-register-app.md#add-credentials)部分 - [快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。

## <a name="more-on-api-permissions-and-admin-consent"></a>有关 API 权限和管理员同意的更多信息

应用注册的“API 权限”窗格包含[配置的权限](#configured-permissions)表，还可能包含[授予的其他权限](#other-permissions-granted)表。 以下各部分介绍了这两个表和[管理员同意按钮](#admin-consent-button)。

### <a name="configured-permissions"></a>已配置权限

“API 权限”窗格上的“配置的权限”表显示了应用程序在执行基本操作时需要的权限列表 - 必需的资源访问 (RRA) 列表 。 用户或其管理员需要在使用你的应用前同意这些权限。 稍后可在运行时（使用动态同意）请求其他可选权限。

这是用户同意你的应用所需的最小权限列表。 可能还有更多权限，但总是需要这些权限。 为了安全起见，并让用户和管理员更舒适地使用你的应用，请勿询问不需要的内容。

可按照上面概述的步骤或从[授予的其他权限中](#other-permissions-granted)（在下一部分中介绍）添加或删除此表中显示的权限。 作为管理员，你可授予管理员同意表中出现的一整套 API 权限并撤销对单个权限的同意。

### <a name="other-permissions-granted"></a>授予的其他权限

你可能还会在“API 权限”窗格上看到标题是“为 {你的租户} 授予的其他权限”的表 。 在“为 {你的租户} 授予的其他权限”表中，显示了为租户授予的、未在应用程序对象中显式配置的权限。 这些权限是动态请求且获得同意的。 仅当至少应用了其中一个权限时，才会显示此部分。

可将此表中显示的一整套 API 权限或单个权限添加到“已配置权限”表。 作为管理员，你可撤销此部分中对 API 或单个权限的管理员同意。

### <a name="admin-consent-button"></a>管理员同意按钮

通过“为 {你的租户} 授予其他权限”按钮，管理员可向为应用程序配置的权限授予管理员同意。 选择该按钮后，会显示一个对话框，请求确认同意操作。

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Azure 门户中的“配置的权限”窗格中突出显示了“授予管理员同意”按钮":::

授予同意后，要求管理员同意的权限显示为“已获得权限”：

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="在 Azure 门户中“配置权限”表，其中显示了已为 Files.Read.All 权限授予管理员同意":::

如果你不是管理员，或者没有为应用程序配置任何权限，则将禁用“授予管理员同意”按钮。 如果你获得了权限但尚未配置它们，则“管理员同意”按钮会提示你处理这些权限。 可将它们添加到已配置的权限，或者将其删除。

## <a name="next-steps"></a>后续步骤

转到本系列的下一个快速入门，了解如何配置可以访问应用程序的帐户类型。 例如，你可能希望将访问权限仅限于你的组织（单租户）中的用户，或者允许其他 Azure AD 租户（多租户）中的用户和拥有个人 Microsoft 帐户 (MSA) 的用户。

> [!div class="nextstepaction"]
> [修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)