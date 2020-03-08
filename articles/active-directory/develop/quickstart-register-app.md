---
title: 快速入门：向 Microsoft 标识平台注册应用 |Microsoft
description: 本快速入门介绍如何使用 Microsoft 标识平台添加和注册应用程序。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: dc719064166be917d868c7b7fee6b126b4099840
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893147"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入门：向 Microsoft 标识平台注册应用程序

在本快速入门中，你将使用 Azure 门户中的**应用注册**体验注册应用程序。 你的应用已与 Microsoft 标识平台集成。 企业开发人员和软件即服务（SaaS）提供商可以开发能够与 Microsoft 标识平台集成的商业云服务或业务线应用程序。 集成为此类服务提供安全的登录和授权。

## <a name="prerequisites"></a>必备条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="register-a-new-application-using-the-azure-portal"></a>使用 Azure 门户注册新应用程序

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户允许你访问多个租户，请在右上角选择你的帐户。 将门户会话设置为所需的 Azure AD 租户。
1. 搜索并选择“Azure Active Directory”。 在 "**管理**" 下，选择**应用注册**。
1. 选择“新注册”。
1. 在 "**注册应用程序**" 中，输入要向用户显示的有意义的应用程序名称。
1. 指定可使用应用程序的用户，如下所示：

    | 支持的帐户类型 | 说明 |
    |-------------------------|-------------|
    | **仅此组织目录中的帐户** | 若要生成业务线 (LOB) 应用程序，请选择此选项。 如果未在目录中注册应用程序，则此选项不可用。<br><br>此选项映射到仅限 Azure AD 的单租户。<br><br>此选项是默认选项，除非你要在目录之外注册应用。 如果在目录外部注册应用，则默认设置为 Azure AD 多租户和个人 Microsoft 帐户。 |
    | **任何组织目录中的帐户** | 若要面向所有企业和教育客户，请选择此选项。<br><br>此选项映射到仅限 Azure AD 的多租户。<br><br>如果已将应用注册为仅 Azure AD 单租户，则可以通过 "**身份验证**" 页将其更新为 Azure AD 多租户并返回到单租户。 |
    | **任何组织目录中的帐户和个人 Microsoft 帐户** | 若要面向最广泛的客户，请选择此选项。<br><br>此选项映射到 Azure AD 多租户和个人 Microsoft 帐户。<br><br>如果已将应用注册为 Azure AD 多租户和个人 Microsoft 帐户，则不能在 UI 中更改此设置。 而只能使用应用程序清单编辑器来更改支持的帐户类型。 |

1. 在 "**重定向 URI （可选）** " 下，选择要生成的应用类型： **Web**或**公用客户端（移动 & 桌面）** 。 然后输入应用程序的 "重定向 URI" 或 "回复 URL"。

    * 对于 Web 应用程序，请提供应用的基 URL。 例如，`https://localhost:31544` 可以是本地计算机上运行的 Web 应用的 URL。 用户将使用此 URL 登录到 Web 客户端应用程序。
    * 对于公共客户端应用程序，请提供 Azure AD 返回令牌响应时所用的 URI。 输入特定于应用程序的值，例如 `myapp://auth`。

    有关 web 应用程序或本机应用程序的示例，请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop)中的快速入门。

1. 完成后，选择“注册”。

    ![显示用于在 Azure 门户中注册新应用程序的屏幕](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD 为应用分配唯一的应用程序或客户端 ID。 门户会打开应用程序的 "**概述**" 页。 若要向应用程序添加功能，可以选择其他配置选项，包括品牌、证书和密码、API 权限等。

![新注册应用程序概述页的示例](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>后续步骤

转到下一篇文章，了解如何访问 web Api。
> [!div class="nextstepaction"]
> [快速入门：配置客户端应用程序以访问 web Api](quickstart-configure-app-access-web-apis.md)

* 若要了解权限，请参阅[Microsoft 标识平台终结点中的权限和许可](v2-permissions-and-consent.md)。

* 若要公开 web Api，请参阅[快速入门：将应用程序配置为公开 Web api](quickstart-configure-app-expose-web-apis.md)。

* 若要管理支持的帐户，请参阅[快速入门：修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)。

* 若要生成应用并添加功能，请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop)中的快速入门。

* 了解有关表示已注册应用程序的两个 Azure AD 对象及它们之间的关系的详细信息，请参阅[应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

* 若要详细了解开发应用时应使用的品牌准则，请参阅[应用程序的品牌指导原则](howto-add-branding-in-azure-ad-apps.md)。
