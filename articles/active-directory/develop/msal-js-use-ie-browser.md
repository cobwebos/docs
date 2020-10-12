---
title: Internet Explorer 上的问题 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 将适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 与 Internet Explorer 浏览器配合使用。
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
ms.openlocfilehash: 633166f3bb46212991920d6720737f8268b3f401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87026848"
---
# <a name="known-issues-on-internet-explorer-browsers-msaljs"></a>Internet Explorer 浏览器 (MSAL.js) 的已知问题

适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 是为 [JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) 生成的，因此可以在 Internet Explorer 中运行。 但是，需要注意几个问题。

## <a name="run-an-app-in-internet-explorer"></a>在 Internet Explorer 中运行应用
若要在能够在 Internet Explorer 中运行的应用程序中使用 MSAL.js，需在引用 MSAL.js 脚本之前添加对承诺填充代码的引用。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

这是因为，Internet Explorer 原本不支持 JavaScript 承诺。

## <a name="debugging-an-application-running-in-internet-explorer"></a>调试在 Internet Explorer 中运行的应用程序

### <a name="running-in-production"></a>在生产环境中运行
将应用程序部署到生产环境（例如，部署到 Azure Web 应用）通常是可行的，前提是最终用户已接受弹出窗口。 我们已在 Internet Explorer 11 中测试了它。

### <a name="running-locally"></a>在本地运行
若要在本地运行并调试在 Internet Explorer 中运行的应用程序，则需了解以下注意事项（假设要将应用程序作为 *http://localhost:1234* 运行）：

- Internet Explorer 有一个名为“保护模式”的安全机制，该机制会妨碍 MSAL.js 正常运行。 其症状是，在登录后，页面可能会重定向到 http://localhost:1234/null 。

- 若要在本地运行并调试应用程序，需禁用此“保护模式”。 为此，请执行以下操作：

    1. 单击 Internet Explorer 的“工具”  （齿轮图标）。
    1. 选择“Internet 选项”，然后选择“安全”选项卡  。 
    1. 单击“Internet”区域，然后取消选中“启用保护模式(需要重启 Internet Explorer)”选项卡  。  Internet Explorer 会警告：你的计算机不再受保护。 单击“确定”。 
    1. 重启 Internet Explorer。
    1. 运行并调试应用程序。

完成后，请还原 Internet Explorer 安全设置。  选择“设置”   -> “Internet 选项”   -> “安全”   ->   “将所有区域重置为默认级别”。

## <a name="next-steps"></a>后续步骤
详细了解[在 Internet Explorer 中使用 MSAL.js 时的已知问题](msal-js-use-ie-browser.md)。