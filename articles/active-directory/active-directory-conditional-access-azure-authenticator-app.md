---
title: "适用于 Android 的 Azure 验证器 | Microsoft 文档"
description: "Microsoft Azure 身份验证器应用可用于登录以访问工作资源。 Azure 验证器应用对移动设备显示警报，以通知你有一个等待中的双因素验证请求。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 5dd6701f25c69f5e870d3add79c312f9aeec6bf4


---
# <a name="azure-authenticator-for-android"></a>适用于 Android 的 Azure 验证器
你的 IT 管理员可能会建议你使用 Microsoft Azure 验证器登录以访问工作资源。 此应用程序提供以下两个登录选项：

* 多重身份验证可让你通过两步骤验证来保护工作帐户或学校帐户。 你使用知道的某个事物（例如密码）登录，并以拥有的某个事物（此应用提供的安全密钥）进一步保护帐户。 Azure 验证器应用对移动设备显示警报，以通知你有一个等待中的双因素验证请求。 你只需在该应用中查看此请求并点击验证或取消。 或者，系统可能会提示你输入应用中显示的密码。
* 工作帐户可让你将 Android 手机或平板电脑转变成受信任的设备，并提供对公司应用程序的单一登录 (SSO)。 IT 管理员可以要求你添加一个工作帐户，以便访问公司资源。 SSO 可让你在登录一次后，自动登录到公司为你提供的所有应用程序。

## <a name="installing-the-azure-authenticator-app"></a>安装 Azure 验证器应用
可以从 Google Play 应用商店安装 Azure 验证器应用。
从 Samsung Android 设备与非 Samsung Android 设备添加工作帐户的说明稍有不同。 下面列出了两者的说明。

## <a name="adding-the-work-account-from-samsung-android-device"></a>从 Samsung Android 设备添加工作帐户
### <a name="adding-the-work-account-through-the-app-home-screen"></a>通过应用主页屏幕添加工作帐户
以下说明适用于 Samsung GS3 及更高版本的手机或 Note2 及更高版本的平板电脑。

1. 在应用程序的主页屏幕上，接受最终用户许可协议 (EULA)。
2. 在“激活帐户”屏幕上，单击右侧的上下文菜单，然后选择“工作帐户”。
3. 在“添加帐户”屏幕上，选择“工作帐户”。
4. 在“激活设备管理员”屏幕上，单击“激活”。
5. 在“隐私政策”屏幕上，选中相应的复选框并单击“确认”。
6. 在“工作区加入”屏幕上，输入组织提供的用户 ID，然后单击“加入”。
7. 若要登录 Azure 验证器应用，请输入组织帐户和密码，然后单击“登录”。
8. 下一个屏幕会显示多重身份验证 (MFA) 的相关信息，此项设置是可选的，目的是为了提高安全性。 如果你的公司或学校要求使用双因素身份验证创建工作帐户，你将看到这个屏幕。 其中提供了有关进一步验证帐户的说明。
9. “工作区加入”屏幕显示“正在加入工作区”消息。 Azure 验证器应用尝试将你的设备添加到工作区。
10. 下一个屏幕应会显示“已加入工作区”消息。

> [!NOTE]
> 你只能在设备上创建一个工作帐户。
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>从设置菜单添加工作帐户
安装 Azure 验证器应用后，你还可以从 Android 帐户管理器创建工作帐户。

