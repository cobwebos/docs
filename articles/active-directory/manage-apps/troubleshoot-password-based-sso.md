---
title: 排查 Azure Active Directory 中基于密码的单一登录的问题
description: 排查配置为基于密码的单一登录的 Azure AD 应用的问题。
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 0534c85548b1d8b6203aaac4911dc851dd49d81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89460348"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>排查 Azure AD 中基于密码的单一登录的问题

若要使用 "我的应用" 中的 "基于密码的单一登录 (SSO) ，必须安装浏览器扩展。 当你选择配置为基于密码的 SSO 的应用时，会自动下载此扩展。 若要了解如何从最终用户的角度使用我的应用，请参阅 [我的应用门户的帮助](../user-help/my-apps-portal-end-user-access.md)。

## <a name="my-apps-browser-extension-not-installed"></a>我的应用浏览器扩展未安装
请确保已安装浏览器扩展。 若要了解详细信息，请参阅 [计划 Azure Active Directory 应用部署](access-panel-deployment-plan.md)。 

## <a name="single-sign-on-not-configured"></a>未配置单一登录
请确保已配置基于密码的单一登录。 若要了解详细信息，请参阅 [配置基于密码的单一登录](configure-password-single-sign-on-non-gallery-applications.md)。

## <a name="users-not-assigned"></a>未分配的用户
请确保已将用户分配到应用。 若要了解详细信息，请参阅 [将用户或组分配到应用](assign-user-or-group-access-portal.md)。

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>凭据已填写，但扩展却未提交凭据

这种问题通常发生在以下情景中：应用程序供应商为了添加字段而在近期更改了他们的登录页面、改变了用于检测用户名和密码字段的标识符，或修改了登录体验作用于应用程序的方式。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题需要一些时间才能解决。 如果其中一个集成无法正确工作，请打开支持案例以尽快解决这一问题。

**** 如果与该应用程序的供应商联系，请向他们发送我们的方法，这样，Microsoft 就可以与他们合作，将其应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../azuread-dev/howto-app-gallery-listing.md)来让他们开始工作。

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>凭据已填写并提交，但页面却显示凭据不正确

若要解决此问题，请先尝试以下操作：

- 先让用户使用为他们保存的凭据尝试**直接登录应用程序网站**。

  * 如果登录工作，则让用户在 "[我的应用](https://myapps.microsoft.com/) **" 的 "应用" 部分中**单击 "**应用程序" 磁贴**上的 "**更新凭据**" 按钮，将其更新为最新的已知工作用户名和密码。

  * 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”**** 选项卡、选择分配并单击“更新凭据”**** 按钮，找到该用户或组的应用程序分配。

- 如果用户自己分配凭据，让用户“检查以确保他们的密码在应用程序中未过期”****，如果已经过期，直接登录应用程序**更新过期密码**。

  * 在应用程序中更新密码后，请求用户在 "[我的应用](https://myapps.microsoft.com/)"**的 "应用" 部分中**单击 "**应用程序" 磁贴**上的 "**更新凭据**" 按钮，以将其更新为最新的已知工作用户名和密码。

  * 如果或另一个管理员为该用户分配凭据，请导航到该应用程序的“用户和组”**** 选项卡、选择分配并单击“更新凭据”**** 按钮，找到该用户或组的应用程序分配。

- 确保在用户的浏览器中运行并启用 "我的应用" 浏览器扩展。

- 确保在 **incognito、inPrivate 或私有模式下**，用户不会尝试从我的应用程序登录到应用程序。 在这些模式下，"我的应用" 扩展不受支持。

如果前面的建议无效，可能是应用程序端已发生变更，已暂时中断了应用程序与 Azure AD 的集成。 例如，当应用程序供应商在页面上引入与手动和自动化输入表现不同的脚本，从而导致自动集成（就像我们这样的）中断时，就会发生这样情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。 

除此之外，**如果与该应用程序的供应商联系，请** **向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../azuread-dev/howto-app-gallery-listing.md)来让他们开始工作。

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>检查应用程序的登录页面近期是否已变更，或需要其他字段

如果应用程序的登录页面已彻底变更，有时这会导致我们的集成中断。 例如，当应用程序供应商向体验添加登录字段、验证码或多重身份验证时就会出现这种情况。 幸运的是，在许多情况下，Microsoft 能够与应用程序供应商合作来快速解决这些问题。

但是，尽管 Microsoft 的技术能够在应用程序集成中断时进行自动检测，但是可能无法立即找到这些问题，或者这些问题可能需要一些时间才能解决。 当集成无法正常工作时，可以打开支持案例以尽快解决这一问题。 

