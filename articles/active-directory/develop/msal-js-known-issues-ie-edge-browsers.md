---
title: Internet Explorer & Microsoft Edge （MSAL）上的问题 |Microsoft
titleSuffix: Microsoft identity platform
description: 了解使用适用于 JavaScript 的 Microsoft 身份验证库（MSAL）与 Internet Explorer 和 Microsoft Edge 浏览器的已知问题。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3a4e847bc0d297f3aa41551fbf56242ae1a566f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424327"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Internet Explorer 和 Microsoft Edge 浏览器（MSAL）中的已知问题

## <a name="issues-due-to-security-zones"></a>安全区域导致的问题
由于在 IE 和 Microsoft Edge 中进行身份验证，我们提供了多个有关身份验证问题的报告（从*Microsoft edge 浏览器版本更新到 40.15063.0.0*）。 我们正在跟踪这些项目，并已通知 Microsoft Edge 团队。 虽然 Microsoft Edge 处理解决方案，但下面介绍了经常发生的问题，以及可以实现的可能的解决方法。

### <a name="cause"></a>原因
大多数此类问题的原因如下所示。 使用 Microsoft Edge 浏览器中的安全区域对会话存储和本地存储进行分区。 在此特定版本的 Microsoft Edge 中，当跨区域重定向应用程序时，将清除会话存储和本地存储。 具体而言，在常规浏览器导航中清除会话存储，并在浏览器的 InPrivate 模式下清除会话和本地存储。 MSAL 在会话存储中保存某些状态，并依赖于在身份验证流期间检查此状态。 当清除会话存储时，此状态会丢失，因而会导致体验中断。

### <a name="issues"></a>问题

- **身份验证期间的无限重定向循环和页面重载**。 当用户在 Microsoft Edge 上登录到应用程序时，它们将从 AAD 登录页重定向回来，并停滞在无限重定向循环中，导致重复页面重新加载。 这通常伴随会话存储中出现 `invalid_state` 错误。

- **无限获取令牌循环和 AADSTS50058 错误**。 当在 Microsoft Edge 上运行的应用程序尝试获取某个资源的令牌时，该应用程序可能会停滞获取令牌调用的无限循环，并在网络跟踪中从 AAD 收到以下错误：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **使用登录通过 popup 进行身份验证时，弹出窗口不会关闭或停滞**。 在 Microsoft Edge 或 IE （InPrivate）中通过弹出窗口进行身份验证时，如果导航中涉及到多个安全区域中的多个域，则弹出窗口不会关闭，因为 MSAL 丢失了弹出窗口。  

    下面是 Microsoft Edge 问题跟踪器中这些问题的链接：  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>更新：在 MSAL 中提供修复0.2。3
已在[MSAL 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)中发布身份验证重定向循环问题的修复程序。 在 MSAL 配置中启用标志 `storeAuthStateInCookie` 以利用此修补程序。 默认情况下，此标志设置为 false。

启用 `storeAuthStateInCookie` 标志后，MSAL 将使用浏览器 cookie 来存储验证身份验证流所需的请求状态。

> [!NOTE]
> 此修补程序尚不适用于 msal、msal 和 angularjs 包装器。 此修补程序不解决弹出窗口中的问题。

使用以下解决方法。

#### <a name="other-workarounds"></a>其他解决方法
请确保仅在特定版本的 Microsoft Edge 浏览器中测试你的问题，并在采用这些解决方法之前在其他浏览器上工作。  
1. 若要解决这些问题，请确保在浏览器的 "安全设置" 中将应用程序域、和所涉及的任何其他站点作为受信任站点添加到浏览器的安全设置，以便它们属于同一安全区域。
为此，请执行下列步骤：
    - 打开**Internet Explorer** ，并单击右上角的 "**设置**" （齿轮图标）
    - 选择**Internet 选项**
    - 选择 "**安全**" 选项卡
    - 在 "**受信任的站点**" 选项下，单击 "**站点**" 按钮，然后在打开的对话框中添加 url。

2. 如前所述，由于仅在常规导航过程中清除了会话存储，因此，你可以将 MSAL 配置为使用本地存储。 初始化 MSAL 时，可以将此设置为 `cacheLocation` config 参数。

请注意，这不会解决 InPrivate 浏览问题，因为会话和本地存储都已清除。

## <a name="issues-due-to-popup-blockers"></a>弹出窗口阻止程序的问题

在某些情况下，当在 IE 或 Microsoft Edge 中阻止弹出窗口时，例如当多重身份验证期间发生第二个弹出窗口时。 你将在浏览器中收到一个警报，允许弹出一次或始终使用。 如果选择 "允许"，浏览器会自动打开弹出窗口，并为其返回 `null` 句柄。 因此，库没有该窗口的句柄，因此无法关闭弹出窗口。 当 Chrome 提示你允许弹出窗口时，此问题不会出现在 Chrome 中，因为它不会自动打开弹出窗口。

作为一种**解决方法**，开发人员需要在 IE 和 Microsoft Edge 中允许弹出窗口，才能开始使用其应用来避免此问题。

## <a name="next-steps"></a>后续步骤
[在 Internet Explorer 中了解有关使用 MSAL 的](msal-js-use-ie-browser.md)详细信息。
