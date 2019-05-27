---
title: 使用 Internet Explorer （适用于 JavaScript 的 Microsoft 身份验证库） |Azure
description: 了解如何使用 Internet Explorer 浏览器中使用 JavaScript (MSAL.js) 的 Microsoft 身份验证库。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf8c84120f4c90d3943cfc31ffbf9aafcec0ba3
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873910"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js 的 Internet Explorer 和 Microsoft Edge 浏览器上的已知的问题

为生成的 JavaScript (MSAL.js) 的 Microsoft 身份验证库[JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) ，以便它可以在 Internet Explorer 中运行。 但是，存在一些需要了解的一些事项。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中运行应用
如果你想要使用 MSAL.js 可以在 Internet Explorer 中运行的应用程序中，您需要引用 MSAL.js 脚本之前添加对承诺提供填充代码的引用。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

这是因为 Internet Explorer 不以本机方式支持 JavaScript 承诺。

## <a name="debugging-an-application-running-in-internet-explorer"></a>调试 Internet Explorer 中运行的应用程序

### <a name="running-in-production"></a>在生产中运行
通常情况下部署到生产环境 （例如在 Azure Web 应用） 应用程序正常运行，提供最终用户已接受弹出窗口。 经过测试，它使用 Internet Explorer 11。

### <a name="running-locally"></a>在本地运行
如果你想要本地运行和调试在 Internet Explorer 中运行的应用程序，需要注意以下事项 (假设你想要运行应用程序作为*http://localhost:1234*):

- Internet Explorer 具有名为"受保护的模式"，这样可防止 MSAL.js 正常工作的安全机制。 症状，在你登录后页面可重定向到 http://localhost:1234/null。

- 若要运行和调试本地应用程序，你将需要禁用此"受保护的模式"。 为以下内容：

    1. 单击 Internet Explorer**工具**（齿轮图标）。
    1. 选择**Internet 选项**，然后**安全**选项卡。
    1. 单击**Internet**区域，并取消选中**启用保护模式 （需要重新启动 Internet Explorer）**。 Internet Explorer 会警告您的计算机不再受保护。 单击“确定”。
    1. 重新启动 Internet Explorer。
    1. 运行和调试你的应用程序。

完成后，还原 Internet Explorer 安全设置。  选择**设置** -> **Internet 选项** -> **安全** -> **将所有区域重都置为默认级别**.

## <a name="next-steps"></a>后续步骤
详细了解如何[已知问题时在 Internet Explorer 中使用 MSAL.js](msal-js-use-ie-browser.md)。