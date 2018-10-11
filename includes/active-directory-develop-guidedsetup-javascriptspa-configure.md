---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843022"
---
## <a name="register-your-application"></a>注册应用程序

可以通过多种方法来创建应用程序，请选择其中一种方法：

### <a name="option-1-register-your-application-express-mode"></a>选项 1：注册应用程序（快速模式）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：

1.  通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)注册应用程序。
2.  输入应用程序的名称和电子邮件。
3.  确保选中“指导式设置”选项。
4.  按照说明获取应用程序 ID，并将其粘贴到代码中。

### <a name="option-2-register-your-application-advanced-mode"></a>选项 2：注册应用程序（高级模式）

1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序。
2. 输入应用程序的名称和电子邮件。
3. 确保取消选中“指导式设置”选项。
4.  单击“添加平台”，然后选择“Web”。
5. 基于 Web 服务器，添加与应用程序 URL 对应的**重定向 URL**。 有关如何在 Visual Studio 和 Node 中设置和获取重定向 URL 的说明，请参阅以下部分。
6. 选择“保存”。

> #### <a name="setting-redirect-url-for-node"></a>设置 Node 的重定向 URL
> 对于 Node.js，可以在 *server.js* 文件中设置 Web 服务器端口。 本教程使用端口 30662 作为参考，但可以随意使用任何可用的其他端口。 在任何情况下，都请按照以下说明在应用程序注册信息中设置重定向 URL：<br/>
> - 切换回“应用程序注册门户”并将 `http://localhost:30662/` 设置为 `Redirect URL`，或者，如果你使用的是自定义 TCP 端口（其中[端口]为自定义 TCP 端口号），则使用 `http://localhost:[port]/`，然后单击“保存”

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>用于获取重定向 URL 的 Visual Studio 说明
> 按照以下步骤获取重定向 URL：
> 1.    在解决方案资源管理器中，选择项目并查看“属性”窗口。 如果没有看到“属性”窗口，请按 **F4**。
> 2.    将 **URL** 中的值复制到剪贴板：<br/> ![项目属性](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    切换回*应用程序注册门户*并将该值粘贴为**重定向 URL**，然后选择“保存”


#### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

1.  在设置项目期间创建的 `index.html` 文件中，添加应用程序注册信息。 在 `index.html` 文件正文的 `<script></script>` 标记顶部添加以下代码：

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
将 <code>Enter the application Id here</code> 替换为刚注册的应用程序 ID。
</li>
</ol>
