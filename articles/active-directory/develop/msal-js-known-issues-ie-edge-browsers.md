---
title: Internet Explorer 和 Microsoft Edge (MSAL.js) 的问题 | Azure
titleSuffix: Microsoft identity platform
description: 了解将适用于 JavaScript 的 Microsoft 身份验证库 (MSAL.js) 与 Internet Explorer 和 Microsoft Edge 浏览器配合使用时的已知问题。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696089"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 和 Microsoft Edge 浏览器 (MSAL.js) 的已知问题

## <a name="issues-due-to-security-zones"></a>安全区域造成的问题
用户已多次向我们报告 IE 和 Microsoft Edge 中的身份验证存在问题（自从 Microsoft Edge 浏览器版本更新到 40.15063.0.0 开始）。** 我们正在跟踪这些问题，并已通知 Microsoft Edge 团队。 Microsoft Edge 正在努力寻求解决方法。下面是常见问题的描述，以及可以实施的可行解决方法。

### <a name="cause"></a>原因
其中大部分问题的原因如下。 会话存储和本地存储已在 Microsoft Edge 浏览器中按安全区域分区。 在此特定版本的 Microsoft Edge 中，如果应用程序跨区域重定向，则会清除会话存储和本地存储。 具体而言，在进行常规的浏览器导航时会清除会话存储，在浏览器的 InPrivate 模式下会清除会话存储和本地存储。 MSAL.js 在会话存储中保存特定的状态，在执行身份验证流期间依赖于检查此状态。 清除会话存储后，此状态将会丢失，导致体验受到破坏。

### <a name="issues"></a>问题

- **身份验证期间出现无限重定向循环和页面重载**。 当用户在 Microsoft Edge 中登录到应用程序时，他们会从 AAD 登录页重定向回到原始位置，并停滞在无限重定向循环中，导致页面反复重载。 通常，会话存储中还会附带出现 `invalid_state` 错误。

- **无限获取令牌循环和 AADSTS50058 错误**。 当 Microsoft Edge 中运行的应用程序尝试获取资源的令牌时，应用程序可能会停滞在获取令牌调用的无限循环中，同时 AAD 会在网络跟踪中发出以下错误：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **通过弹出窗口使用登录名进行身份验证时，弹出窗口不会关闭或处于停滞状态**。 通过 Microsoft Edge 或 IE (InPrivate) 中的弹出窗口进行身份验证时，在输入凭据并登录后，如果有多个跨安全区域的域涉及导航，则弹出窗口不会关闭，因为 MSAL.js 丢失了弹出窗口的句柄。  

### <a name="update-fix-available-in-msaljs-023"></a>更新： MSAL.js 0.2.3 中的修复可用
[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 中已发布身份验证重定向循环问题的修复措施。 在 MSAL.js 配置中启用标志 `storeAuthStateInCookie` 即可利用此修复措施。 此标志默认设置为 false。

如果已启用 `storeAuthStateInCookie` 标志，MSAL.js 将使用浏览器 Cookie 来存储验证身份验证流所需的请求状态。

> [!NOTE]
> 此修复措施尚不适用于 msal-angular 和 msal-angularjs 包装器。 此修复措施不能解决弹出窗口的问题。

请使用以下解决方法。

#### <a name="other-workarounds"></a>其他解决方法
在采用这些解决方法之前，请务必测试你的问题是否仅在特定版本的 Microsoft Edge 浏览器中发生，而在其他浏览器中则不会出现。  
1. 解决这些问题的第一步是确保应用程序域，以及涉及到身份验证流重定向的任何其他站点已在浏览器的安全设置中添加为受信任的站点，使之属于同一个安全区域。
为此，请执行下列步骤：
    - 打开“Internet Explorer”，单击右上角的“设置”（齿轮图标）********
    - 选择“Internet 选项”****
    - 选择 **"安全**"选项卡
    - 在“受信任的站点”选项下单击“站点”按钮，然后在打开的对话框中添加 URL。********

2. 如前所述，由于在常规导航期间只会清除会话存储，因此可将 MSAL.js 配置为改用本地存储。 可以在初始化 MSAL 时将此项设置为 `cacheLocation` 配置参数。

请注意，这不会解决 InPrivate 浏览问题，因为在此模式下，会同时清除会话存储和本地存储。

## <a name="issues-due-to-popup-blockers"></a>弹出窗口阻止程序造成的问题

有时，IE 或 Microsoft Edge 中会阻止弹出窗口，例如，在多重身份验证期间弹出另一个窗口。 浏览器中会显示警报，可让你选择允许弹出一次或始终允许弹出。 如果选择允许弹出，则浏览器会自动打开弹出窗口，并返回该窗口的 `null` 句柄。 因此，库不会获得该窗口的句柄，因此也就无法关闭弹出窗口。 当 Chrome 提示是否允许弹出窗口时不会发生相同的问题，因为它不会自动打开弹出窗口。

作为**解决方法**，开发人员需要在开始使用其应用之前在 IE 和 Microsoft Edge 中允许弹出窗口，这样就可以避免此问题。

## <a name="next-steps"></a>后续步骤
详细了解如何[在 Internet Explorer 中使用 MSAL.js](msal-js-use-ie-browser.md)。
