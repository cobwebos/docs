---
title: "Azure AD v2 Windows 桌面入门 - 配置 | Microsoft Docs"
description: "Windows 桌面 .NET (XAML) 应用程序如何获取访问令牌并调用 Azure Active Directory v2 终结点保护的 API。"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c46837af57978b047242b2869243f83d372ee43e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

## <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息
在此步骤中，需要将应用程序 ID 添加到项目。

1.    打开 `App.xaml.cs`，并将包含 `ClientId` 的行替换为：

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>后续操作

[测试和验证](active-directory-mobileanddesktopapp-windowsdesktop-test.md)

