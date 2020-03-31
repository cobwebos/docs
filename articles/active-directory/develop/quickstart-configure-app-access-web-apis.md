---
title: 快速入门：访问应用的 Web API - Microsoft 标识平台 | Azure
description: 本快速入门介绍如何配置注册到 Microsoft 标识平台的应用，以包含用于访问 Web API 的重定向 URI、凭据或权限。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/09/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 5e628626f2db49ff67d6d7ab425a3a19870b1ebd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215965"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>快速入门：配置客户端应用程序以访问 Web API

在本快速入门中，你将添加重定向 URI、凭据或权限，用于访问应用程序的 Web API。 Web 或机密客户端应用程序需要建立安全凭据，才能参与需要身份验证的授权流。 Azure 门户支持的默认身份验证方法为“客户端 ID + 机密密钥”。 应用将在此过程中获取访问令牌。

在客户端可以访问资源应用程序公开的 Web API（例如 Microsoft Graph API）之前，许可框架可确保客户端获取所请求权限的授权。 默认情况下，所有应用程序都可以从 Microsoft Graph API 请求权限。

## <a name="prerequisites"></a>先决条件

* 完成[快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)。
* 查看 [Microsoft 标识平台终结点中的权限和许可](v2-permissions-and-consent.md)。
* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>登录到 Azure 门户，并选择应用

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户。 将门户会话设置为所需的 Azure AD 租户。
1. 搜索并选择“Azure Active Directory”  。 在“管理”下，选择“应用注册”。  
1. 找到并选择要配置的应用程序。 选择应用后，会看到应用程序的“概述”页或主注册页。 

使用以下过程配置应用程序以访问 Web API。

## <a name="add-redirect-uris-to-your-application"></a>将重定向 URI 添加到应用程序

可将自定义重定向 URI 和建议的重定向 URI 添加到应用程序。 若要为 Web 和公共客户端应用程序添加自定义重定向 URI：

1. 在应用的“概述”页中，选择“身份验证”。  
1. 找到“重定向 URI”。  可能需要选择“切换到旧体验”。 
1. 选择要生成的应用程序类型：“Web”或“公共客户端/本机(移动和桌面)”。  
1. 输入应用程序的重定向 URI。

   * 对于 Web 应用程序，请提供应用程序的基 URL。 例如，`http://localhost:31544` 可以是本地计算机上运行的 Web 应用程序的 URL。 用户将使用此 URL 登录到 Web 客户端应用程序。
   * 对于公共应用程序，请提供 Azure AD 返回令牌响应时所用的 URI。 输入特定于应用程序的值，例如 `https://MyFirstApp`。
1. 选择“保存”。 

若要从建议用于公共客户端的重定向 URI 中进行选择，请执行以下步骤：

1. 在应用的“概述”页中，选择“身份验证”。  
1. 找到“建议用于公共客户端(移动、桌面)的重定向 URI”。  可能需要选择“切换到旧体验”。 
1. 为应用程序选择一个或多个重定向 URI。 还可以输入自定义重定向 URI。 如果不确定要使用哪个值，请参阅库文档。
1. 选择“保存”。 

