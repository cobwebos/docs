---
title: 已知问题在浏览器 （适用于 JavaScript 的 Microsoft 身份验证库） |Azure
description: 了解如何使用 javascript (MSAL.js) 的 Microsoft 身份验证库时知道问题与 Internet Explorer 和 Microsoft Edge 浏览器。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873895"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>MSAL.js 的 Internet Explorer 和 Microsoft Edge 浏览器上的已知的问题

## <a name="issues-due-to-security-zones"></a>由于安全区域的问题
我们有多个报表对 IE 和 Microsoft Edge 中的身份验证的问题 (因为的更新*40.15063.0.0 的 Microsoft Edge 浏览器版本*)。 我们正在跟踪这些，并已通知 Microsoft Edge 团队。 而 Microsoft Edge 适用于解析，以下是经常出现的问题和可能的解决方法，可实现的说明。

### <a name="cause"></a>原因
大多数问题的原因是，如下所示。 会话存储和本地存储进行分区的 Microsoft Edge 浏览器中的安全区域。 在此特定版本的 Microsoft Edge 中，当跨区域，该应用程序将被重定向会话存储和本地存储区清除。 具体而言，正则浏览器导航窗格中，清除会话存储并在浏览器的 InPrivate 模式中清除会话和本地存储。 MSAL.js 会话存储中保存某些状态，并且依赖于在身份验证流过程中检查此状态。 清除会话存储后，此状态将丢失，因此会导致中断的体验。

### <a name="issues"></a>问题

- **无限重定向循环和页面重新加载在身份验证期间**。 当用户登录到 Microsoft Edge 上的应用程序时，它们将被重定向回从 AAD 登录页和卡在无限重定向循环导致重复的页面重新加载。 这通常伴随着`invalid_state`会话存储中的错误。

- **无限获取令牌的循环和 AADSTS50058 错误**。 当在 Microsoft Edge 上运行的应用程序尝试获取资源的令牌时，应用程序可能会停滞在获取令牌中的调用以下的错误以及从 AAD 网络跟踪一个无限循环的状态：

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **弹出窗口不会关闭或处于停滞状态，使用弹出窗口通过登录进行身份验证时**。 因为 MSAL.js 失去的句柄进行身份验证时通过在 Microsoft Edge 或 IE(InPrivate)，弹出窗口后输入凭据并登录，如果在导航栏中，涉及到安全区域跨多个域不会关闭弹出窗口弹出窗口中。  

    下面是在 Microsoft Edge 问题跟踪程序中的这些问题的链接：  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>更新:MSAL.js 0.2.3 提供修复程序
修复了在中发布的身份验证重定向循环问题[MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)。 启用标志`storeAuthStateInCookie`MSAL.js 配置才能利用此修补程序中。 默认情况下此标志设置为 false。

当`storeAuthStateInCookie`启用标志，MSAL.js 将使用浏览器 cookie 来存储所需的身份验证流验证的请求状态。

> [!NOTE]
> 此修补程序尚不可用 msal angular 和 msal angularjs 包装器。 此修补程序不会解决弹出窗口中的问题。

使用以下解决方法。

#### <a name="other-workarounds"></a>其他解决方法
请确保测试采用以下解决方法之前在问题发生仅上的特定版本的 Microsoft Edge 浏览器和其他浏览器上的工作原理。  
1. 若要解决这些问题的第一步，确保应用程序域，并且任何其他站点中的身份验证流重定向涉及将添加为受信任的站点中的浏览器中，安全设置，以便它们属于同一安全区域。
为此，请执行下列步骤：
    - 打开**Internet Explorer** ，然后单击**设置**（齿轮图标） 中右上角
    - 选择**Internet 选项**
    - 选择**安全**选项卡
    - 下**受信任的站点**选项，请单击**站点**按钮，然后在打开的对话框中添加 Url。

2. 如前文所述仅会话之后存储清除在常规导航过程之前，可能会配置 MSAL.js 以改为使用本地存储。 这可以将设置为`cacheLocation`初始化 MSAL 时的配置参数。

请注意，这不会解决有关 InPrivate 浏览会话和本地存储系统会清除该问题。

## <a name="issues-due-to-popup-blockers"></a>由于弹出窗口阻止程序问题导致的问题

有些情况下在 IE 或 Microsoft Edge，例如多重身份验证过程中发生第二个弹出窗口时，已阻止弹出窗口。 在浏览器以一次，也始终允许弹出窗口中，将收到警报。 如果你选择允许，在浏览器会自动打开的弹出窗口中，并返回`null`处理它。 因此，库不具有在窗口的句柄，并且没有办法关闭弹出窗口。 相同的问题不会发生在 Chrome 中时它会提示您允许显示弹出式窗口，因为它不会自动打开一个弹出窗口。

作为**解决方法**，开发人员将需要允许显示弹出式窗口在 IE 和 Microsoft Edge 中的，可以先使用自己的应用程序，以避免此问题。

## <a name="next-steps"></a>后续步骤
详细了解如何[Internet Explorer 中使用 MSAL.js](msal-js-use-ie-browser.md)。
