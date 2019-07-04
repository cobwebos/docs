---
title: 配置非库应用程序的密码 SSO 时遇到问题 |Microsoft Docs
description: 配置密码单一登录 (SSO) 时出现的不是 Azure AD 应用程序库中的自定义应用的常见问题。
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
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440357"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>配置密码单一登录的非库应用程序时遇到问题

本指南介绍了可以配置时出现的常见问题*密码单一登录*(SSO) 的非库应用程序。

## <a name="capture-sign-in-fields-for-an-app"></a>捕获登录字段的应用

登录字段捕获只支持 HTML 启用的登录页面。 它具有不支持非标准登录页面，例如那些使用 Adobe Flash 或其他非 HTML 启用技术。

有两种方法来捕获登录字段的自定义应用：

- **自动登录字段捕获**适用于大多数 HTML 启用登录页中，*如果他们使用的已知 DIV Id*的用户名和密码字段。 擦除页面上的 HTML 以查找与特定条件匹配的 DIV Id。 该元数据将保存，以便它可以稍后重播到应用。

- **手动登录字段捕获**如果使用的应用程序供应商*不会标记在登录输入的字段*。 如果还使用手动捕获供应商*呈现不能为自动检测到的多个字段*。 Azure Active Directory (Azure AD) 可以存储在登录页上，如果还有告诉它这些字段所在的页上的所有字段的数据。

一般情况下，如果自动登录字段捕获不起作用，请尝试手动选项。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自动捕获登录字段的应用

若要通过使用自动登录字段捕获配置基于密码的 SSO，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 登录以全局管理员或共同管理员。

2. 在左侧导航窗格中，选择**所有服务**以打开 Azure AD 扩展。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE]
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**的所有应用程序**列表。 设置**显示**选项为"所有应用程序。"

6. 选择你想要为 SSO 配置的应用。

7. 应用程序加载后，选择**单一登录**在左侧导航窗格中。

8. 选择**基于密码的单一登录**模式。

9. 输入**单一登录 URL**，这是用户在其中输入其用户名和密码即可登录的页的 URL。 *请确保登录字段在提供的 URL 的页上可见*。

10. 选择“保存”。 

    用户名称和密码输入框会自动擦除页面。 现在可以使用 Azure AD 安全地将密码传输到该应用通过使用访问面板浏览器扩展。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手动捕获登录字段的应用

若要手动捕获登录字段，你必须安装访问面板浏览器扩展。 此外，不能运行你的浏览器*inPrivate*， *incognito*，或*专用*模式。

若要安装扩展，请参阅[安装访问面板浏览器扩展](#install-the-access-panel-browser-extension)本文的部分。

若要通过使用手动登录字段捕获配置基于密码的 SSO 的应用，请按照下列步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)。 登录以全局管理员或共同管理员。

2. 在左侧导航窗格中，选择**所有服务**以打开 Azure AD 扩展。

3. 类型**Azure Active Directory**在筛选器搜索框，然后选择**Azure Active Directory**。

4. 选择**企业应用程序**在 Azure AD 的导航窗格中。

5. 选择**所有应用程序**若要查看您的应用程序的列表。

   > [!NOTE] 
   > 如果未看到所需的应用，使用**筛选器**顶部的控件**的所有应用程序**列表。 设置**显示**选项为"所有应用程序。"

6. 选择你想要为 SSO 配置的应用。

7. 应用程序加载后，选择**单一登录**在左侧导航窗格中。

8. 选择**基于密码的单一登录**模式。

9. 输入**单一登录 URL**，这是用户在其中输入其用户名和密码即可登录的页。 *请确保登录字段在提供的 URL 的页上可见*。

10. 选择**配置 *&lt;appname&gt;* 密码单一登录设置**。

11. 选择**手动检测登录字段**。

14. 选择**确定**。

15. 选择“保存”。 

16. 按照说明使用访问面板。

## <a name="troubleshoot-problems"></a>对问题进行故障排除

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到"找不到 url 的任何登录字段"错误

