---
title: "Azure AD v2 JS SPA 指导安装程序-配置 |Microsoft 文档"
description: "JavaScript SPA 应用程序如何才能通过 Azure Active Directory v2 终结点调用需要访问令牌的 API"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: adff529bfdc40006666cc643d49a302d7f1d09ad
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
## <a name="register-your-application"></a>注册应用程序

有多种方法来创建应用程序，请选择其中之一：

### <a name="option-1-register-your-application-express-mode"></a>选项 1： 注册你的应用程序 （快速模式）
现在需要在 Microsoft 应用程序注册门户中注册应用程序：

1.  通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)注册应用程序
2.  输入应用程序的名称和电子邮件
3.  确保选中“指导式设置”选项
4.  按照说明获取应用程序 ID，并将其粘贴到代码中

### <a name="option-2-register-your-application-advanced-mode"></a>选项 2： 注册你的应用程序 （高级模式）

1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)注册应用程序
2. 输入应用程序的名称和电子邮件 
3. 确保取消选中“指导式设置”选项
4.  单击 `Add Platform`，并选择 `Web`
5. 添加`Redirect URL`对应的基于 web 服务器上的应用程序的 URL。 有关如何设置 / 获取 Visual Studio 和 Python 中的重定向 URL，请参阅下面的部分将有关的说明。
6. 单击“保存”

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>获取重定向 URL 的 Visual Studio 说明
> 请按照说明来获取你重定向 URL:
> 1.    在解决方案资源管理器中，选择项目并查看 `Properties` 窗口（如果看不到“属性”窗口，请按 `F4`）
> 2.    将 `URL` 中的值复制到剪贴板：<br/> ![项目属性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    切换回*应用程序注册门户*粘贴的值作为`Redirect URL`，然后单击保存

<p/>

> #### <a name="setting-redirect-url-for-python"></a>设置 Python 的重定向 URL
> 对于 Python，可通过命令行设置 Web 服务器端口。 此指南设置使用端口 8080 作为参考，但可以随意使用任何可用的其他端口。 在任何情况下，都请按照以下说明在应用程序注册信息中设置重定向 URL：<br/>
> - 切换回*应用程序注册门户*并设置`http://localhost:8080/`作为`Redirect URL`，或使用`http://localhost:[port]/`如果你使用的自定义的 TCP 端口 (其中*[端口]*是自定义的 TCP 端口号)，然后单击保存


#### <a name="configure-your-javascript-spa"></a>配置 JavaScript SPA

1.  创建包含应用程序注册信息且名为 `msalconfig.js` 的文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），然后右键单击并选择：`Add` > `New Item` > `JavaScript File`。 将其命名为 `msalconfig.js`
2.  将以下代码添加到 `msalconfig.js` 文件：

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
用刚注册的应用程序 ID 替换 <code>Enter_the_Application_Id_here</code>
</li>
</ol>
