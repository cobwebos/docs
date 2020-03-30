---
title: 为非库应用配置密码 SSO 时出现问题
description: 为 Azure AD 应用程序库中未使用的自定义应用配置密码单一登录 （SSO） 时出现的常见问题。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274136"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>为非库应用程序配置密码单一登录时遇到问题

本文介绍了为非库应用配置*密码单一登录*（SSO） 时可能出现的常见问题。

## <a name="capture-sign-in-fields-for-an-app"></a>捕获应用的登录字段

登录字段捕获仅支持启用 HTML 的登录页。 不支持非标准登录页，例如使用 Adobe Flash 或其他非 HTML 支持的技术的登录页。

有两种方法可以捕获自定义应用的登录字段：

- **如果自动登录字段捕获**对用户名和密码字段*使用众所周知的 DIV ID，* 则适用于大多数启用 HTML 的登录页。 页面上的 HTML 被刮去以查找符合特定条件的 DIV ID。 该元数据将被保存，以便以后可以重播到应用。

- 如果应用供应商*未标记登录输入字段*，则使用**手动登录字段捕获**。 如果供应商*呈现多个无法自动检测到的字段，* 则也使用手动捕获。 Azure 活动目录 （Azure AD） 可以存储与登录页上相同的字段的数据（如果告诉它这些字段在页面上的位置）。

通常，如果自动登录字段捕获不起作用，请尝试手动选项。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自动捕获应用的登录字段

要使用自动登录字段捕获配置基于密码的 SSO，请按照以下步骤操作：

