---
title: 有关 Internet Explorer 和 Microsoft Edge (MSAL.js) 的问题 | Azure
titleSuffix: Microsoft identity platform
description: 了解在 Internet Explorer 和 Microsoft Edge 浏览器中使用适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 的已知问题。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83772074"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>有关 Internet Explorer 和 Microsoft Edge 浏览器 (MSAL.js) 的已知问题

## <a name="issues-due-to-security-zones"></a>安全区域引起的问题
我们收到了关于 IE 和 Microsoft Edge 中身份验证问题的多个报告（自从将 *Microsoft Edge 浏览器版本更新为 40.15063.0.0* 以来）。 我们正在跟踪这些问题，并已通知 Microsoft Edge 团队。 在 Microsoft Edge 努力寻找解决方法的同时，我们提供了一些对常见问题的说明以及可以实施的可能的解决方法。

### <a name="cause"></a>原因
导致大多数此类问题的原因如下。 会话存储和本地存储按 Microsoft Edge 浏览器中的安全区域进行分区。 在这个特定版本的 Microsoft Edge 中，当跨区域重定向应用程序时，将清除会话存储和本地存储。 具体而言，在常规浏览器导航中清除会话存储，在浏览器的 InPrivate 模式下清除会话存储和本地存储。 MSAL.js 将某些状态保存在会话存储中，并在身份验证流期间依赖于检查此状态。 清除会话存储时，此状态会丢失，从而导致体验中断。

### <a name="issues"></a>问题

- **身份验证期间无限重定向循环和页面重载**。 当用户在 Microsoft Edge 上登录到应用程序时，他们将从 AAD 登录页面重定向回原来的页面，并陷入无限重定向循环中，从而导致反复进行页面重载。 这通常伴随着会话存储中的 `invalid_state` 错误。

- **无限获取令牌循环和 AADSTS50058 错误**。 当 Microsoft Edge 上运行的应用程序尝试获取资源令牌时，该应用程序可能会陷入获取令牌调用的无限循环中，并在网络跟踪中从 AAD 收到以下错误：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **通过弹出窗口进行身份验证来完成登录时，弹出窗口无法关闭或卡住不动**。 通过 Microsoft Edge 或 IE(InPrivate) 中的弹出窗口进行身份验证时，输入凭据并登录后，如果导航中涉及跨安全区域的多个域，则会因为 MSAL.js 丢失弹出窗口句柄而无法关闭弹出窗口。  

### <a name="update-fix-available-in-msaljs-023"></a>更新：MSAL.js 0.2.3 中提供的修补程序
[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 发布了针对身份验证重定向循环问题的修补程序。 在 MSAL.js 配置中启用标志 `storeAuthStateInCookie` 可利用此修补程序。 默认情况下，此标志设置为 false。

启用 `storeAuthStateInCookie` 标志后，MSAL.js 将使用浏览器 Cookie 存储验证身份验证流所需的请求状态。

> [!NOTE]
> 此修补程序尚不适用于 msal-angular 和 msal-angularjs 包装器。 此修补程序无法解决弹出窗口的问题。

使用以下解决方法。

#### <a name="other-workarounds"></a>其他解决方法
在采用这些解决方法之前，请确保测试你的问题仅出现在特定版本的 Microsoft Edge 浏览器中，在其他浏览器上可以正常运行。  
1. 若要解决这些问题，第一步是确保将身份验证流重定向中涉及的应用程序域和任何其他站点添加为浏览器安全设置中的受信任站点，以使它们属于同一安全区域。
为此，请执行下列步骤：
    - 打开“Internet Explorer”，单击右上角的“设置”（齿轮图标） 。
    - 选择“Internet 选项”
    - 选择“安全”选项卡
    - 在“受信任的站点”选项下，单击“站点”按钮，然后在打开的对话框中添加 URL。

2. 如前所述，由于在常规导航期间仅清除会话存储，因此，可以将 MSAL.js 配置为改用本地存储。 初始化 MSAL 时，可以将其设置为 `cacheLocation` 配置参数。

请注意，这不会解决 InPrivate 浏览问题，因为会话存储和本地存储都会被清除。

## <a name="issues-due-to-popup-blockers"></a>弹出窗口阻止程序引起的问题

有时会在 IE 或 Microsoft Edge 中阻止弹出窗口，例如在[多重身份验证](../authentication/concept-mfa-howitworks.md)期间出现第二个弹出窗口时。 你将在浏览器中收到警报，以允许弹出一次或始终弹出。 如果选择允许，浏览器将自动打开弹出窗口并为其返回 `null` 句柄。 因此，身份验证库没有该窗口的句柄，从而无法关闭弹出窗口。 当 Chrome 提示你允许弹出窗口时，由于它不会自动打开弹出窗口，因此不会发生相同的问题。

作为解决方法，开发人员需要在开始使用其应用之前，在 IE 和 Microsoft Edge 中允许弹出窗口，以避免此问题。

## <a name="next-steps"></a>后续步骤
详细了解[在 Internet Explorer 中使用 MSAL.js](msal-js-use-ie-browser.md)。
