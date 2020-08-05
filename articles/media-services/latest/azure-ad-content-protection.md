---
title: 使用 Azure AD 进行端到端内容保护
description: 本文介绍如何通过 Azure 媒体服务和 Azure Active Directory 保护内容
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/1/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: ad50b29dbda7c09c9312ebb4a01ebc5da568f3da
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422090"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>教程：使用 Azure AD 进行端到端内容保护

通过本教程和提供的播放器示例，可以在 Azure 媒体服务 (AMS) 和 Azure Active Directory (AAD) 上设置端到端媒体内容保护子系统，以使用所有 AMS 支持的 DRM/AES-128、流式处理协议、编解码器和容器格式来流式传输媒体内容。 该示例的兼容性足够高，可以通过使用 Proof Key for Code Exchange (PKCE) 的授权代码流安全访问受 OAuth 2 保护的任何 REST API。 （Azure 媒体服务许可证传送服务只是其中之一。）它还适用于 Microsoft Graph API 或使用 OAuth 2 授权代码流保护的任何自定义开发 REST API。 这是[示例代码](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)的配套文档。

在本教程中，将：

> [!div class="checklist"]
>
> * 考虑身份验证要求
> * 了解应用的工作原理
> * 注册后端资源应用
> * 注册客户端应用
> * 设置媒体服务帐户内容密钥策略和流式处理策略
> * 设置播放器应用