除此之外，**如果与该应用程序的供应商联系，请** **向他们发送我们的方法**，如此一来，我们就可以与他们合作，将他们的应用程序与 Azure Active Directory 进行本机集成。 可以将该供应商发送至[列出 Azure Active Directory 应用程序库中的应用程序](../azuread-dev/howto-app-gallery-listing.md)来让他们开始工作。

## <a name="capture-sign-in-fields-for-an-app"></a>捕获应用程序的登录字段

仅支持 HTML 的登录页支持登录字段捕获。 非标准登录页面不支持该功能，如使用 Adobe Flash 或其他未启用 HTML 的技术的页面。

可以通过两种方法来捕获自定义应用的登录字段：

- 如果用户为 "用户名" 和 "密码" 字段使用众所周知的 " *DIV id* "，**自动登录字段捕获**适用于大多数已启用 HTML 的登录页。 页面上的 HTML 擦除查找与特定条件匹配的 DIV Id。 保存元数据，以便以后可将其重播到应用中。

- 如果应用供应商*未标记登录输入字段*，则使用**手动登录字段捕获**。 如果供应商 *呈现多个无法自动检测到的字段，* 则也可以使用手动捕获。 Azure Active Directory (Azure AD) 可以将多个字段的数据存储在登录页上，如果您告诉它这些字段在页面上的位置。

通常，如果自动登录字段捕获不起作用，请尝试使用 "手动" 选项。

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>自动捕获应用程序的登录字段

