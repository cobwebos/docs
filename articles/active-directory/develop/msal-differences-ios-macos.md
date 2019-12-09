---
title: MSAL for iOS & macOS |Microsoft
titleSuffix: Microsoft identity platform
description: 介绍 iOS 与 macOS 之间的 Microsoft 身份验证库（MSAL）使用差异。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d1a985d1dfbae9ad0dbaa252512c868dcb199eb
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916837"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>适用于 iOS 的 Microsoft 身份验证库和适用于 macOS 的 Microsoft 身份验证库的差异

本文介绍适用于 iOS 和 macOS 的 Microsoft 身份验证库（MSAL）之间的功能差异。

> [!NOTE]
> 在 Mac 上，MSAL 仅支持 macOS 应用。

## <a name="general-differences"></a>一般差异

MSAL for macOS 是适用于 iOS 的功能的子集。

MSAL for macOS 不支持：

- 不同的浏览器类型，如 `ASWebAuthenticationSession`、`SFAuthenticationSession`和 `SFSafariViewController`。
- macOS 不支持通过 Microsoft Authenticator 应用进行中转身份验证。

在 macOS 10.14 及更早版本中，与同一发布者的应用之间的密钥链共享更受限制。 使用[访问控制列表](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc)指定应该共享密钥链的应用的路径。 用户可能会看到其他密钥链提示。

在 macOS 10.15 + 上，MSAL 的行为在 iOS 和 macOS 之间是相同的。 MSAL 使用[密钥链访问组](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)进行密钥链共享。 

### <a name="conditional-access-authentication-differences"></a>条件性访问身份验证差异

对于条件访问方案，使用适用于 iOS 的 MSAL 时，用户将会出现更少的提示。 这是因为，iOS 使用 broker 应用（Microsoft Authenticator），这在某些情况下不再需要提示用户。

### <a name="project-setup-differences"></a>项目设置差异

**macOS**

- 在 macOS 上设置项目时，请确保使用有效的开发或生产证书对应用程序进行签名。 MSAL 仍在未签名模式下工作，但它的行为与缓存持久性的行为有所不同。 应用只能出于调试目的而运行无符号。 如果你将应用程序分发到无符号，它将：
1. 在10.14 及更早版本中，MSAL 将在每次重新启动应用时提示用户输入密钥链密码。
2. 在 10.15 + 上，MSAL 将提示用户提供每个令牌获取的凭据。 

- macOS 应用无需实现 AppDelegate 调用。

**iOS**

- 还需要执行其他步骤来设置项目以支持身份验证代理流。 本教程介绍了这些步骤。
- iOS 项目需要注册 info.plist 中的自定义方案。 这不是 macOS 所必需的。
