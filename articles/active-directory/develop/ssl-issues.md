---
title: 排除 TLS/SSL 问题（MSAL iOS/macOS） |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解如何使用带有 MSAL 的 TLS/SSL 证书解决各种问题。目标 C 库。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881071"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>如何：针对 iOS 和 macOS TLS/SSL 问题排除 MSAL 故障

本文介绍如何排查使用[用于 iOS 和 macOS 的 Microsoft Authentication 库 (MSAL)](reference-v2-libraries.md) 时可能会遇到的问题

## <a name="network-issues"></a>网络问题

**错误 -1200**："发生了 SSL 错误，无法与服务器建立安全连接。

此错误意味着连接不安全。 证书无效时会发生此错误。 有关详细信息（包括哪个服务器未通过 TLS 检查），请参阅`NSURLErrorFailingURLErrorKey`错误对象的`userInfo`字典。

此错误来自 Apple 的网络库。 NSURL 错误代码的完整列表位于 macOS 和 iOS 的 SDK 的 NSURLError.h 中。 有关此错误的更多详细信息，请参阅 [URL Loading System Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)（URL 加载系统错误代码）。

## <a name="certificate-issues"></a>证书问题

如果提供无效证书的 URL 连接到您打算用作身份验证流的一部分的服务器，则诊断问题的一个好开始是使用 SSL 验证服务（如[SSL 服务器测试](https://www.ssllabs.com/ssltest/analyze.html)）测试 URL。 它会针对一系列广泛的方案和浏览器测试服务器，并针对许多已知漏洞进行检查。

默认情况下，Apple 新的[应用传输安全 （ATS）](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35)功能对使用 TLS/SSL 证书的应用应用更严格的安全策略。 某些操作系统和 Web 浏览器已开始在默认情况下强制实施这些策略中的一部分。 出于安全原因，我们建议不要禁用 ATS。

使用 SHA-1 哈希的证书存在已知的漏洞。 大多数新式 Web 浏览器不允许使用 SHA-1 哈希的证书。

## <a name="captive-portals"></a>强制网络门户

当用户首次访问某个 Wi-Fi 网络且尚未被授予该网络的访问权限时，强制网络门户会向该用户提供一个网页。 它会截获用户的 Internet 流量，直至用户满足门户的要求。 在用户通过门户进行连接之前，预期会出现网络错误（因为用户不能连接到网络资源）。

## <a name="next-steps"></a>后续步骤

了解[强制网络门户](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 的新[应用传输安全性 (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 功能。
