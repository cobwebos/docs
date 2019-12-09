---
title: 将 Internet Explorer 用于 MSAL |Microsoft
titleSuffix: Microsoft identity platform
description: 在 Internet Explorer 浏览器中使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 715e92a10ca0b4cbe38119931bc66d36c3b9259a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916275"
---
# <a name="use-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>通过 MSAL 使用 Internet Explorer 和 Microsoft Edge 浏览器

将为[JAVASCRIPT ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29)生成适用于 javascript 的 Microsoft 身份验证库（MSAL），以便它可以在 Internet Explorer 中运行。 但有一些需要注意的事项。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中运行应用程序
如果你打算在可以在 Internet Explorer 中运行的应用程序中使用 MSAL，则在引用 MSAL 脚本之前，你将需要添加对承诺填充代码的引用。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

这是因为 Internet Explorer 不支持本机承诺 JavaScript。

## <a name="debugging-an-application-running-in-internet-explorer"></a>调试在 Internet Explorer 中运行的应用程序

### <a name="running-in-production"></a>在生产环境中运行
如果最终用户已接受弹出窗口，则将应用程序部署到生产环境（例如，在 Azure Web 应用中）通常可以正常工作。 我们在 Internet Explorer 11 中进行了测试。

### <a name="running-locally"></a>本地运行
如果你想要在 Internet Explorer 中运行的应用程序本地运行和调试，需要注意以下事项（假设你想要将应用程序作为 *http://localhost:1234* 运行）：

- Internet Explorer 具有一个名为 "protected mode" 的安全机制，这会阻止 MSAL 正常工作。 在这种情况下，可以在登录后将页面重定向到 http://localhost:1234/null 。

- 若要在本地运行和调试应用程序，需要禁用此 "保护模式"。 对于此：

    1. 单击 "Internet Explorer**工具**" （齿轮图标）。
    1. 选择 " **Internet 选项**"，然后选择 "**安全**" 选项卡。
    1. 单击 " **Internet** " 区域，然后取消选中 "**启用保护模式" （需要重新启动 Internet Explorer）** 。 Internet Explorer 警告您的计算机不再受保护。 单击 **“确定”** 。
    1. 重新启动 Internet Explorer。
    1. 运行和调试应用程序。

完成后，请还原 Internet Explorer 安全设置。  选择 "**设置**" -> **Internet 选项**" -> **安全** -> **将所有区域重置为默认级别**。

## <a name="next-steps"></a>后续步骤
了解有关[在 Internet Explorer 中使用 MSAL 时的已知问题的](msal-js-use-ie-browser.md)详细信息。