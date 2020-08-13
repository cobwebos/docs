---
title: 快速入门：将应用注册到 Microsoft 标识平台 | Azure
description: 本快速入门介绍如何使用 Microsoft 标识平台添加和注册应用程序。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 65fff06b4a2d28bbc276920ccbaba90d814d03f3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115350"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入门：将应用程序注册到 Microsoft 标识平台

在本快速入门中，我们使用 Azure 门户中的“应用注册”  体验注册应用程序。 

将应用与 Microsoft 标识平台集成的方法是将其注册到 Azure Active Directory 租户。 企业开发人员和软件即服务 (SaaS) 提供商可以开发能够与 Microsoft 标识平台集成的商业云服务或业务线应用程序。 集成为此类服务提供安全的登录和授权。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Azure AD 租户](quickstart-create-new-tenant.md)。

## <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果帐户有权访问多个租户，请在右上角选择该帐户。 将门户会话设置为所需的 Azure AD 租户。
1. 搜索并选择“Azure Active Directory”  。 在“管理”  下，选择“应用注册”  。
1. 选择“新注册”。 
1. 在“注册应用程序”  中，输入一个要向用户显示的有意义的应用程序名称。
1. 指定可使用应用程序的用户，如下所示：

    | 支持的帐户类型 | 说明 |
    |-------------------------|-------------|
    | **仅此组织目录中的帐户** | 若要生成业务线 (LOB) 应用程序，请选择此选项。 如果不在目录中注册应用程序，则此选项不可用。<br><br>此选项映射到仅限 Azure AD 的单租户。<br><br>此选项是默认选项，除非你是在目录外部注册应用。 如果在目录外部注册应用，则默认设置为 Azure AD 多租户和个人 Microsoft 帐户。 |
    | **任何组织目录中的帐户** | 若要面向所有企业和教育客户，请选择此选项。<br><br>此选项映射到仅限 Azure AD 的多租户。<br><br>如果已将应用注册为仅限 Azure AD 的单租户，则可通过“身份验证”页将其更新为 Azure AD 多租户，以及从多租户更新为单租户。  |
    | **任何组织目录中的帐户和个人 Microsoft 帐户** | 若要面向最广泛的客户，请选择此选项。<br><br>此选项映射到 Azure AD 多租户和个人 Microsoft 帐户。<br><br>如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改此设置， 而只能使用应用程序清单编辑器来更改支持的帐户类型。 |

1. 在“重定向 URI (可选)”下，选择要生成的应用的类型：“Web”或“公共客户端(移动和桌面)”。  然后，输入应用程序的重定向 URI 或回复 URL。

    * 对于 Web 应用程序，请提供应用的基 URL。 例如，`https://localhost:31544` 可以是本地计算机上运行的 Web 应用的 URL。 用户将使用此 URL 登录到 Web 客户端应用程序。
    * 对于公共客户端应用程序，请提供 Azure AD 返回令牌响应时所用的 URI。 输入特定于应用程序的值，例如 `myapp://auth`。

    如需 Web 应用程序或本机应用程序的示例，请参阅 [Microsoft 标识平台](./index.yml)中的快速入门。

1. 完成后，选择“注册”  。

    ![显示了在 Azure 门户中注册新应用程序的屏幕](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD 会向应用分配唯一的应用程序 ID 或客户端 ID。 门户会打开应用程序的“概览”页。  若要向应用程序添加功能，可以选择其他配置选项，包括品牌、证书和机密、API 权限等。

![新注册应用的概览页示例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>后续步骤

* 若要访问 Web API，请参阅[快速入门：配置客户端应用程序以访问 Web API](quickstart-configure-app-access-web-apis.md)

* 若要了解权限，请参阅 [Microsoft 标识平台终结点中的权限和许可](v2-permissions-and-consent.md)。

* 若要公开 Web API，请参阅[快速入门：配置应用程序来公开 Web API](quickstart-configure-app-expose-web-apis.md)。

* 若要管理支持的帐户，请参阅[快速入门：修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)。

* 若要生成应用并添加功能，请参阅 [Microsoft 标识平台](./index.yml)中的快速入门。

* 了解有关表示已注册应用程序的两个 Azure AD 对象及它们之间的关系的详细信息，请参阅[应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

* 如需深入了解开发应用时应使用的品牌准则，请参阅[应用程序的品牌准则](howto-add-branding-in-azure-ad-apps.md)。