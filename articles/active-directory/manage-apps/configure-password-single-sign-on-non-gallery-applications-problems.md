---
title: 为非库应用配置密码 SSO 的问题
description: 为不在 Azure AD 应用程序库中的自定义应用配置密码单一登录（SSO）时出现的常见问题。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274136"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>为非库应用程序配置密码单一登录时遇到的问题

本文介绍为非库应用配置*密码单一登录*（SSO）时可能出现的常见问题。

## <a name="capture-sign-in-fields-for-an-app"></a>捕获应用程序的登录字段

仅支持 HTML 的登录页支持登录字段捕获。 非标准登录页面不支持该功能，如使用 Adobe Flash 或其他未启用 HTML 的技术的页面。

可以通过两种方法来捕获自定义应用的登录字段：

- 如果用户为 "用户名" 和 "密码" 字段使用众所周知的 " *DIV id* "，**自动登录字段捕获**适用于大多数已启用 HTML 的登录页。 页面上的 HTML 擦除查找与特定条件匹配的 DIV Id。 保存元数据，以便以后可将其重播到应用中。

- 如果应用供应商*未标记登录输入字段*，则使用**手动登录字段捕获**。 如果供应商*呈现多个无法自动检测到的字段，* 则也可以使用手动捕获。 Azure Active Directory （Azure AD）可将多个字段的数据存储在登录页上，如果您告诉它字段在页面上的位置。

通常，如果自动登录字段捕获不起作用，请尝试使用 "手动" 选项。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自动捕获应用程序的登录字段

若要使用自动登录字段捕获来配置基于密码的 SSO，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 以 "全局管理员" 或 "共同管理员" 身份登录。

2. 在左侧的导航窗格中，选择 "**所有服务**" 打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 "**所有应用程序**" 列表顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 在应用程序加载后，在左侧的导航窗格中选择 "**单一登录**"。

8. 选择 **"基于密码的登录模式"** 。

9. 输入 "**登录 url**"，它是用户在其中输入用户名和密码以登录的页面的 url。 *确保登录字段在提供的 URL 的页面上可见*。

10. 选择“保存”。

    对于 "用户名" 和 "密码" 输入框，会自动擦除页面。 你现在可以使用 Azure AD 通过访问面板浏览器扩展安全地将密码传输到该应用程序。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手动捕获应用程序的登录字段

若要手动捕获登录字段，你必须已安装访问面板浏览器扩展。 此外，浏览器不能在*inPrivate*、 *incognito*或*private*模式下运行。

