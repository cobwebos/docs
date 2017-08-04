---
title: "配置非库应用程序的密码单一登录时的遇到问题 | Microsoft Docs"
description: "了解在为 Azure AD 应用程序库中所列非库应用程序配置密码单一登录时人们面对的常见问题"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 2fc922cb591bcd9ef333fbe3d77ee3dd03005915
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>配置非库应用程序密码单一登录时遇到的问题

阅读本文有助于了解在通过非库应用程序配置**密码单一登录**时人们面对的常见问题。

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>如何捕获应用程序的登录字段

登录字段捕获只支持 HTML 启用的登录页面，**不支持非标准登录页面**，例如使用 Flash 或其他非 HTML 启用技术的页面。

有两种方法可以捕获自定义应用程序的登录字段：

-   自动登录字段捕获

-   手动登录字段捕获

如果登录页面使用**用户名和密码输入的已知 DIV ID**字段，则**自动登录字段捕获**很适合大多数 HTML 启用的登录页面。 这种方法是通过擦除页面上的 HTML 找到与一定条件匹配的 DIV ID 后保存应用程序的元数据，以便我们稍后重新使用密码。

**手动登录字段捕获**可以在应用程序**供应商未标记**登录所用输入字段的情况下使用。 也可以在**供应商呈现多个字段**且这些字段无法自动检测的情况下使用。 只要告诉我们字段在页面上的位置，Azure AD 就可以保存登录页面上所有字段的数据。

一般情况下，**如果自动登录字段捕获无效，我们总是会建议尝试手动选项。**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>如何自动捕获应用程序的登录字段

若要使用**自动登录字段捕获**配置应用程序的**基于密码的单一登录**，请遵循以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

  * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 **确保登录字段在提供的 URL 中可见**。

10. 单击“保存”按钮  。

11. 执行该操作后，系统将自动擦除该 URL 并显示用户名和密码输入框，允许使用 Azure AD 通过访问面板浏览器扩展安全地将密码传输到该应用程序。

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>如何手动捕获应用程序的登录字段

