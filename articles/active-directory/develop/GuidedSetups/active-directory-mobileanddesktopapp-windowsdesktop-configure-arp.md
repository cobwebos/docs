---
title: Azure AD v2 Windows 桌面入门 - 配置 | Microsoft Docs
description: Windows 桌面 .NET (XAML) 应用程序如何获取访问令牌并调用 Azure Active Directory v2 终结点保护的 API。
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: c42f40252733f0c4fd7fdbabb49714ea537acc70
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919043"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息
在此步骤中，需要将应用程序 ID 添加到项目。

1.  打开 `App.xaml.cs`，并将包含 `ClientId` 的行替换为：

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>后续操作

[!INCLUDE [Test and Validate](..\..\..\..\includes\active-directory-develop-guidedsetup-windesktop-test.md)]