1. 从“设置”菜单导航到“帐户”，然后单击“添加帐户”。
2. 根据“通过应用主页屏幕添加工作帐户”过程中的步骤 3-10 添加工作帐户。

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>从非 Samsung Android 设备添加工作帐户
### <a name="adding-the-work-account-through-the-app-home-screen"></a>通过应用主页屏幕添加工作帐户
1. 在应用程序的主页屏幕上，接受最终用户许可协议 (EULA)。
2. 在“激活帐户”屏幕上，单击右侧的上下文菜单，然后选择“工作帐户”。
3. 在“帐户”屏幕上，单击“添加帐户”。
4. 如果看到“帐户”屏幕，请单击“添加帐户”。 如果前面已创建工作帐户，你将看到“同步”屏幕显示现有的工作帐户。 只需点击主页屏幕中的上一步箭头，即可保留该工作帐户。 或者，你可以选择删除此帐户并重建新的工作帐户。在“工作区加入”屏幕上，输入组织提供的用户 ID，然后单击“加入”。
5. 若要登录 Azure 验证器应用，请输入组织帐户和密码，然后单击“登录”。
6. 下一个屏幕会显示多重身份验证 (MFA) 的相关信息，此项设置是可选的，目的是为了提高安全性。 如果你的公司或学校要求使用双因素身份验证创建工作帐户，你将看到这个屏幕。 其中提供了有关进一步验证帐户的说明。
7. 在下一个屏幕上单击“确定”。 不要更改证书名称。
   消息“正在加入工作区”。 Azure 验证器应用尝试将你的设备添加到工作区。
   下一个屏幕应会显示“已加入工作区”消息。

> [!NOTE]
> 你只能在设备上创建一个工作帐户。
> 
> 

安装 Azure 验证器应用后，你还可以从 Android 帐户管理器创建工作帐户。

1. 从“设置”菜单导航到“帐户”，然后单击“添加帐户”。
2. 根据“通过应用主页屏幕添加工作帐户”过程中的步骤 2-7 添加工作帐户。

### <a name="how-to-find-out-which-version-is-installed"></a>如何查找安装的版本
1. 你可以找出设备上安装的 Azure 验证器应用版本和相关的服务版本。
2. 在弹出菜单中单击“关于”。
3. “关于”屏幕会显示应用的服务以及设备上安装的版本。

### <a name="sending-log-files-to-report-issues"></a>发送日志文件以报告问题
1. 遵循 Microsoft 支持网站上的指导报告 Azure 验证器应用相关的事件、获取事件编号，以及针对指定的事件编号发送日志文件，如下所示：
2. 在弹出菜单中单击“日志记录”。
3. 如果有未结的 Microsoft 支持事件，请记下事件编号（后面的步骤需要用到它）。 如果尚未创建支持事件并想要获得帮助，请根据 [Microsoft 支持](https://support.microsoft.com/en-us/contactus)网站上的指导来建立新的事件。
4. 在“日志记录”屏幕上单击“立即发送”。
5. 选择想要使用的电子邮件提供程序。
6. 如果已有未结的 Microsoft 支持事件，请联系受理你的问题的支持工程师，以了解如何发送日志数据并使其与事件相关联。 支持工程师将为你提供电子邮件地址和主题行信息。 如果你尚未创建支持事件，请根据 Microsoft 支持网站上的指导来建立新的事件。
7. 使用 Microsoft 支持人员提供给你的信息编辑“收件人”行和“主题”行。
8. Azure 验证器应用会将日志文件附加到你要发送的电子邮件。 请描述你遇到的问题，更新收件人列表（可选），然后发送电子邮件。

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>删除工作帐户和退出工作区
你随时可以删除创建的工作帐户，如下所述：

**从“设置”菜单中删除工作帐户**

1. 从帐户管理器中选择“工作帐户”。
2. 在“工作帐户”屏幕上的“常规设置”中，选择“帐户设置”–“退出工作区网络”。
3. 在“工作区加入”屏幕上选择“退出”。
4. 显示“确实要退出工作区吗”消息时，单击“确定”。
5. 这可以确保从工作区中删除你的工作帐户。

> [!NOTE]
> 建议不要使用“删除帐户”选项来删除工作帐户，因为此选项在某些旧版 Android 中可能无法正常工作。
> 
> 

## <a name="uninstalling-the-app"></a>卸载应用
在 Samsung Android 设备上，必须先按如下所述删除设备管理员特权才能卸载应用： 

1. 在“设置”部分的“系统”下，选择“安全性”。
2. 在“设备管理”中单击“设备管理员”。 确保清除“Azure 验证器”旁边的复选框。

## <a name="troubleshooting"></a>故障排除
如果看到“密钥存储错误”，这可能是因为没有为锁屏界面设置 PIN。 若要解决此问题，请卸载 Azure 验证器应用，配置用于锁屏的 PIN，然后重新安装该应用。




<!--HONumber=Dec16_HO5-->


