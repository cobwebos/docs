---
title: "如何使用适用于 Azure 移动应用的 JavaScript SDK"
description: "如何为 Azure 移动应用使用 v"
services: app-service\mobile
documentationcenter: javascript
author: adrianhall
manager: erikre
editor: 
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: d6e68497cf0a4c48e6f611834089224b50b9bd5e
ms.openlocfilehash: 79743cab91f8c22d06796803eb7e31681a581233


---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>如何使用适用于 Azure 移动应用的 JavaScript 客户端库
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南介绍如何使用最新的[适用于 Azure 移动应用的 JavaScript SDK] 执行常见任务。 对于 Azure 移动应用的新手，请先完成 [Azure 移动应用快速入门]创建后端和表。 本指南着重介绍如何在 HTML/JavaScript Web 应用程序中使用移动后端。

## <a name="supported-platforms"></a>支持的平台
我们将浏览器支持限于当前版本和最新版本的主要浏览器：Google Chrome、Microsoft Edge、Microsoft Internet Explorer 和 Mozilla Firefox。  我们期望 SDK 能与任何相对新式的浏览器一起运作。

由于包已被分发为通用 JavaScript 模块，因此它支持全局、AMD 和 CommonJS 格式。

## <a name="a-namesetupasetup-and-prerequisites"></a><a name="Setup"></a>安装与先决条件
本指南假设已创建了包含表的后端。 本指南假设该表的架构与这些教程中的表相同。

可以通过 `npm` 命令安装 Azure 移动应用 JavaScript SDK：

```
npm install azure-mobile-apps-client --save
```

也可将库用作 CommonJS 环境（例如 Browserify 和 Webpack）中的 ES2015 模块，或者用作 AMD 库。  例如：

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

还可以通过直接从 CDN 下载来使用 SDK 的预建版本：

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="a-nameauthahow-to-authenticate-users"></a><a name="auth"></a>如何：对用户进行身份验证
Azure 应用服务支持使用各种外部标识提供者（例如 Facebook、Google、Microsoft 帐户和 Twitter）对应用的用户进行身份验证和授权。 你可以在表中设置权限，以便将特定操作的访问权限限制给已经过身份验证的用户。 你还可以在服务器脚本中使用已经过身份验证的用户的标识来实施授权规则。 有关详细信息，请参阅[身份验证入门]教程。

支持两种身份验证流：服务器流和客户端流。  服务器流依赖于提供者的 Web 身份验证界面，因此可提供最简便的身份验证体验。 客户端流依赖于提供程序特定的 SDK，因此允许与设备特定的功能（例如单一登录）进行更深入的集成。

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="a-nameconfigure-external-redirect-urlsahow-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>如何为外部重定向 URL 配置移动应用服务。
有多种类型的 JavaScript 应用程序使用环回功能来处理 OAuth UI 流。  这些功能包括：

* 在本地运行服务
* 搭配使用实时重载和 Ionic 框架
* 重定向至应用服务进行身份验证。 

在本地运行可能会导致问题产生，因为默认情况下，应用服务身份验证只配置为允许从移动应用后端访问。 使用以下步骤更改应用服务设置，允许在本地运行服务器时进行身份验证：

1. 登录到 [Azure 门户]
2. 导航到移动应用后端。
3. 在“开发工具”菜单中，选择“资源浏览器”。
4. 单击“开始”在新选项卡或窗口中打开移动应用后端的资源管理器。
5. 展开应用的“config” > “authsettings”节点。
6. 单击“编辑”按钮可对资源进行编辑。
7. 查找应为 null 的“allowedExternalRedirectUrls”元素。 在数组中添加 URL：
   
         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],
   
    将数组中的 URL 替换为服务的 URL，在本示例中为本地 Node.js 示例服务的 `http://localhost:3000`。 对于 Ripple 服务，也可以根据应用的配置方式，使用 `http://localhost:4400` 或其他某个 URL。
8. 在页面顶部，单击“读/写”，然后单击“PUT”保存更新。

还需要将相同的环回 URL 添加到 CORS 白名单设置：

1. 导航回到 [Azure 门户]。
2. 导航到移动应用后端。
3. 在“API”菜单中单击“CORS”。
4. 在空的“允许的来源”文本框中输入每个 URL。  将创建新的文本框。
5. 单击“保存”

后端更新后，可以在应用中使用新的环回 URL。

<!-- URLs. -->
[Azure 移动应用快速入门]: app-service-mobile-cordova-get-started.md
[身份验证入门]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure 门户]: https://portal.azure.com/
[适用于 Azure 移动应用的 JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/en-us/library/azure/jj613353.aspx




<!--HONumber=Dec16_HO1-->


