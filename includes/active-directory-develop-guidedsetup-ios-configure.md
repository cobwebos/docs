---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 9782c6c2024c5cf490f207bb12a214c93a53b813
ms.sourcegitcommit: 807c318f5c034f8256f91c241e9d6f8f4d7de90a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951358"
---
## <a name="register-your-application"></a>注册应用程序

如接下来的两部分中所述，可以采用两种方式之一注册应用程序。

### <a name="option-1-express-mode"></a>选项 1：快速模式

现在需要在 Microsoft 应用程序注册门户中注册应用程序：

1. 通过 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)注册应用程序。
2. 输入应用程序的名称和电子邮件。
3. 确保选中“指导式设置”选项。
4. 按照说明获取应用程序 ID，并将其粘贴到代码中。

### <a name="option-2-advanced-mode"></a>选项 2：高级模式

1. 转到 [Microsoft 应用程序注册门户](https://apps.dev.microsoft.com/portal/register-app)。
2. 输入应用程序的名称。
3. 确保取消选中“指导式设置”选项。
4. 依次选择“`Add Platform`”、“`Native Application`”。
5. 选择 `Save`。
6. 返回 Xcode。 在 `ViewController.swift` 中，将以“ `let kClientID`”开头的行替换为刚注册的应用程序 ID：

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
按住 Control 键并单击 <code>Info.plist</code> 打开上下文菜单，然后单击：<code>Open As</code>> <code>Source Code</code>
</li>
<li>
在 <code>dict</code> 根节点下添加以下代码：
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
将 <i><code>[Your_Application_Id_Here]</code></i> 替换为刚注册的应用程序 ID
</li>
</ol>