自动检测登录字段失败时收到此错误消息。 若要解决此问题，请尝试手动登录字段检测。 请参阅[手动捕获登录字段的应用程序](#manually-capture-sign-in-fields-for-an-app)本文的部分。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>获取"无法保存单一登录配置"错误

很少，将更新 SSO 配置失败。 若要解决此问题，请尝试再次保存配置。

如果不断收到此错误，请打开支持案例。 包括中所述的信息[查看门户通知的详细信息](#view-portal-notification-details)并[向获取帮助和支持工程师发送通知详细信息](#send-notification-details-to-a-support-engineer-to-get-help)本文的部分。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我的应用程序无法手动检测登录字段

手动检测不能正常工作时，你可能会发现以下行为：

- 手动捕获进程看似有效，但捕获的字段不正确。

- 当捕获进程在运行时未突出显示了正确的字段。

- 捕获进程将您带到应用程序的登录页按预期运行，但没有任何反应。

- 手动捕获看似有效，但用户导航到应用程序从访问面板时 SSO 并未发生。

如果遇到这些问题，请执行以下操作：

- 请确保具有最新版本的访问面板浏览器扩展*安装并启用*。 请参阅[安装访问面板浏览器扩展](#install-the-access-panel-browser-extension)本文的部分。

- 请确保你的浏览器不在*incognito*， *inPrivate*，或*专用*在捕获过程中的模式。 访问面板扩展不支持在这些模式。

- 请确保你的用户并不会尝试登录到应用程序从访问面板时在*incognito*， *inPrivate*，或*专用模式*。

- 请再次尝试手动捕获进程。 请确保红色红色标记在正确的域的上方。

- 如果手动捕获进程看似已停止响应或登录页不会做出响应，请再次尝试手动捕获进程。 但这一次，完成过程后，按 F12 键以打开浏览器的开发人员控制台。 选择**控制台**选项卡。类型**window.location =" *&lt;配置应用程序时指定的登录 URL&gt;* "** ，然后按 Enter。 这会强制页面重定向，结束捕获过程并将存储已捕获的字段。

### <a name="contact-support"></a>联系支持人员

如果问题仍然存在，请与 Microsoft 支持部门打开一种情况。 描述您的尝试。 包括详细信息中所述[查看门户通知的详细信息](#view-portal-notification-details)和[向获取帮助和支持工程师发送通知详细信息](#send-notification-details-to-a-support-engineer-to-get-help)本文 （如果适用） 的部分。

## <a name="install-the-access-panel-browser-extension"></a>安装访问面板浏览器扩展

执行以下步骤:

1. 打开[访问面板](https://myapps.microsoft.com)中支持的浏览器。 登录到 Azure AD*用户*。

2. 选择**密码-SSO 应用程序**访问面板中。

3. 当系统提示安装软件时，选择**立即安装**。

4. 你将定向到下载页面的浏览器。 选择**添加**扩展。

5. 如果系统提示，请选择**启用**或**允许**。

6. 安装完成后，重新启动你的浏览器。

7. 登录到访问面板。 请参阅是否可以打开密码 SSO 已启用应用。

通过这些链接，可以直接于 Chrome 和 Firefox 下载浏览器扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>查看门户通知详细信息

若要查看任何门户通知的详细信息，请执行以下步骤：

1. 选择**通知**在 Azure 门户的右上角的图标 （铃铛）。

2. 选择任何条通知，指出*错误*状态。 (它们具有红色"！"。)

   > [!NOTE]
   > 不能选择中的通知*成功*或*正在进行中*状态。

3. “通知详细信息”  窗格随即打开。 阅读以了解有关该问题的信息。

5. 如果仍需帮助，请与支持工程师或产品组共享信息。 选择**副本**右侧的图标**复制错误**复制通知详细信息，以共享。

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>向以获取帮助的支持工程师发送通知详细信息

务必你共享*所有*节列出了此支持，以便它们可以帮助您快速的详细信息。 若要记录它，可以拍摄屏幕快照，或选择**复制错误**。

以下信息说明了每个通知项意味着并提供示例。

### <a name="essential-notification-items"></a>基本通知项

- **标题**： 通知的描述性标题。

   示例：*应用程序代理设置*

- **说明**： 由于操作而发生了什么。

   示例：*输入的内部 URL 已被其他应用程序使用。*

- **通知 ID**： 通知的唯一 ID。

    示例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **客户端请求 ID**： 你的浏览器所做的特定请求 ID。

    示例：*302fd775-3329-4670-a9f3-bea37004f0bc*

- **时间戳 UTC**： 通知发生时，在 UTC 中的时间戳。

    示例：*2017-03-23T19:50:43.7583681Z*

- **内部事务 ID**： 用于在系统中查找错误的内部 ID。

    示例：**71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**:运行该操作的用户。

    示例： *tperkins\@f128.info*

- **租户 ID**： 运行该操作的用户的成员的租户的唯一 ID。

    示例：*7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **用户对象 ID**:运行该操作的用户的唯一 ID。

    示例：*17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>详细的通知项

- **显示名称**: （可以为空） 错误的更详细的显示名称。

    示例：*应用程序代理设置*

- **状态**： 通知的特定状态。

    示例：失败 

- **对象 ID**: （可以是空的） 对其运行操作的对象 ID。

   示例：*8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **详细信息**： 由于操作而发生了什么的详细的说明。

    示例：*内部 url<https://bing.com/>无效，因为它已在使用。*

- **复制错误**:使您能够选择**复制图标**右侧**复制错误**文本框中，复制通知详细信息，以帮助进行支持。

    示例：   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](application-proxy-configure-single-sign-on-with-kcd.md)