某些限制适用于重定向 URI。 有关详细信息，请参阅[重定向 URI/回复 URL 的限制和局限性](https://docs.microsoft.com/azure/active-directory/develop/reply-url)。

> [!NOTE]
> 尝试新的“身份验证设置”体验，在其中可以根据要面向的平台或设备配置应用程序的设置。 
>
> 若要查看此视图，请在“身份验证”页中选择“试用新体验”。  
>
> ![单击“试用新体验”以查看平台配置视图](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> 此时会转到[新的“平台配置”页](#configure-platform-settings-for-your-application)。 

### <a name="configure-advanced-settings-for-your-application"></a>配置应用程序的高级设置

根据要注册的应用程序，可能需要配置其他一些设置，例如：

* **注销 URL**。
* 对于单页应用，可以启用“隐式授权”，并选择希望授权终结点颁发的令牌。 
* 对于“默认客户端类型”部分中使用 Windows 集成身份验证、设备代码流或用户名/密码获取令牌的桌面应用，请将“将应用程序视为公共客户端”设置指定为“是”。   
* 对于使用 Live SDK 来与 Microsoft 帐户服务集成的传统应用，请配置“Live SDK 支持”。  新应用不需要此设置。
* **默认客户端类型**。
* **支持的帐户类型**。

### <a name="modify-supported-account-types"></a>修改支持的帐户类型

“支持的帐户类型”指定哪些用户可以使用该应用程序或访问 API。 

如果在注册应用程序时配置了支持的帐户类型，则只能在以下情况下使用应用程序清单编辑器更改此设置：

* 将帐户类型从 **AzureADMyOrg** 或 **AzureADMultipleOrgs** 更改为 **AzureADandPersonalMicrosoftAccount**，或反之，或者
* 将帐户类型从 **AzureADMyOrg** 更改为 **AzureADMultipleOrgs**，或反之。

若要更改现有应用注册支持的帐户类型，请更新 `signInAudience` 键。 有关详细信息，请参阅[配置应用程序清单](reference-app-manifest.md#configure-the-app-manifest)。

## <a name="configure-platform-settings-for-your-application"></a>配置应用程序的平台设置

![根据平台或设备配置应用的设置](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

若要根据面向的平台或设备配置应用程序设置：

1. 在“平台配置”页上选择“添加平台”，并从可用选项中进行选择。  

   ![显示“配置平台”页](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. 根据所选的平台输入设置信息。

   | 平台                | 配置设置            |
   |-------------------------|-----------------------------------|
   | **Web**              | 输入应用程序的“重定向 URI”。  |
   | **iOS/macOS**              | 输入应用的“捆绑 ID”（可在 XCode 中的 info.plist 内找到，或者在“生成设置”中找到）。  添加捆绑 ID 可自动创建应用程序的重定向 URI。 |
   | **Android**          | 提供应用的“包名称”（可在 AndroidManifest.xml 文件中找到）。 <br/>生成并输入**签名哈希**。 添加签名哈希可自动创建应用程序的重定向 URI。  |
   | **移动和桌面应用程序**  | 可选。 为桌面和设备生成应用时，请选择**建议的重定向 URI** 之一。<br/>可选。 输入一个**自定义重定向 URI**，用作 Azure AD 在响应身份验证请求时将用户重定向到的位置。 例如，对于要交互的 .NET Core 应用程序，请使用 `https://localhost`。 |

   > [!IMPORTANT]
   > 对于不使用最新 Microsoft 身份验证库 (MSAL) 或不使用中介的移动应用程序，必须在“桌面 + 设备”中为这些应用程序配置重定向 URI。 

根据所选的平台，可能还可以配置其他设置。 对于“Web”应用，可以： 

* 添加更多重定向 URI
* 配置“隐式授权”，以选择希望由授权终结点颁发的令牌： 

  * 对于单页应用，请同时选择“访问令牌”和“ID 令牌”  
  * 对于 Web 应用，请选择“ID 令牌” 

## <a name="add-credentials-to-your-web-application"></a>将凭据添加到 Web 应用程序

若要将凭据添加到 Web 应用程序，请添加证书或创建客户端机密。 若要添加证书，请执行以下操作：

1. 在应用的“概述”页中，选择“证书和机密”部分。  
1. 选择“上传证书”。 
1. 选择要上传的文件。 它必须是以下文件类型之一：.cer、.pem、.crt。
1. 选择 **添加** 。

若要添加客户端机密：

1. 在应用的“概述”页中，选择“证书和机密”部分。  
1. 选择“新建客户端机密”。 
1. 添加客户端机密的说明。
1. 选择持续时间。
1. 选择 **添加** 。

> [!NOTE]
> 保存配置更改后，最右边的列会包含客户端机密值。 **请务必复制此值**，以便在客户端应用程序代码中使用，因为退出此页后将无法访问此密钥。

## <a name="add-permissions-to-access-web-apis"></a>添加用于访问 Web API 的权限

默认已选择[图形 API 登录和读取用户配置文件权限](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions)。 对于每个 Web API，可以从[两种类型的权限](developer-glossary.md#permissions)中进行选择：

* **应用程序权限**。 客户端应用程序需要自行直接访问 Web API，不使用用户上下文。 此权限类型需要管理员许可。 此权限不适用于桌面和移动客户端应用程序。
* **委托的权限**。 客户端应用程序需要以登录用户的身份访问 Web API，但访问权限受所选权限的限制。 除非权限需要管理员许可，否则用户可以授予此类型的权限。

  > [!NOTE]
  > 将委托权限添加到应用程序不会自动向租户中的用户授予许可。 除非管理员代表所有用户授予许可，否则用户仍必须在运行时手动同意添加的委托权限。

若要添加从客户端访问资源 API 的权限：

1. 在应用的“概述”页中，选择“API 权限”。  
1. 在“已配置权限”  下，选择“添加权限”  。
1. 默认情况下，此视图允许从“Microsoft API”进行选择。  选择感兴趣的 API 部分。

    * **Microsoft API**。 用于选择 Microsoft API（例如 Microsoft Graph）的权限。
    * **我的组织使用的 API**。 用于选择组织公开的 API 或者组织已集成的 API 的权限。
    * **我的 API**。 用于选择你公开的 API 的权限。

1. 选择 API 后，会看到“请求 API 权限”页。  如果 API 公开委托的权限和应用程序权限，请选择应用程序需要哪种类型的权限。
1. 完成后，请选择“添加权限”  。

随即会返回到“API 权限”页。  权限已保存并已添加到表中。

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>了解 API 权限和管理员同意 UI

### <a name="configured-permissions"></a>已配置权限

本部分介绍已在应用程序对象中显式配置的权限。 这些权限是应用所需资源访问列表的一部分。 可以在此表中添加或删除权限。 管理员还可为一组 API 权限或单个权限授予或撤销管理员许可。

### <a name="other-permissions-granted"></a>授予的其他权限

如果你的应用程序已在租户中注册，则你可能会看到一个名为“为租户授予的其他权限”的附加部分  。 本部分介绍为租户授予的、未在应用程序对象中显式配置的权限。 这些权限是动态请求和许可的。 仅当至少有一个应用的权限时，才会显示此部分。

可以将此部分中显示的一组 API 权限或单个权限添加到“已配置权限”部分  。 作为管理员，你还可以撤销此部分中各个 API 或权限的管理员同意。

### <a name="admin-consent-button"></a>管理员同意按钮

如果你的应用程序已在租户中注册，你将看到“为租户授予管理员许可”按钮。  如果你不是管理员，或者没有为应用程序配置任何权限，则将禁用此按钮。
管理员可以通过此按钮向为应用程序配置的权限授予管理员许可。 单击“管理员同意”按钮将启动一个新窗口，其中包含显示了所有已配置权限的同意提示。

> [!NOTE]
> 为应用程序配置的权限与在同意提示下显示的权限之间存在延迟。 如果在同意提示中看不到所有配置的权限，请将其关闭并重新启动。

如果为你授予了权限但尚未配置这些权限，则管理员许可按钮会提示你处理这些权限。 可以将它们添加到已配置的权限，也可以将其删除。

同意提示提供“接受”或“取消”选项   。 选择“接受”以授予管理员许可。  如果选择“取消”，则不会授予管理员许可。  将有一条错误消息指出已拒绝许可。

> [!NOTE]
> 在许可提示中选择“接受”授予管理员许可之后，在门户中反映管理员许可状态之前，会存在一段延迟。 

## <a name="next-steps"></a>后续步骤

继续学习下一篇文章了解如何公开 Web API。
> [!div class="nextstepaction"]
> [快速入门：配置应用程序以公开 Web API](quickstart-configure-app-expose-web-apis.md)

* 了解有关表示已注册应用程序的两个 Azure AD 对象及它们之间的关系的详细信息，请参阅[应用程序对象和服务主体对象](app-objects-and-service-principals.md)。

* 深入了解使用 Azure Active Directory 开发应用程序时应使用的品牌准则，请参阅[应用程序的品牌准则](howto-add-branding-in-azure-ad-apps.md)。

* [快速入门：将应用程序注册到 Microsoft 标识平台](quickstart-register-app.md)

* [快速入门：修改应用程序支持的帐户](quickstart-modify-supported-accounts.md)

* [快速入门：删除已注册到 Microsoft 标识平台的应用程序](quickstart-remove-app.md)
