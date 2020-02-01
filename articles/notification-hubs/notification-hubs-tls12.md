---
title: 通知中心 TLS 更新
description: 了解 Azure 通知中心中的 TLS 支持。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908522"
---
# <a name="transport-layer-security-tls"></a>传输层安全性（TLS）

为了确保更高的安全级别，通知中心将在1.0 年4月 30 2020 日禁用对 TLS 版本和1.1 的支持。 这些较旧的协议提供弱加密，容易受到 BEAST 和贵宾攻击。 部署到运行 Android 版本5或更高版本（或更高版本）或更高版本的 iOS 的应用程序不受此更改的影响，因为这些操作系统支持 TLS 1.2，并且客户端和服务器将协商连接时的协议。

建议你查看使用 Azure 通知中心的所有应用程序，以确保它们使用支持 TLS 1.2 的最适用的库和 TLS 堆栈。

## <a name="update-apps"></a>更新应用

可以通过名为应用传输安全（ATS）的 Apple 网络安全功能，确保 iOS 应用正在使用 TLS 1.2。 ATS 不能用于早于 iOS 9.0 或 macOS 10.11 的 Sdk，你可以从[Apple 文档](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)中进一步阅读。

对于使用 SSLSocket 实例的 Android 应用程序，请在每个 SSLSocket 实例上设置启用的协议，如[setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))中所述。

[Tls 协议兼容性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)支持页上的表有助于映射兼容 TLS 版本的操作系统。

有关详细信息，请参阅[Windows 上的 TLS 协议支持](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)概述。

## <a name="next-steps"></a>后续步骤

- [通知中心概述](notification-hubs-push-notification-overview.md)