如果没有 Azure 媒体服务订阅，请创建一个 Azure [免费试用帐户](https://azure.microsoft.com/free/)，然后创建一个媒体服务帐户。

### <a name="duration"></a>Duration
具备必备技术后，大约可在两小时内完成本教程。

## <a name="prerequisites"></a>先决条件

本教程将使用以下最新的技术版本和概念。 建议你先熟悉它们，然后再开始学习本教程。

### <a name="prerequisite-knowledge"></a>必备知识

这是可选的，但建议你在开始学习本教程之前熟悉以下概念：

* 数字版权管理 (DRM)
* [Azure 媒体服务 (AMS) v3](./media-services-overview.md)
* 使用 AMS API v3、Azure 门户或 [Azure 媒体服务资源管理器 (AMSE) 工具](https://github.com/Azure/Azure-Media-Services-Explorer)的 AMS [内容密钥策略](content-key-policy-concept.md)
* [Microsoft 标识平台](../../active-directory/develop/index.yml)上的 Azure AD v2 终结点
* 新式云身份验证，如 [OAuth 2.0 和 OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [OAuth 2.0 中的授权代码流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)和需要 PKCE 的原因
  * [委派权限与应用程序权限](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT 令牌](../../active-directory/develop/access-tokens.md)、其声明和签名密钥滚动更新（包含在示例中）。

### <a name="prerequisite-code-and-installations"></a>必备代码和安装

* 示例代码。 下载[示例代码](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)。
* 安装 Visual Studio Code。 在此处 [https://code.visualstudio.com/download](https://code.visualstudio.com/download) 下载 Visual Studio Code。
* 安装 Node.js。 在此处 [https://nodejs.org](https://nodejs.org) 下载 Node.js。安装附带 NPM。
* 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
* Azure 媒体服务 (AMS) 帐户。
* @azure/msal-browser v2.0，[Microsoft 身份验证库 (MSAL)](../../active-directory/develop/msal-overview.md) SDK 系列中用于不同客户端平台的成员之一
* 最新版本的 [Azure Media Player](https://github.com/Azure-Samples/azure-media-player-samples)（包含在示例中）。
* Apple 提供的 FPS 凭据，如果想要添加 FairPlay DRM 和使用 CORS（可通过客户端 JavaScript 访问）托管的应用程序证书，则需要它。

> [!IMPORTANT]
> 本教程使用 .NET 创建内容密钥策略限制。  如果你不是 .NET 开发者，但想尝试使用 Node.js 连接到 Azure 媒体服务，请阅读[连接到媒体服务 v3 API - Node.js](configure-connect-nodejs-howto.md)。 还有一个 Node.js 模块可用于自动处理密钥滚动更新，请参阅 Node.js [passport-ad 模块](https://github.com/AzureAD/passport-azure-ad)。

## <a name="consider-the-authentication-and-authorization-requirements"></a>考虑身份验证和授权要求

设计子系统时存在几个难题。 它具有多个移动部件，受客户端应用约束，并且每六周发生一次 Azure AD 密钥滚动更新。

本教程中使用的单页应用 (SPA) 考虑到对身份验证要求的挑战以及随之而来的限制。 它使用：

* Azure AD v2 终结点，因为 Azure AD 开发者平台（v1 终结点）正在转变为 Microsoft 标识平台（v2 终结点）。
* 授权代码流，因为已弃用 OAuth 2 隐式授权流。
* 受以下约束限制的应用：
    * 公共客户端无法隐藏客户端密码。  单独的授权代码流需要隐藏客户端密码，因此使用带有 PKCE 的授权代码流。
    * 基于浏览器的应用，它受限于浏览器安全沙盒（CORS/预检约束）。
    * 基于浏览器的应用，它使用受限于 JavaScript 安全约束的新式 JavaScript（自定义标头辅助功能、correlation-id）。

## <a name="understand-the-subsystem-design"></a>了解子系统设计

下图显示了子系统的设计。  它具有三层：

* 后端办公系统层（黑色），用于配置内容密钥策略和发布待流式处理的内容
* 公共终结点（蓝色），面向播放器/客户的终结点，提供身份验证、授权、DRM 许可证和加密内容
* 播放器应用（浅蓝色），它会集成所有组件并
    * 通过 Azure AD 处理用户身份验证。
    * 处理获取自 Azure AD 的 access_token。
    * 从 AMS/CDN 接收清单和加密内容。
    * 从 Azure 媒体服务获取 DRM 许可证。
    * 处理内容解密、解码和显示。

![用于解析 JWT 令牌的屏幕](media/aad-ams-content-protection/subsystem.svg)

有关子系统的详细内容，请参阅[设计带访问控制的多 DRM 内容保护系统](./design-multi-drm-system-with-access-control.md)。

## <a name="understand-the-single-page-app"></a>了解单页应用

播放器应用是一个单页应用程序 (SPA)，使用以下平台开发：

* 带有 ES 6 JavaScript 的 Node.js
* @azure/msal-browser 2.0 beta
* Azure Media Player SDK
* 针对 Azure AD v2 终结点的 OAuth 2 流（Microsoft 标识平台）

SPA 播放器应用完成以下操作：

* 对租户本地用户和来自其他 AAD 租户或 MSA 帐户的来宾用户进行用户身份验证。 用户可以选择通过浏览器弹出或重定向（对于 Safari 等不允许弹出窗口的浏览器）进行登录。
* 通过带有 PKCE 的授权代码流获取 `access_token`。
* 更新 `access_token`（AAD 发行的令牌有效期为 1 小时），此操作还可获取 `refresh_token`。
* 解析 JWT 令牌（`access_token` 和 `id_token`）以进行测试/检查。
* 获得所有三个 DRM 或 AES-128 内容密钥的 DRM 许可证。
* 采用 DRM 与流式处理协议和容器格式的各种组合对内容进行流式处理。 为每个组合生成正确的格式字符串。
* 解密、解码和显示。
* 调用 Microsoft Graph API 以进行故障排除。 <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

用于登录、令牌获取、令牌续订和令牌显示的屏幕：

 ![用于登录、令牌获取、令牌续订和令牌显示的屏幕](media/aad-ams-content-protection/token-acquisition.png)

用于解析 JWT 令牌（access_token 或 id_token）的屏幕：

![用于解析 JWT 令牌的屏幕](media/aad-ams-content-protection/parsing-jwt-tokens.png)

用于通过 DRM/AES 和流式处理协议和容器格式的不同组合测试受保护内容的屏幕：

![用于解析 JWT 令牌的屏幕](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>选择 Azure AD 租户

> [!NOTE]
> 从这里开始，假定你已登录到 Azure 门户并且至少有一个 Azure AD 租户。

选择要用于端到端示例的 Azure AD 租户。 可以使用两个选项：

* 现有的 Azure AD 租户。 任何 Azure 订阅都必须有一个 Azure AD 租户，但一个 Azure AD 租户可供多个 Azure 订阅使用。
* 新 Azure AD 租户，未由任何 Azure 订阅使用。 如果选择新租户选项，则媒体服务帐户和示例播放器应用必须位于使用单独 Azure AD 租户的 Azure 订阅中。 这提供了一些灵活性。 例如，你可以使用自己的 Azure AD 租户，还可以使用客户的 Azure 订阅中的媒体服务帐户。

## <a name="register-the-backend-resource-app"></a>注册后端资源应用

1. 导航到选择或创建的 Azure AD 租户。
1. 从菜单中选择“Azure Active Directory”。
1. 从菜单中选择“应用注册”。
1. 单击“+ 新建注册”。
1. 将应用命名为 LicenseDeliveryResource2（其中 2 表示 AAD v2 终结点）。
1. 选择“仅此组织目录中的帐户(仅 [租户名称] - 单个租户)”。 若要启用对多个租户的访问权限，请选择其他多租户选项。
1. “重定向 URI”是可选项，并且稍后可以更改。
1. 单击“注册”。 此时将显示“应用注册”视图。
1. 从菜单中选择“清单”。 此时将显示“清单”视图。
1. 将 `accessTokenAcceptedVersion` 的值更改为 2（无引号）。
1. 将 `groupMembershipClaims` 的值更改为 "SecurityGroup"（含引号）。
1. 单击“ **保存**”。
1. 从菜单中选择“公开 API”。 此时将显示“添加作用域”视图。 （Azure 提供应用程序 ID URI，但如果你想对其进行更改，可以在“应用程序 ID URI”字段中编辑它。）
1. 单击“保存并继续”。 视图将会更改。 对于下表“设置”列中的每个设置，请在“值”列中输入值，然后单击“添加作用域”。

| 设置 | 值 | 说明 |
| ------- | ----- | ----------- |
| 作用域名称 | DRM.License.Delivery | 在请求访问此 API 时作用域将如何显示，以及在将作用域授予客户端应用程序时作用域将如何显示在访问令牌中。 这必须在应用程序内保持唯一。 最佳做法是使用“resource.operation.constraint”作为生成名称的模式。 |
| 谁能同意？ | 管理员和用户 | 这决定了用户能否在已启用用户同意的目录中同意此作用域。 |
| 管理员许可显示名称 | *DRM 许可证传送* | 这是当管理员向此作用域授予同意时在同意屏幕中调用的作用域。 |
| 管理员许可说明** | DRM 许可证传送后端资源作用域 | 这是当租户管理员在同意屏幕上扩展作用域时显示的作用域的详细说明。 |
| 用户许可显示名称 | DRM.License.Delivery | 这是将在用户同意此作用域时在同意屏幕中调用的作用域。 |
| 用户许可说明 | DRM 许可证传送后端资源作用域 | 这是当用户在同意屏幕上扩展作用域时显示的作用域的详细说明。 |
| 状态 | *已启用* | 这决定了此作用域是否可供客户端请求获取。 如果不想让作用域对客户端可见，请将状态设置为“已禁用”。 只能删除已禁用的作用域，建议在禁用作用域后至少等待一周，然后再删除作用域，以确保没有客户端仍在使用它。 |

## <a name="register-the-client-app"></a>注册客户端应用

1. 导航到选择或创建的 Azure AD 租户。
1. 从菜单中选择“Azure Active Directory”。
1. 从菜单中选择“应用注册”。
1. 单击“+ 新建注册”。
1. 为客户端应用指定一个名称，如“AMS AAD 内容保护”。
1. 选择“仅此组织目录中的帐户(仅 [租户名称] - 单个租户)”。 若要启用对多个租户的访问权限，请选择其他多租户选项。
1. “重定向 URI”是可选项，并且稍后可以更改。
1. 单击“注册”。
1. 从菜单中选择“API 权限”。
1. 单击“+ 添加权限”。 将打开“请求获取 API 权限”视图。
1. 单击“我的 API”选项卡，选择你在上一部分创建的 LicenseDeliveryResource2 应用。
1. 单击 DRM 箭头并选中 DRM.License.Delivery 权限。
1. 单击“添加权限”。 将关闭“添加权限”视图。
1. 从菜单中选择“清单”。 此时将显示“清单”视图。
1. 查找以下值对，并将其添加到 `replyUrlsWithType` 属性：

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > 此时，你还没有播放器应用的 URL。  如果要从 localhost Web 服务器运行应用，可以仅使用 localhost 值对。 部署播放器应用后，可以在此处使用已部署的 URL 添加条目。  如果忘记这样做，将在 Azure AD 登录时看到一条错误消息。

1. 单击“ **保存**”。
1. 最后，要验证配置是否正确，请选择“身份验证”。  此时将显示“身份验证”视图。 你的客户端应用程序将被列为单页应用 (SPA)，并列出重定向 URI，且授权类型将为带有 PKCE 的授权代码流。

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>设置媒体服务帐户内容密钥策略和流式处理策略

本部分假定你是一名 .NET 开发者，并且熟悉使用 AMS v3 API。

> [!NOTE]
> 在撰写本文时，不能将 Azure 门户用于媒体服务帐户密钥策略设置，因为它不支持在 OpenID-Config 中使用非对称令牌签名密钥。设置必须支持 Azure AD 密钥滚动更新，因为 Azure AD 颁发的令牌由非对称密钥进行签名，并且密钥每六周滚动更新一次。 因此，本教程使用 .NET 和 AMS v3 API。

适用于 DRM 和 AES-128 的[内容密钥策略](content-key-policy-concept.md)和[流式处理策略](streaming-policy-concept.md)的配置。  更改内容密钥策略中的 `ContentKeyPolicyRestriction`。

下面是用于创建内容密钥策略限制的 .NET 代码。

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

在上面的代码中更改 `ida_AADOpenIdDiscoveryDocument`、`ida_audience` 和 `ida_issuer` 值。 若要在 Azure 门户中查找这些项的值：

1. 选择之前使用的 AAD 租户，单击菜单中的“应用注册”，并单击“终结点”链接 。
1. 选择并复制 OpenIdConnect metadata document 字段的值，并将其作为 `ida_AADOpenIdDiscoveryDocument` 值粘贴到代码中。
1. `ida_audience` 值是已注册应用 LicenseDeliveryResource2 的应用（客户端）ID。
1. `ida_issuer` 值为 URL `https://login.microsoftonline.com/[tenant_id]/v2.0`。 将 tenant_id 替换为你的租户 ID。

## <a name="set-up-the-sample-player-app"></a>设置示例播放器应用

如果尚未从以下源存储库克隆或下载应用，请克隆或下载：[https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)。

可以使用两种方法设置播放器应用：

* 最基本的自定义操作（仅替换一些常量字符串值，例如 client_id、tenant_id 和流式处理 URL），但必须使用 Visual Studio Code 和 Node.js。
* 如果希望使用其他 IDE 和 Web 平台（例如 ASP.NET Core），则可以将网页文件、JavaScript 文件和 CSS 文件放入项目中，因为播放器应用本身不使用任何服务器端代码。

### <a name="option-1"></a>选项 1

1. 启动 Visual Studio Code。
1. 若要打开项目，请单击“文件”->“打开文件夹”->“浏览到”并选择 package.json 文件的父文件夹。
1. 打开 JavaScript 文件 public/javascript/constants.js。
1. 将 `OAUTH2_CONST.CLIENT_ID` 替换为 AAD 租户中已注册的客户端应用的 `client_id`。  可以在 Azure 门户中已注册的应用的“概述”部分找到 `client_id`。 注意：它是客户端 ID，而不是对象 ID。
1. 将 `OAUTH2_CONST.TENANT_ID` 替换为 Azure AD 租户的 `tenant_id`。 可以通过单击 Azure Active Directory 菜单找到 `tenant_id`。 tenant_id 将出现在“概述”部分。
1. 将 `OAUTH2_CONST.SCOPE` 替换为添加到已注册的客户端应用中的作用域。 可以通过从“应用注册”菜单导航到已注册的客户端应用，然后选择客户端应用来找到该作用域：
    1. 选择客户端应用，单击“API 权限”菜单，然后在 API 权限 LicenseDeliveryResource2 下选择作用域 DRM.License.Delivery 。 权限的格式应类似于 api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery。 **重要说明**：在 `OAUTH2_CONST.SCOPE` 中的 `offline_access` 前面留空格。
1. 替换 `AMS_CONST` 的两个常量字符串，如下所示。 一个是测试资产的受保护流式处理 URL，另一个是 FPS 应用程序证书 URL（如果要包含 FairPlay 测试用例）。 否则，可以将其保留为 `AMS_CONST.APP_CERT_URL`。 然后，单击“保存”。

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

若要在本地测试：

1. 在 Visual Studio Code (VSC) 中，从主菜单中依次选择“查看”和“终端” 。
1. 如果尚未安装 npm，请在命令提示符处输入 `npm install`。
1. 在命令提示符处输入 `npm start`。 （如果 npm 没有启动，请尝试在命令提示符处输入 `cd npmweb` 将目录更改为 `npmweb`。）
1. 使用浏览器浏览到 `http://localhost:3000`。

根据所使用的浏览器，选择 DRM/AES 与流式处理协议与容器格式的正确组合，以在登录后进行测试（获取 `access_token`）。 如果在 macOS 上的 Safari 中进行测试，请选中“重定向 API”选项，因为 Safari 不允许弹出窗口。 其他大多数浏览器都同时允许弹出和重定向选项。

### <a name="option-2"></a>方法 2

如果计划使用其他 IDE/Web 平台和/或 Web 服务器（如在开发计算机上运行的 IIS），请将以下文件复制到本地 Web 服务器的新目录中。 通过以下路径可在下载的代码中找到这些文件。

* views/index.ejs（将后缀改为 .html）
* views/jwt.ejs（将后缀改为 .html）
* views/info.ejs（将后缀改为 .html）
* public/*（JavaScript 文件、CSS、映像如下所示）

1. 将在 view 文件夹中找到的文件复制到新目录的根目录下。
1. 将在 public 文件夹中找到的文件夹复制到新目录的根目录下 。
1. 将 `.ejs` 文件的扩展名更改为 `.html`。 （没有使用服务器端变量，因此可以安全地对其进行更改。）
1. 在 VSC（或其他代码编辑器）打开 index.html，并更改 `<script>` 和 `<link>` 路径，使其反映文件所在的位置。  如果按照前面的步骤操作，则只需删除路径中的 `\`。  例如，`<script type="text/javascript" src="/javascript/constants.js"></script>` 重命名为 `<script type="text/javascript" src="javascript/constants.js"></script>`。
1. 自定义 javascript/constants.js 文件中的常量，如选项 1 中所述。

## <a name="common-customer-scenarios"></a>常见的客户方案

现在，你已经完成了本教程并拥有一个正常工作的子系统，可以尝试针对以下客户方案修改该子系统：

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>基于角色的访问控制 (RBAC)，用于通过 Azure AD 组成员资格传送许可证

到目前为止，系统允许任何能够登录的用户获取有效的许可证并播放受保护的内容。

这是一种常见的客户要求，即允许一部分经过身份验证的用户观看内容，而其他用户则不能，例如为其视频内容提供基本和高级订阅的客户。 购买基本订阅的客户不能观看需要高级订阅的内容。 以下是满足此要求所需的其他步骤：

#### <a name="set-up-the-azure-ad-tenant"></a>设置 Azure AD 租户

1. 在租户中设置两个帐户。 可将它们命名为 premium_user 和 basic_user ；
1. 创建一个用户组并将其命名为 PremiumGroup。
1. 将 premium_user 作为成员添加到 PremiumGroup，但不要将 basic_user 添加到该组  。
1. 记下 PremiumGroup 的对象 ID。

#### <a name="set-up-the-media-services-account"></a>设置 Media Services 帐户

通过添加名为分组的声明来修改 `ContentKeyPolicyRestriction`（如上面“媒体服务帐户中的设置”部分中所示），其中 `ida_EntitledGroupObjectId` 以 PremiumGroup 的对象 ID 作为其值 ：

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

组声明是 Azure AD 中的[受限声明集](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets)的成员。

#### <a name="test"></a>测试

1. 使用 premium_user 帐户登录。 你应该能够播放受保护的内容。
1. 使用 basic_user 帐户登录。 你会收到一条错误消息，指示视频已加密，但没有密钥可对其进行解密。 如果使用播放器诊断覆盖底部的下拉列表查看事件、错误和下载，则错误消息应指示由于 Azure AD 令牌终结点发布的 JWT 中的组声明的声明值丢失而导致许可证获取失败。

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>支持多个媒体服务帐户（跨多个订阅）

一个客户可在单个或多个 Azure 订阅中拥有多个媒体服务帐户。 例如，客户可能将一个媒体服务帐户作为生产主帐户，另一个作为辅助/备份帐户，还有一个作为开发/测试帐户。

你需要做的就是确保在所有媒体服务帐户中创建 `ContentKeyPolicyRestriction` 时，使用与“媒体服务帐户中的设置”部分相同的一组参数。

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>在多个 AAD 租户中为客户、其供应商和/或子公司提供支持

作为解决方案的用户，客户的子公司、供应商/合作伙伴可能驻留在不同的 AAD 租户中，如 `mycustomer.com`、`mysubsidiary.com` 和 `myparther.com`。 虽然此解决方案基于单个特定 AAD 租户（如 `mycustomer.com`）生成，但你可以将其用于其他租户的用户。

使用此解决方案的 `mycustomer.com`，将 `mypartner.com` 中的用户作为来宾用户添加到 `mycustomer.com` 租户。 确保 `mypartner.com` 用户激活来宾帐户。 来宾帐户可以来自另一个 AAD 租户或 `outlook.com` 帐户。

请注意，来自 `mypartner.com` 的来宾用户在 `mycustomer.com` 中激活后，仍通过其自己的/原始的 AAD 租户 `mypartner.com` 进行身份验证，但是 `access_token` 由 `mycustomer.com` 颁发。

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>使用订阅/租户中的设置支持客户租户/订阅

你可以使用自己的设置来测试客户媒体服务帐户/订阅中受保护的内容。 你将使用同一订阅中的 Azure AD 租户和媒体服务帐户进行设置。 可在客户的 Azure 订阅（包含 Azure AD 租户）中找到其媒体服务帐户。

1. 将客户帐户作为来宾帐户添加到租户中。
1. 通过提供“媒体服务帐户中的设置”部分列出的三个参数，与你的客户一起在客户的媒体服务帐户中准备受保护的内容。

然后，你的客户可以浏览到你的设置，使用来宾帐户登录，并测试其自己的受保护内容。 你还可以用自己的帐户登录，并测试客户的内容。

可以在具有 Microsoft 订阅的 Microsoft 租户或具有 Microsoft 订阅的自定义租户中设置示例解决方案。 Azure 媒体服务实例可以来自与其租户不同的订阅。

## <a name="clean-up-resources"></a>清理资源

> [!WARNING]
> 如果不打算继续使用此应用程序，请删除本教程中创建的资源。 否则，将向你收取费用。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [快速入门：加密内容](encrypt-content-quickstart.md)
