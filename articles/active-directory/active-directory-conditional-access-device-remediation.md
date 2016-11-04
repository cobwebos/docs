---
title: 故障排除：无法从此处进行访问 | Microsoft Docs
description: 本主题可帮助你确定可以遵循的修复步骤，以便获取应用程序的访问权限。
services: active-directory
keywords: 基于设备的条件访问, 设备注册, 启用设备注册, 设备注册和 MDM
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: markvi

---
# 故障排除：无法从此处进行访问
访问应用程序时（如 SharePoint Online）遇到访问被拒绝页面。现在，该怎么办？

本指南可帮助你确定可以遵循的修复步骤，以便获取应用程序的访问权限。

你的设备在哪种设备平台上运行？ 此问题的答案可确定本主题中适合你的部分：

* Windows 设备
* iOS 设备（iPhone 或 iPad）
* Android 设备

## 从 Windows 设备访问
如果设备运行 Windows 10、Windows 8.1、Windows 8.0、Windows 7、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2，通过识别尝试访问应用程序时遇到的页面，选择合适的原因。

### 未注册设备
如果未在 Azure Active Directory (Azure AD) 中注册设备，并且应用程序使用基于设备的策略进行保护，可能会看到具有以下内容的页面：

![针对未注册的设备的“无法从此处访问”消息](./media/active-directory-conditional-access-device-remediation/01.png "方案")

如果设备已通过域加入组织的 Active Directory，可以尝试以下操作：

1. 请确保已使用工作帐户（Active Directory 帐户）登录到 Windows。
2. 通过 VPN 或 DirectAccess 连接到公司网络。
3. 建立连接后，使用 Windows 键 + L 键来锁定 Windows 会话。
4. 输入工作帐户凭据解锁 Windows 会话。
5. 等待一分钟，然后尝试再次访问应用程序。
6. 如果遇到相同的页面，请与管理员联系，并单击“更多详细信息”链接，提供详细信息。

如果设备未加入域，并且运行 Windows 10，则有两个选项：

* 运行 Azure AD Join。
* 将工作帐户或学校帐户添加到 Windows。

有关两者之间的差异的信息，请参阅[在工作区中使用 Windows 10 设备](active-directory-azureadjoin-windows10-devices.md)。

若要运行 Azure AD Join，请执行以下操作（不可用于 Windows Phone）：

**Windows 10 周年更新**

1. 打开“设置”应用程序。
2. 单击“帐户”>“访问工作或学校”。
3. 单击“连接”。
4. 单击页面底部的“将设备加入到 Azure AD”。
5. 执行组织的身份验证，如有必要，提供多重身份验证凭据，然后执行步骤，直至完成为止。
6. 注销，再使用工作帐户登录。
7. 尝试再次访问应用程序。

**Windows 10 2015 年 11 月更新**

1. 打开“设置”应用程序。
2. 单击“系统”>“关于”。
3. 单击“加入 Azure AD”。
4. 执行组织的身份验证，如有必要，提供多重身份验证凭据，然后执行步骤，直至完成为止。
5. 注销，再使用工作帐户（Azure AD 帐户）登录。
6. 尝试再次访问应用程序。

若要添加工作或学校帐户，请执行以下步骤：

**Windows 10 周年更新**

1. 打开“设置”应用程序。
2. 单击“帐户”>“访问工作或学校”。
3. 单击“连接”。
4. 执行组织的身份验证，如有必要，提供多重身份验证凭据，然后执行步骤，直至完成为止。
5. 尝试再次访问应用程序。

**Windows 10 2015 年 11 月更新**

1. 打开“设置”应用程序。
2. 单击“帐户”>“你的帐户”。
3. 单击“工作或学校帐户”。
4. 执行组织的身份验证，如有必要，提供多重身份验证凭据，然后执行步骤，直至完成为止。
5. 尝试再次访问应用程序。

如果设备未加入域，并且运行 Windows 8.1，则可以通过执行以下操作执行工作区加入，并在 Microsoft Intune 中注册：

1. 打开“PC 设置”。
2. 单击“网络”>“工作区”。
3. 单击“加入”。
4. 执行组织的身份验证，如有必要，提供多重身份验证凭据，然后执行步骤，直至完成为止。
5. 单击“开启”。
6. 请等待直至完成为止。
7. 尝试再次访问应用程序。

## 不支持的浏览器
如果从以下浏览器访问应用程序，将看到类似于先前所示的页面的页面：

* Windows 10 或 Windows Server 2016 中不是 Microsoft Edge 或 Microsoft Internet Explorer 的 Chrome、Firefox 或任何其他浏览器。
* Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 中的 Firefox。

![针对不支持的浏览器的“无法从此处访问”消息](./media/active-directory-conditional-access-device-remediation/02.png "方案")

唯一的修复方法是在设备平台上使用应用程序支持的浏览器。

## 从 iOS 设备访问
有关 iPhone 或 iPad 的说明，请稍后再查阅。

## 从 Android 设备访问
有关 Android 手机或平板电脑的说明，请稍后再查阅。

## 后续步骤
[Azure Active Directory 条件访问](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0921_2016-->