若要手动捕获登录字段，首先必须已安装访问面板浏览器扩展，并且此扩展**不在 inPrivate、incognito 和私有模式下运行**。 若要安装浏览器扩展，请遵循[如何安装访问面板浏览器扩展](#i-cannot-manually-detect-sign-in-fields-for-my-application)部分中的步骤。

若要使用**手动登录字段捕获**配置应用程序的**基于密码的单一登录**，请遵循以下步骤：

1.  打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”或“共同管理员”身份登录。

2.  在左侧主导航菜单底部单击“更多服务”，打开“Azure Active Directory 扩展”。

3.  在筛选器搜索框中键入“Azure Active Directory”，然后选择“Azure Active Directory”项。

4.  在 Azure Active Directory 的左侧导航菜单中，单击“企业应用程序”。

5.  单击“所有应用程序”，查看所有应用程序的列表。

   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。

6.  选择要配置单一登录的应用程序。

7.  在应用程序加载后，在应用程序的左侧导航菜单中单击“单一登录”。

8.  选择“基于密码的登录”模式。

9.  输入“登录 URL”。 这就是用户在其中输入用户名和密码进行登录时的 URL。 **确保登录字段在提供的 URL 中可见**。

10. 单击“保存”按钮  。

11. 执行该操作后，系统将自动擦除该 URL 并显示用户名和密码输入框，允许使用 Azure AD 通过访问面板浏览器扩展安全地将密码传输到该应用程序。 如果操作失败，可以继续到步骤 12，**将登录模式更改为使用手动登录字段捕获**。

12. 单击“配置 &lt;应用名称&gt; 密码单一登录设置”。

13. 选择“手动检测登录字段”配置选项。

14. 单击“确定” 。

15. 单击“保存” 。

16. 按照屏幕上的说明使用访问面板。

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>看到“无法找到 URL 的任一登录字段”错误

自动检测登录字段失败时会看到此错误。 要解决这一问题，请遵循[如何手动捕获应用程序的登录字段](#how-to-manually-capture-sign-in-fields-for-an-application)部分中的步骤，尝试手动检测登录字段。

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>看到“无法保存单一登录配置”错误

在少数情况下，更新单一登录配置会失败。 要解决这一问题，请尝试再次保存单一登录配置。

如果这种情况持续失败，打开支持案例，并提供[如何看到门户通知的详细信息](#i-cannot-manually-detect-sign-in-fields-for-my-application)和[如何通过向支持工程师发送通知详细信息来获取帮助](#how-to-get-help-by-sending-notification-details-to-a-support-engineer)部分中搜集的信息。

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>无法手动检测应用程序的登录字段

手动检测无效时可能看到的一些行为包括：

-   手动捕获进程看似有效，但捕获的字段不正确

-   执行捕获进程时未突出显示正确的字段

-   捕获进程如期将我带到应用程序的登录页面，但却无任何变化

-   手动捕获看似有效，但用户从访问面板导航至应用程序时 SSO 并未发生。

如果遇到上述任一问题，请检查以下内容：

-   确保最新版本的访问面板浏览器扩展**已安装**，并且遵循[如何安装访问面板浏览器扩展](#how-to-install-the-access-panel-browser-extension)部分的步骤**已启用**该扩展。

-   确保在浏览器为 **incognito、inPrivate 或私有模式**下不会尝试捕获进程。 在这些模式下访问面板扩展不受支持。

-   确保用户未在 **incognito、inPrivate 或私有模式**下试图从访问面板登录应用程序。 在这些模式下访问面板扩展不受支持。

-   再次尝试手动捕获进程，确保红色红色标记在正确的字段上。

-   如果手动捕获进程看似已挂起或登录页面没有任何变化（上述案例 3），请再次尝试手动捕获进程。 但是，这次在进程完成后，请按“F12”按钮打开浏览器的开发人员控制台。 到达控制台后，打开“控制台”，键入“window.location=’&lt;enter the sign in url you specified when configuring the app&gt;’”，然后按“Enter”。 这会使页面重定向至其他位置，以结束捕获过程并存储已捕获的字段。

如果这些方法都无效，我们可以提供帮助。 打开支持案例，说明你尝试过的操作的详细信息和在[如何看到门户通知的详细信息](#i-cannot-manually-detect-sign-in-fields-for-my-application)和[如何通过向支持工程师发送通知详细信息来获取帮助](#how-to-get-help-by-sending-notification-details-to-a-support-engineer)部分搜集的信息（如适用）。

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安装访问面板浏览器扩展

若要安装访问面板浏览器扩展，请按照以下步骤操作：

1.  在某个支持的浏览器中打开 [访问面板](https://myapps.microsoft.com) ，并在 Azure AD 中以“用户”身份登录。

2.  单击访问面板中的“密码 - SSO 应用程序”。

3.  在出现询问是否安装该软件的提示时，选择“立即安装”。

4.  根据浏览器的情况，用户将定向到下载链接。 将扩展“添加”到浏览器中。

5.  如果浏览器出现提示，选择“启用”或“允许”扩展。

6.  安装完成后，“重启”浏览器会话。

7.  登录到访问面板，并查看是否可以**启动**密码 - SSO 应用程序。

也可以通过下面的直接链接下载适用于 Chrome 和 Firefox 的扩展：

-   [Chrome 访问面板扩展](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 访问面板扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>如何查看门户通知的详细信息

可以通过遵循以下步骤来查看任何门户通知的详细信息：

1.  单击 Azure 门户右上方的“通知”图标（铃铛）

2.  选择“错误”状态的任何通知（旁边红色的 (!)）。

  >!注意] 不能单击“成功”或“正在进行中”状态下的通知。
  >
  >

3.  这将打开“通知的详细信息”边栏选项卡。

4.  使用此信息了解有关问题的详细信息。

5.  如果仍需要帮助，还可以与支持工程师或产品组共享此信息以获取问题的帮助。

6.  单击“复制错误”文本框右侧的“复制”图标，复制所有通知的详细信息以与支持工程师或产品组工程师共享。

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>如何通过向支持工程师发送通知的详细信息来获取帮助

如果需要帮助，与支持工程师共享**下面列出的所有详细信息**非常重要，这有助于他们迅速提供帮助。 可以通过**拍摄屏幕截图**或单击“复制错误”图标（“复制错误”文本框右侧），轻松实现此目的。

## <a name="notification-details-explained"></a>介绍通知的详细信息

下面介绍了每个通知项的含义，并提供了每项的示例。

### <a name="essential-notification-items"></a>基本通知项

-   **标题** - 通知的描述性标题

    -   示例 - **应用程序代理设置**

-   **说明** - 已发生情况的说明（操作的结果）

    -   示例 - **输入的内部 URL 已被其他应用程序使用**

-   **通知 ID** - 通知的唯一 ID

    -   示例 - **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **客户请求 ID** - 浏览器发出的特定请求 ID

    -   示例 - **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **时间戳 UTC** - 在通知生成期间的时间戳（采用 UTC 时制）

    -   示例 - **2017-03-23T19:50:43.7583681Z**

-   **内部事务 ID** - 可用于在系统中查找错误的内部 ID

    -   示例 - **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** - 执行操作的用户

    -   示例 - **tperkins@f128.info**

-   **租户 ID** - 执行操作的用户是其成员的租户的唯一 ID

    -   示例 - **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **用户对象 ID** - 执行操作的用户的唯一 ID

    -   示例 - **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>详细的通知项

-   **显示名称** - **（可以为空）**错误的更详细的显示名称

    -   示例 * - **应用程序代理设置**

-   **状态** - 通知的特定状态

    -   示例 * - **失败**

-   **对象 ID** - **（可以为空）**已对其执行操作的对象的 ID

    -   示例 - **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **详细信息** - 已发生情况的说明（操作的结果）

    -   示例 - **内部 URL“http://bing.com/”无效，因为它已在使用中**

-   **复制错误** - 单击“复制错误”文本框右侧的“复制”图标，复制所有通知的详细信息以与支持工程师或产品组工程师共享

    -   示例 - ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>后续步骤
[使用应用程序代理为应用提供单一登录](active-directory-application-proxy-sso-using-kcd.md)


