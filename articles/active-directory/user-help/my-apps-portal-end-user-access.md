---
title: 访问和使用 "我的应用" 门户上的应用-Azure Active Directory |Microsoft Docs
description: 了解如何访问 "我的应用" 门户。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 901067e198616dba390e0e1162ec26a0b11768f1
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "68383136"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>访问和使用 "我的应用" 门户上的应用

可以在基于 Web 的“我的应用”门户中使用工作或学校帐户，以便查看并启动组织的许多基于云的应用、更新你的部分配置文件和帐户信息、查看**组**信息，以及为应用和组执行“访问评审”。 如果无法访问“我的应用”门户，则必须联系支持人员以获取相关权限。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在[应用程序管理文档](https://docs.microsoft.com/azure/active-directory/manage-apps)中查找有关如何设置和管理基于云的应用的详细信息。

## <a name="supported-browsers"></a>支持的浏览器

可以从以下任何 web 浏览器转到 "**我的应用**" 门户：

- Google Chrome

- Mozilla Firefox，版本26.0 或更高版本

- Microsoft Edge

- Internet Explorer 第11版（有限支持）

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>下载并安装 "我的应用" 安全登录扩展

如果系统提示，请下载并安装 "我的应用" 安全登录扩展。 此扩展可帮助你启动组织的任何需要你使用单一登录过程的云应用。 如果你的组织已为你设置了单一登录，则会自动安装该扩展，并且你可以跳过此部分。

此扩展可帮助你：

- 直接从登录页登录到应用。

- 使用 "**快速搜索**" 功能启动任何应用。

- 请参阅**最近使用**的部分中最近使用过的应用。

- 远程使用[应用程序代理](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)时使用内部公司 url。

### <a name="to-download-and-install-the-extension"></a>下载并安装扩展

基于你使用的浏览器下载并安装扩展：

- **Google Chrome。** 从 Chrome Web 应用商店中转到 "[我的应用安全登录扩展](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)功能"，然后选择 "**添加到 Chrome**"。

- **Mozilla Firefox**在 " **Firefox 外接程序**" 页上，中转到 "[我的应用安全登录扩展](https://addons.mozilla.org/firefox/addon/access-panel-extension/)功能"，然后选择 "**添加到 Firefox**"。

- **Microsoft Edge**在 Microsoft Store 中，转到 "[我的应用安全登录扩展](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)功能"，然后选择 "**获取**"。

将在**地址**栏的右侧添加一个图标，让用户登录并自定义扩展。

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>使用扩展更改 "我的应用" 门户
你可以选择要在 "**最近使用**" 部分中查看的应用数，并决定是否允许你的组织的内部 url 重定向。

1. 选择 "新的我的应用" "**安全登录扩展**" 图标 ![Extension 在**地址**栏右侧 ](media/my-apps-portal/my-apps-portal-extension-icon.png)，然后选择 "**登录以开始**"。

2. 右键单击 "**设置**" 图标 ![Settings "图标 ](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)，然后选择"**设置**"。

3. 在 "**设置**" 框中，选择你想要在门户上看到的最近应用的数量，以及是否允许你的组织的内部 url 重定向以便可以远程使用它们。

    ![扩展的 "设置" 页，其中显示了可用的自定义](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>通过设备访问和使用 "我的应用" 门户
你可以从 Intune 管理的浏览器中访问和使用 "我的应用" 门户，也可以从 iOS 或 Android 移动设备访问和。

!["我的应用" 门户中的 "应用" 页](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>在你的计算机上访问和使用 "我的应用" 门户
如果你有权访问并使用组织的基于云的应用，可以通过 "**我的应用**" 门户获取这些应用。

1. 登录到工作或学校帐户。

2. 打开 web 浏览器并中转到 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

3. 从 "**应用**" 页中，选择要开始使用的应用。

    此时会打开应用程序的新页面，可以在其中登录（如有必要）或开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>在 Intune 托管的浏览器中访问和使用 "我的应用" 门户

通过 iOS 和 Android 设备上的 Intune Managed Browser 查看和使用组织的应用。

1. 在移动设备上，从[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)和[Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)下载并安装 Intune Managed Browser 应用。

2. 打开 Intune Managed Browser 应用，中转到 https://myapps.microsoft.com ，或者使用你的组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

3. 从 "**应用**" 页中，选择要开始使用的应用。

    此时会打开应用程序的新页面，可以在其中登录（如有必要）或开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>在 iOS 设备上访问和使用 "我的应用" 门户

查看并使用 iPhone 或 iPad 设备上的 **"我的应用**" 门户，运行 iOS 版本7或更高版本。 你还可以安装 "[我的应用" 移动应用](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)，以便在 iOS 设备上访问你的组织的应用。 

1. 在移动设备上，启动 web 浏览器应用，如 Safari。

2. 请参阅 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

3. 从 "**应用**" 页中，选择要开始使用的应用。

    此时会打开应用程序的新页面，可以在其中登录（如有必要）或开始使用应用。

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>在 Android 设备上访问和使用 "我的应用" 门户

查看并使用 Android 设备上的 "**我的应用**" 门户。

1. 在移动设备上，启动 web 浏览器应用，如 Google Chrome。

2. 请参阅 https://myapps.microsoft.com ，或使用组织提供的链接。 例如，你可能会被定向到组织的自定义页面，如 https://myapps.microsoft.com/contoso.com 。

    此时将显示 "**应用**" 页，其中显示组织所拥有的所有基于云的应用程序，并可供你使用。

3. 从 "**应用**" 页中，选择要开始使用的应用。

    此时会打开应用程序的新页面，可以在其中登录（如有必要）或开始使用应用。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>将新应用添加到 "我的应用" 门户

如果管理员已授予你权限，则可以将新应用添加到 "**应用**" 页。

1. 从 "**应用**" 页中，选择 "**添加应用**"。

    此时将显示 "**添加应用**" 页。

    !["我的应用" 门户中的 "添加应用" 页](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. 从提供的列表中选择要添加的应用，然后选择 "**添加**"。

3. 应用将添加到 "**应用**" 页上的列表中。

    某些应用可能需要在添加之前获得管理员批准。 在这种情况下，该应用程序不会添加到 "**应用**" 页中，直到管理员批准。

## <a name="next-steps"></a>后续步骤

转到 "**应用**" 页后，可以：

- [更改你的个人资料信息](my-apps-portal-end-user-update-profile.md)

- [查看和更新组相关信息](my-apps-portal-end-user-groups.md)

- [执行自己的访问评审](my-apps-portal-end-user-access-reviews.md)