1. 打开[Azure 门户](https://portal.azure.com/)。 以全局管理员或共同管理员身份登录。

2. 在左侧的导航窗格中，选择 **"所有服务**"以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用列表。

   > [!NOTE]
   > 如果看不到所需的应用，请使用 **"所有应用程序**"列表顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 加载应用后，在左侧的导航窗格中选择 **"单一登录**"。

8. 选择**基于密码的登录**模式。

9. 输入**登录 URL**，这是用户输入其用户名和密码登录的页面的 URL。 *确保登录字段在提供的 URL 的页面上可见*。

10. 选择“保存”。****

    用户名和密码输入框会自动刮擦页面。 现在，您可以使用 Azure AD 使用访问面板浏览器扩展安全地将密码传输到该应用。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手动捕获应用的登录字段

要手动捕获登录字段，必须安装访问面板浏览器扩展。 此外，您的浏览器不能*以"私人*"、*隐身*或*私有*模式运行。

要安装扩展，请参阅本文[的"安装面板浏览器扩展](#install-the-access-panel-browser-extension)"部分。

要使用手动登录字段捕获为应用配置基于密码的 SSO，请按照以下步骤操作：

1. 打开[Azure 门户](https://portal.azure.com/)。 以全局管理员或共同管理员身份登录。

2. 在左侧的导航窗格中，选择 **"所有服务**"以打开 Azure AD 扩展名。

3. 在筛选器搜索框中键入**Azure 活动目录**，然后选择**Azure 活动目录**。

4. 在 Azure AD 导航窗格中选择**企业应用程序**。

5. 选择 **"所有应用程序**"以查看应用列表。

   > [!NOTE] 
   > 如果看不到所需的应用，请使用 **"所有应用程序**"列表顶部的 **"筛选器"** 控件。 将 **"显示"** 选项设置为"所有应用程序"。

6. 选择要为 SSO 配置的应用。

7. 加载应用后，在左侧的导航窗格中选择 **"单一登录**"。

8. 选择**基于密码的登录**模式。

9. 输入**登录 URL**，这是用户输入其用户名和密码登录的页面。 *确保登录字段在提供的 URL 的页面上可见*。

10. 选择 **"*&lt;配置应用程序&gt;名称*密码单一登录设置**"。

11. 选择 **"手动检测登录字段**"。

14. 选择 **"确定**"。

15. 选择“保存”。****

16. 按照说明使用访问面板。

## <a name="troubleshoot-problems"></a>疑难解答问题

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我得到一个"我们找不到任何登录字段在该 URL"错误

当自动检测登录字段失败时，您会收到此错误消息。 要解决此问题，请尝试手动登录字段检测。 请参阅本文[的应用程序部分的手动捕获登录字段](#manually-capture-sign-in-fields-for-an-app)。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到"无法保存单一登录配置"错误

更新 SSO 配置很少失败。 要解决此问题，请尝试再次保存配置。

如果继续收到错误，请打开一个支持案例。 包括["查看门户通知详细信息](#view-portal-notification-details)"中描述的信息，并将[通知详细信息发送给支持工程师，以获得本文的帮助](#send-notification-details-to-a-support-engineer-to-get-help)部分。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>无法手动检测应用的登录字段

当手动检测不起作用时，您可能会观察到以下行为：

- 手动捕获过程似乎工作正常，但捕获的字段不正确。

- 捕获过程运行时，正确的字段不会突出显示。

- 捕获过程将按预期的方式访问应用的登录页面，但没有任何反应。

- 手动捕获似乎工作，但当用户从访问面板导航到应用时，不会发生 SSO。

如果您遇到上述任何问题，可以执行以下操作：

- 请确保您*已安装并启用*了访问面板浏览器扩展的最新版本。 请参阅本文[的"安装访问面板"浏览器扩展](#install-the-access-panel-browser-extension)部分。

- 确保您的浏览器在捕获过程中未处于*隐身**、Private*或*私有*模式。 这些模式下不支持访问面板扩展。

- 确保用户在*隐身**、Private*或*私有模式下*不会尝试从访问面板登录到应用。

- 请重试手动捕获过程。 确保红色标记位于正确的字段上。

- 如果手动捕获过程似乎停止响应或登录页未响应，请重试手动捕获过程。 但这次，在完成此过程后，按 F12 键打开浏览器的开发人员控制台。 选择**控制台**选项卡.键入**窗口.location_"*&lt;配置应用&gt;时指定的登录 URL"，*** 然后按 Enter。 这将强制页面重定向，该页面将结束捕获过程并存储捕获的字段。

### <a name="contact-support"></a>联系支持人员

如果仍有问题，则使用 Microsoft 支持打开案例。 描述你尝试过什么。 包括["查看门户通知详细信息](#view-portal-notification-details)"中描述的详细信息，并将[通知详细信息发送给支持工程师，以获得本文的帮助](#send-notification-details-to-a-support-engineer-to-get-help)部分（如果适用）。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

执行以下步骤:

1. 在受支持的浏览器中打开[访问面板](https://myapps.microsoft.com)。 以*用户*身份登录到 Azure AD。

2. 在访问面板中选择**密码 SSO 应用程序**。

3. 当系统提示您安装软件时，选择"**立即安装**"。

4. 您将被引导到浏览器的下载页面。 选择**添加**扩展名。

5. 如果系统提示您，请选择"**启用**"或 **"允许**"。

6. 安装后，重新启动浏览器。

7. 登录到访问面板。 查看是否可以打开启用密码 SSO 的应用。

您也可以通过以下链接直接下载 Chrome 和 Firefox 的浏览器扩展：

-   [铬接入面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [火狐接入面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>查看门户通知详细信息

要查看任何门户通知的详细信息，请按照以下步骤操作：

1. 选择 Azure 门户右上角**的通知**图标（铃声）。

2. 选择显示*错误*状态的任何通知。 （他们有一个红色的"！

   > [!NOTE]
   > 不能选择处于 *"成功*"或"*正在"* 状态的通知。

3. “通知详细信息”**** 窗格随即打开。 阅读信息以了解问题。

5. 如果您仍需要帮助，则与支持工程师或产品组共享信息。 选择 **"复制"错误**框右侧的**复制**图标，以复制要共享的通知详细信息。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>向支持工程师发送通知详细信息以获取有关帮助

请务必与支持部门共享本节中列出的*所有*详细信息，以便它们能够快速帮助您。 要录制它，您可以截屏或选择 **"复制错误**"。

以下信息说明了每个通知项的含义，并提供了示例。

### <a name="essential-notification-items"></a>基本通知项

- **标题**：通知的描述性标题。

   示例：*应用程序代理设置*

- **说明**：操作的结果。

   示例：*输入的内部 URL 已被另一个应用程序使用。*

- **通知 ID**：通知的唯一 ID。

    示例：*客户端通知-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **客户端请求 ID**：您的浏览器提供的特定请求 ID。

    示例 *：302fd775-3329-4670-a9f3-bea37004f0bc*

- **时间戳 UTC**：发生通知时的时间戳，以 UTC 表示。

    示例： *2017-03-23T19：50：43.7583681Z*

- **内部事务 ID**：用于查找系统中错误的内部 ID。

    示例 **：71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**： 运行该操作的用户。

    示例 *：tperkinsf128.info\@*

- **租户 ID**：运行操作的用户是 其成员的租户的唯一 ID。

    示例 *：7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **用户对象 ID**：运行操作的用户的唯一 ID。

    示例 *：17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>详细的通知项

- **显示名称**：（可以为空）错误更详细的显示名称。

    示例：*应用程序代理设置*

- **状态**：通知的特定状态。

    示例：*失败*

- **对象 ID**：（可以为空）运行操作的对象 ID。

   示例 *：8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **详细信息**：操作结果的详细描述。

    示例：*内部 url<https://bing.com/>' ' 无效，因为它已在使用中。*

- **复制错误**：允许您选择 **"复制错误**文本框"右侧的**复制图标**，以复制通知详细信息以帮助提供支持。

    例子：```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
