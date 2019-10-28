---
title: 解决 MSAL 问题
titleSuffix: Microsoft identity platform
description: 了解如何使用带有 MSAL 的 SSL 证书应对各种问题。目标-C 库。
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc918502cd252b4e53af8bcbd209a8387ef4d8c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803669"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-ssl-issues"></a>如何：对 iOS 和 macOS SSL 问题的 MSAL 进行故障排除

本文提供的信息可帮助你解决使用[适用于 iOS 和 macOS 的 Microsoft 身份验证库（MSAL）](reference-v2-libraries.md)时可能遇到的问题

## <a name="network-issues"></a>网络问题

**错误-1200**：出现 SSL 错误，无法建立到服务器的安全连接。

此错误表示该连接不安全。 当证书无效时，会发生这种情况。 有关详细信息，包括 SSL 检查失败的服务器，请参阅 error 对象的 `userInfo` 字典中的 `NSURLErrorFailingURLErrorKey`。

此错误来自 Apple 的网络库。 NSURL 错误代码的完整列表位于 macOS 和 iOS Sdk 的 NSURLError 中。 有关此错误的详细信息，请参阅[URL 加载系统错误代码](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)。

## <a name="certificate-issues"></a>证书问题

如果提供无效证书的 URL 连接到要用作身份验证流一部分的服务器，则诊断问题的良好开端是使用 SSL 验证服务（如[QUALYS Ssl 实验室分析器](https://www.ssllabs.com/ssltest/analyze.html)）来测试 url。 它针对各种方案和浏览器来测试服务器，并检查许多已知的漏洞。

默认情况下，Apple 的新[应用传输安全（ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能将更严格的安全策略应用到使用 SSL 证书的应用。 某些操作系统和 web 浏览器在默认情况下已开始强制执行某些策略。 出于安全原因，我们建议您不要禁用 ATS。

使用 SHA-1 哈希的证书具有已知的漏洞。 大多数新式 web 浏览器不允许具有 SHA-1 哈希的证书。

## <a name="captive-portals"></a>捕获门户

当用户首次访问 Wi-fi 网络但尚未获得对该网络的访问权限时，它会向用户显示一个网页。 它会截获其 internet 流量，直到用户满足门户的要求。 网络错误，因为在用户通过门户连接之前，用户无法连接到网络资源。

## <a name="next-steps"></a>后续步骤

了解有关强制[门户](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 的新[应用传输安全（ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能的信息。
