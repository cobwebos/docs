---
title: 在“我的应用”门户上查找和使用应用 - Azure AD
description: 了解如何查找“我的应用”门户以及如何访问组织基于云的应用。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: aa9813480425d179bdd11bac6f6f944f9f65e3ab
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741970"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>从“我的应用”门户登录并启动应用

可以结合使用工作和学校帐户与基于 Web 的“我的应用”门户，以便：

- 查看并启用组织的多个基于云的应用
- 更新配置文件和帐户信息
- 查看“组”信息
- 为应用和组执行“访问评审”

如果无法访问“我的应用”门户，请联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> 此内容适用于“我的应用”用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="supported-browsers"></a>支持的浏览器

可以从以下任意 Web 浏览器访问“我的应用”门户：

- Google Chrome

- Mozilla Firefox，版本 26.0 或更高版本

- Microsoft Edge

- Internet Explorer，版本 11（支持受限）

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>下载并安装我的应用安全登录扩展

如出现提示，请下载并安装我的应用安全登录扩展。 此扩展可帮助你启动组织中任何需要你使用单一登录过程的云应用。 如果组织已将你设置为单一登录，则会自动安装该扩展，可以跳过此部分。

此扩展可帮助你：

- 从登录页面直接登录到应用。

- 使用“快速搜索”功能启动任何应用。

- 请在“最近使用的项目”部分中查看你最后使用的应用。

- 使用内部公司 URL，同时通过[应用程序代理](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)进行远程操作。

### <a name="to-download-and-install-the-extension"></a>下载并安装扩展

根据所使用的浏览器下载并安装扩展：

- **Google Chrome。** 在 Chrome Web Store 中，转到[我的应用安全登录扩展](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)功能，然后选择“添加到 Chrome”。

- **Mozilla Firefox**在“Firefox 加载项”页中，转到[我的应用安全登录扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)功能，然后选择“添加到 Firefox” 。

- **Microsoft Edge**在 Microsoft Store 中，转到[我的应用安全登录扩展](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)功能，然后选择“获取”以获取 Microsoft Edge 旧版浏览器的扩展。  
如果使用的是新版 Microsoft Edge 浏览器（基于 Chromium 的 Edge），则可以从 [Microsoft Edge 加载项 Store](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick) 中获取扩展。

将在地址栏的右侧添加一个图标，使你可以登录并自定义扩展。

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>使用扩展更改“我的应用”门户

可以在“最近使用过的项目”部分中选择要查看的应用数量，并决定是否允许组织的内部 URL 进行重定向。

1. 选择地址栏右侧的新“我的应用安全登录扩展”图标![扩展图标](media/my-apps-portal/my-apps-portal-extension-icon.png)，然后选择“登录”以开始使用  。

2. 右键单击“设置”图标![设置图标](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)，然后选择“设置” 。

3. 在“设置”框中，选择要在门户上查看的最新应用的数量，以及是否允许组织的内部 URL 进行重定向，以便可以远程使用它们。

    ![扩展的“设置”页，其中显示了可用的自定义](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>通过设备访问和使用“我的应用”门户

可以从 Intune 托管的浏览器或从 iOS 或 Android 移动设备访问和使用“我的应用”门户。

![“我的应用”门户中的“应用”页](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>在计算机上访问和使用“我的应用”门户

如果有权访问和使用组织基于云的应用，则可以通过“我的应用”门户进入它们。

1. 在计算机上登录到工作或学校帐户。

2. 在受支持的 Web 浏览器中，打开并转到 https://myapps.microsoft.com ，或者使用组织提供的链接（如果其将你定向到自定义页，如 `https://myapps.microsoft.com/contoso.com` ）。

    将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

3. 从“应用”页中，选择要开始使用的应用。

    将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>在 Intune 托管的浏览器上访问和使用“我的应用”门户

通过 iOS 和 Android 设备上的 Intune Managed Browser 查看和使用组织的应用。

1. 在移动设备上，从 Apple App Store 和 Google Play 商店下载并安装 Intune Managed Browser 应用。

2. 打开 Intune Managed Browser 应用，转到 https://myapps.microsoft.com ，或者使用组织提供的链接（如果其将你定向到自定义页，如 https://myapps.microsoft.com/contoso.com ）。

    将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

3. 从“应用”页中，选择要开始使用的应用。

    将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>在 iOS 设备上访问和使用“我的应用”门户

从运行 iOS 7 或更高版本的 iPhone 或 iPad 设备查看并使用“我的应用”门户。 还可以安装[“我的应用”移动应用](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)，以在 iOS 设备上访问组织的应用。

1. 在移动设备上，启动 Web 浏览器应用，如 Safari。

2. 转到 https://myapps.microsoft.com ，或者使用组织提供的链接（如果其将你定向到自定义页，如 https://myapps.microsoft.com/contoso.com ）。

    将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

3. 从“应用”页中，选择要开始使用的应用。

    将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>在 Android 设备上访问和使用“我的应用”门户

在 Android 设备上查看和使用“我的应用”门户。

1. 在移动设备上，启动 Web 浏览器应用，如 Google Chrome。

2. 转到 https://myapps.microsoft.com ，或者使用组织提供的链接（如果其将你定向到自定义页，如 https://myapps.microsoft.com/contoso.com ）。

    将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

3. 从“应用”页中，选择要开始使用的应用。

    将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>将新应用添加到“我的应用”门户

如果管理员已授予你权限，则可以将新应用添加到“应用”页。

1. 在“应用”页上，执行以下操作之一：
    - 如果你处于原始“我的应用”体验中，请选择“添加应用”，如下所示。

      ![“我的应用”门户中的“添加应用”页](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - 如果你处于已更新的“我的应用”体验中，请选择“添加自助服务应用”。

       ![myapplications.microsoft.com 上“我的应用”门户中的“添加应用”页](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. 从提供的列表中选择要添加的应用，然后选择“添加”。

3. 该应用将添加到“应用”页上的列表中。

    某些应用可能需要获得管理员批准才能进行添加。 如果出现这种情况，则在管理员批准之前，应用不会添加到“应用”页。

## <a name="start-a-cloud-based-app"></a>启动基于云的应用

可以从“我的应用”门户中启动任何可用的基于云的应用。 只能看到有权使用的应用。

- 从“应用”页中，选择要开始使用的应用。

    将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

## <a name="activities-in-the-my-apps-portal"></a>“我的应用”门户中的活动

进入“我的应用”门户后，可以进行以下操作：

- 将应用整理到组织创建和提供的各种类别中。 有关详细信息，请参阅[在“我的应用”门户中访问和使用集合](my-applications-portal-workspaces.md)。

- 查看、更新或撤销授予应用程序的权限。 有关详细信息，请参阅[在“我的应用”门户中编辑或撤销应用程序权限](my-applications-portal-permissions-saved-accounts.md)。

>[!Note]
>如果看不到任何可用的集合或类别，则表示管理员尚未设置或共享任何集合。 若要查看共享的集合，请联系组织的支持人员以获得其他帮助或权限。

## <a name="next-steps"></a>后续步骤

进入“应用”页后，可以进行以下操作：

- [更改个人资料信息](my-apps-portal-end-user-update-profile.md)

- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)

- [执行自己的访问评审](my-apps-portal-end-user-access-reviews.md)
