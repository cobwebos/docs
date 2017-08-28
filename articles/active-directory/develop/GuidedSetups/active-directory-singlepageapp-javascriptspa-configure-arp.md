---
title: "Azure AD v2 JS SPA 设置指南 - 配置 (ARP) | Microsoft Docs"
description: "JavaScript SPA 应用程序如何通过 Azure Active Directory v2 终结点 (ARP) 调用需要访问令牌的 API"
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 735559464db4c237ff7498b7350f2385090fa4e4
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---
## <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息

在此步骤中，需要配置应用程序注册信息的重定向 URL，然后将应用程序 ID 添加到 JavaScript SPA 应用程序。

### <a name="configure-redirect-url"></a>配置重定向 URL

使用基于 Web 服务器的 index.html 页面的 URL 配置上述 `Redirect URL` 字段，然后单击“更新”。


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>获取重定向 URL 的 Visual Studio 说明
> 要获取重定向 URL，请根据以下说明进行操作：
> 1.    在解决方案资源管理器中，选择项目并查看 `Properties` 窗口（如果看不到“属性”窗口，请按 `F4`）
> 2.    将 `URL` 中的值复制到剪贴板：<br/> ![项目属性](media/active-directory-singlepageapp-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    将此值粘贴到此页顶部的 `Redirect URL`，然后单击 `Update`

<p/>

> #### <a name="setting-redirect-url-for-python"></a>设置 Python 的重定向 URL
> 对于 Python，可通过命令行设置 Web 服务器端口。 此指南设置使用端口 8080 作为参考，但可以随意使用任何可用的其他端口。 在任何情况下，都请按照以下说明在应用程序注册信息中设置重定向 URL：<br/>
> 将此页面顶部的 `Redirect URL` 设置为 `http://localhost:8080/`，如果使用自定义 TCP 端口（其中 [端口] 是自定义 TCP 端口号），则使用 `http://localhost:[port]/`，然后单击“更新”

### <a name="configure-your-javascript-spa-application"></a>配置 JavaScript SPA 应用程序

1.  创建包含应用程序注册信息且名为 `msalconfig.js` 的文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），然后右键单击并选择：`Add` > `New Item` > `JavaScript File`。 将其命名为 `msalconfig.js`
2.  将以下代码添加到 `msalconfig.js` 文件：

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 