若要使用自动登录字段捕获来配置基于密码的 SSO，请执行以下步骤：
1. 打开 [Azure 门户](https://portal.azure.com/)。 以 "全局管理员" 或 "共同管理员" 身份登录。
2. 在左侧的导航窗格中，选择 " **所有服务** " 打开 Azure AD 扩展。
3. 在筛选器搜索框中键入 **Azure Active Directory** ，然后选择 " **Azure Active Directory**"。
4. 在 Azure AD 导航窗格中选择 " **企业应用程序** "。
5. 选择 " **所有应用程序** " 以查看应用列表。
   > [!NOTE]
   > 如果看不到所需的应用，请使用 "**所有应用程序**" 列表顶部的 "**筛选器**" 控件。 将 " **显示** " 选项设置为 "所有应用程序"。
6. 选择要为 SSO 配置的应用。
7. 在应用程序加载后，在左侧的导航窗格中选择 " **单一登录** "。
8. 选择 **"基于密码的登录模式"** 。
9. 输入 " **登录 url**"，它是用户在其中输入用户名和密码以登录的页面的 url。 *确保登录字段在提供的 URL 的页面上可见*。
10. 选择“保存”。
    对于 "用户名" 和 "密码" 输入框，会自动擦除页面。 你现在可以使用 Azure AD 通过 "我的应用" 浏览器扩展安全地将密码传输到该应用。

### <a name="manually-capture-sign-in-fields-for-an-app"></a>手动捕获应用程序的登录字段

若要手动捕获登录字段，你必须安装 "我的应用" 浏览器扩展。 此外，浏览器不能在 *inPrivate*、 *incognito*或 *private* 模式下运行。

若要使用手动登录字段捕获配置应用程序的基于密码的 SSO，请执行以下步骤：
1. 打开 [Azure 门户](https://portal.azure.com/)。 以 "全局管理员" 或 "共同管理员" 身份登录。
2. 在左侧的导航窗格中，选择 " **所有服务** " 打开 Azure AD 扩展。
3. 在筛选器搜索框中键入 **Azure Active Directory** ，然后选择 " **Azure Active Directory**"。
4. 在 Azure AD 导航窗格中选择 " **企业应用程序** "。
5. 选择 " **所有应用程序** " 以查看应用列表。
   > [!NOTE] 
   > 如果看不到所需的应用，请使用 "**所有应用程序**" 列表顶部的 "**筛选器**" 控件。 将 " **显示** " 选项设置为 "所有应用程序"。
6. 选择要为 SSO 配置的应用。
7. 在应用程序加载后，在左侧的导航窗格中选择 " **单一登录** "。
8. 选择 **"基于密码的登录模式"** 。
9. 输入 " **登录 URL**"，用户可在其中输入用户名和密码进行登录。 *确保登录字段在提供的 URL 的页面上可见*。
10. 选择 "**配置* &lt; &gt; Appname*密码单一登录设置"**。
11. 选择 **"手动检测登录字段"**。
14. 选择“确定”  。
15. 选择“保存”。 
16. 按照说明使用我的应用。


## <a name="troubleshoot-problems"></a>排查问题

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>我收到 "找不到任何登录字段的 URL" 错误

当自动检测登录字段失败时，会收到此错误消息。 若要解决此问题，请尝试手动登录字段检测。 请参阅本文的 [手动捕获应用程序的登录字段](#manually-capture-sign-in-fields-for-an-app) 部分。

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>我收到 "无法保存单一登录配置" 错误

很少，更新 SSO 配置将失败。 若要解决此问题，请尝试再次保存配置。

如果仍然遇到此错误，请打开支持案例。 请包括 " [查看门户通知详细信息](#view-portal-notification-details) " 和 "向 [支持工程师发送通知详细](#send-notification-details-to-a-support-engineer-to-get-help) 信息" 中所述的信息，以获取本文的帮助部分。

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>我无法手动检测应用程序的登录字段

手动检测不起作用时，可能会观察到以下行为：
- 手动捕获进程似乎正在运行，但捕获的字段不正确。
- 当捕获进程运行时，正确的字段不突出显示。
- 捕获过程会将您转到应用程序的登录页，但没有任何反应。
- 手动捕获看起来有效，但当用户从 "我的应用" 导航到应用时，不会发生 SSO。

如果遇到这些问题，请执行以下操作：
- 请确保已 *安装并启用*"我的应用程序" 浏览器扩展的最新版本。
- 在捕获过程中，请确保你的浏览器不处于 *incognito*、 *inPrivate*或 *私有* 模式。 在这些模式下，"我的应用" 扩展不受支持。
- 确保用户在 *incognito*、 *inPrivate*或 *私有模式下*没有尝试从我的应用程序登录应用。
- 再次尝试手动捕获进程。 请确保红色标记在正确的字段上。
- 如果手动捕获进程似乎停止响应或登录页面未响应，请再次尝试手动捕获进程。 但这一次，在完成此过程后，按 F12 键即可打开浏览器的开发人员控制台。 选择 "**控制台**" 选项卡。键入**window。 location = "* &lt; 配置应用 &gt; 时指定的登录 URL*"**，然后按 enter。 这会强制执行页面重定向，以结束捕获过程并存储已捕获的字段。

## <a name="request-support"></a>请求支持 
如果在设置 SSO 并分配用户时收到错误消息，请打开支持票证。 尽可能多地包含以下信息：

-   相关错误 ID
-   UPN（用户电子邮件地址）
-   TenantID
-   浏览器类型
-   发生错误时的时区和时间/时间范围
-   Fiddler 跟踪

### <a name="view-portal-notification-details"></a>查看门户通知的详细信息

若要查看任何门户通知的详细信息，请执行以下步骤：
1. 选择 " **通知** " 图标 (Azure 门户右上角的电铃) 。
2. 选择显示 *错误* 状态的任何通知。  (它们具有红色的 "！"。) 
   > [!NOTE]
   > 你无法选择处于 " *成功* " 或 " *正在进行* " 状态的通知。
3. “通知详细信息”**** 窗格随即打开。 阅读有关此问题的信息。
5. 如果仍需要帮助，请与支持工程师或产品组共享此信息。 选择 "**复制错误**" 框右侧的 "**复制**" 图标，将通知详细信息复制到 "共享"。

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>将通知详细信息发送给支持工程师以获取帮助

请务必共享此部分中列出的 *所有* 详细信息并提供支持，以便他们能够快速帮助你。 要记录它，可以拍摄屏幕快照或选择 " **复制错误**"。

以下信息说明每个通知项的意思，并提供示例。

#### <a name="essential-notification-items"></a>基本通知项目

- **标题**：通知的描述性标题。

   示例： *应用程序代理设置*

- **说明**：操作导致的结果。

   示例： *输入的内部 URL 已被其他应用程序使用。*

- **通知 ID**：通知的唯一 ID。

    示例： *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **客户端请求 id**：浏览器发出的特定请求 id。

    示例： *302fd775-3329-4670-a9f3-bea37004f0bc*

- **时间戳 utc**：通知出现时间的时间戳（utc）。

    示例： *2017-03-23T19：50： 43.7583681 z*

- **内部事务 ID**：用于在我们的系统中查找错误的内部 ID。

    示例： **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**：运行操作的用户。

    示例： *tperkins \@ f128.info*

- **租户 ID**：运行操作的用户所属的租户的唯一 ID。

    示例： *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **用户对象 id**：运行操作的用户的唯一 id。

    示例： *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>详细通知项

- **显示名称**： (可以为空) 错误的详细显示名称。

    示例： *应用程序代理设置*

- **状态**：通知的特定状态。

    示例： *Failed*

- **对象 id**：对于运行操作的对象 id)  (可以为空。

   示例： *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **详细信息**：操作导致的操作的详细说明。

    示例： *内部 url " <https://bing.com/> " 无效，因为它已在使用中。*

- **复制错误**：使你能够选择 "**复制错误**" 文本框右侧的 "**复制" 图标**，复制通知详细信息以帮助提供支持。

    实例   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>后续步骤
* [应用程序管理的快速入门系列](view-applications-portal.md)
* [计划“我的应用”部署](access-panel-deployment-plan.md)