若要安装该扩展，请参阅本文的[安装访问面板浏览器扩展](#install-the-access-panel-browser-extension)部分。

若要使用手动登录字段捕获配置应用程序的基于密码的 SSO，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 以 "全局管理员" 或 "共同管理员" 身份登录。

2. 在左侧的导航窗格中，选择 "**所有服务**" 打开 Azure AD 扩展。

3. 在筛选器搜索框中键入**Azure Active Directory** ，然后选择 " **Azure Active Directory**"。

4. 在 Azure AD 导航窗格中选择 "**企业应用程序**"。

5. 选择 "**所有应用程序**" 以查看应用列表。

   > [!NOTE] 
   > 如果看不到所需的应用，请使用 "**所有应用程序**" 列表顶部的 "**筛选器**" 控件。 将 "**显示**" 选项设置为 "所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 在应用程序加载后，在左侧的导航窗格中选择 "**单一登录**"。

8. 选择 **"基于密码的登录模式"** 。

9. 输入 "**登录 URL**"，用户可在其中输入用户名和密码进行登录。 *确保登录字段在提供的 URL 的页面上可见*。

10. 选择 "**配置 *&lt;appname&gt;* Password 单一登录设置"** 。

11. 选择 **"手动检测登录字段"** 。

14. 选择“确定”。

15. 选择“保存”。

16. 按照说明使用访问面板。

## <a name="troubleshoot-problems"></a>解决问题

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到 "找不到任何登录字段的 URL" 错误

当自动检测登录字段失败时，会收到此错误消息。 若要解决此问题，请尝试手动登录字段检测。 请参阅本文的[手动捕获应用程序的登录字段](#manually-capture-sign-in-fields-for-an-app)部分。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到 "无法保存单一登录配置" 错误

很少，更新 SSO 配置将失败。 若要解决此问题，请尝试再次保存配置。

如果仍然遇到此错误，请打开支持案例。 请包括 "[查看门户通知详细信息](#view-portal-notification-details)" 和 "向[支持工程师发送通知详细](#send-notification-details-to-a-support-engineer-to-get-help)信息" 中所述的信息，以获取本文的帮助部分。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我无法手动检测应用程序的登录字段

手动检测不起作用时，可能会观察到以下行为：

- 手动捕获进程似乎正在运行，但捕获的字段不正确。

- 当捕获进程运行时，正确的字段不突出显示。

- 捕获过程会将您转到应用程序的登录页，但没有任何反应。

- 手动捕获似乎有效，但当用户从访问面板导航到应用时，不会发生 SSO。

如果遇到这些问题，请执行以下操作：

- 请确保已*安装并启用*了访问面板浏览器扩展的最新版本。 请参阅本文的[安装访问面板浏览器扩展](#install-the-access-panel-browser-extension)部分。

- 在捕获过程中，请确保你的浏览器不处于*incognito*、 *inPrivate*或*私有*模式。 在这些模式下不支持访问面板扩展。

- 确保在*incognito*、 *inPrivate*或*私有模式下*，用户不会在访问面板中尝试登录到应用。

- 再次尝试手动捕获进程。 请确保红色标记在正确的字段上。

- 如果手动捕获进程似乎停止响应或登录页面未响应，请再次尝试手动捕获进程。 但这一次，在完成此过程后，按 F12 键即可打开浏览器的开发人员控制台。 选择 "**控制台**" 选项卡。 "**位置 =" *&lt;在配置应用时指定的登录 URL&gt;* ""** ，然后按 enter。 这会强制执行页面重定向，以结束捕获过程并存储已捕获的字段。

### <a name="contact-support"></a>联系支持人员

如果仍有问题，请使用 Microsoft 支持部门打开案例。 描述您尝试的操作。 请包括 "[查看门户通知详细信息](#view-portal-notification-details)" 和 "向[支持工程师发送通知详细](#send-notification-details-to-a-support-engineer-to-get-help)信息" 中所述的详细信息（如果适用）。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

执行以下步骤：

1. 在支持的浏览器中打开[访问面板](https://myapps.microsoft.com)。 以*用户*身份登录到 Azure AD。

2. 在访问面板中选择 "**密码-SSO 应用程序**"。

3. 如果系统提示你安装软件，请选择 "**立即安装**"。

4. 你将被定向到浏览器的下载页。 选择**添加**该扩展。

5. 如果系统提示，请选择 "**启用**" 或 "**允许**"。

6. 安装完成后，重新启动浏览器。

7. 登录到访问面板。 查看是否可以打开启用了密码 SSO 的应用。

还可通过以下链接直接下载适用于 Chrome 和 Firefox 的浏览器扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>查看门户通知的详细信息

若要查看任何门户通知的详细信息，请执行以下步骤：

1. 选择 Azure 门户右上角的 "**通知**" 图标（电铃）。

2. 选择显示*错误*状态的任何通知。 （它们具有红色的 "！"。）

   > [!NOTE]
   > 你无法选择处于 "*成功*" 或 "*正在进行*" 状态的通知。

3. “通知详细信息”窗格随即打开。 阅读有关此问题的信息。

5. 如果仍需要帮助，请与支持工程师或产品组共享此信息。 选择 "**复制错误**" 框右侧的 "**复制**" 图标，将通知详细信息复制到 "共享"。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>将通知详细信息发送给支持工程师以获取帮助

请务必共享此部分中列出的*所有*详细信息并提供支持，以便他们能够快速帮助你。 要记录它，可以拍摄屏幕快照或选择 "**复制错误**"。

以下信息说明每个通知项的意思，并提供示例。

### <a name="essential-notification-items"></a>基本通知项目

- **标题**：通知的描述性标题。

   示例：*应用程序代理设置*

- **说明**：操作导致的结果。

   示例：*输入的内部 URL 已被其他应用程序使用。*

- **通知 ID**：通知的唯一 ID。

    示例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **客户端请求 id**：浏览器发出的特定请求 id。

    示例： *302fd775-3329-4670-a9f3-bea37004f0bc*

- **时间戳 utc**：通知出现时间的时间戳（utc）。

    示例： *2017-03-23T19：50： 43.7583681 z*

- **内部事务 ID**：用于在我们的系统中查找错误的内部 ID。

    示例： **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**：运行操作的用户。

    示例： *tperkins\@f128.info*

- **租户 ID**：运行操作的用户所属的租户的唯一 ID。

    示例： *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **用户对象 id**：运行操作的用户的唯一 id。

    示例： *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>详细通知项

- **显示名称**：（可以为空）错误的更详细显示名称。

    示例：*应用程序代理设置*

- **状态**：通知的特定状态。

    示例： *Failed*

- **对象 id**：（可以为空）运行操作的对象 id。

   示例： *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **详细信息**：操作导致的操作的详细说明。

    示例：*内部 url "<https://bing.com/>" 无效，因为它已在使用中。*

- **复制错误**：使你能够选择 "**复制错误**" 文本框右侧的 "**复制" 图标**，复制通知详细信息以帮助提供支持。

    示例： ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
