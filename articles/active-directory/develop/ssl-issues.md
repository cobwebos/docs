---
title: 排查 TLS/SSL 问题 (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: 了解如何处理将 TLS/SSL 证书与 MSAL.Objective-C 库配合使用时出现的各种问题。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881071"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>如何：排查 iOS 和 macOS TLS/SSL 的 MSAL 问题

本文介绍如何排查使用[用于 iOS 和 macOS 的 Microsoft Authentication 库 (MSAL)](reference-v2-libraries.md) 时可能会遇到的问题

## <a name="network-issues"></a>网络问题

**错误 -1200**：“出现 SSL 错误，无法安全地连接到服务器。”

此错误意味着连接不安全。 证书无效时会发生此错误。 有关详细信息（包括哪个服务器没有通过 TLS 检查），请参考错误对象的 `userInfo` 字典中的 `NSURLErrorFailingURLErrorKey`。

此错误来自 Apple 的网络库。 NSURL 错误代码的完整列表位于 macOS 和 iOS 的 SDK 的 NSURLError.h 中。 有关此错误的更多详细信息，请参阅 [URL Loading System Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)（URL 加载系统错误代码）。

## <a name="certificate-issues"></a>证书问题

如果提供无效证书的 URL 连接到你要在身份验证流中使用的服务器，则在诊断问题时，可以先使用 SSL 验证服务（如 [SSL 服务器测试](https://www.ssllabs.com/ssltest/analyze.html)）测试该 URL。 它会针对一系列广泛的方案和浏览器测试服务器，并针对许多已知漏洞进行检查。

默认情况下，Apple 的新[应用传输安全性 (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 功能会将更严格的安全策略应用到使用 TLS/SSL 证书的应用。 某些操作系统和 Web 浏览器已开始在默认情况下强制实施这些策略中的一部分。 出于安全原因，我们建议不要禁用 ATS。

使用 SHA-1 哈希的证书存在已知的漏洞。 大多数新式 Web 浏览器不允许使用 SHA-1 哈希的证书。

## <a name="captive-portals"></a>强制网络门户

当用户首次访问某个 Wi-Fi 网络且尚未被授予该网络的访问权限时，强制网络门户会向该用户提供一个网页。 它会截获用户的 Internet 流量，直至用户满足门户的要求。 在用户通过门户进行连接之前，预期会出现网络错误（因为用户不能连接到网络资源）。

## <a name="next-steps"></a>后续步骤

了解[强制网络门户](https://en.wikipedia.org/wiki/Captive_portal)和 Apple 的新[应用传输安全性 (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) 功能。
