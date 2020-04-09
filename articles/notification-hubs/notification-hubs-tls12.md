---
title: 通知中心 TLS 更新
description: 了解 Azure 通知中心对 TLS 的支持。
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885746"
---
# <a name="transport-layer-security-tls"></a>传输层安全 (TLS) (Transport Layer Security) (TLS)

为确保更高的安全级别，通知中心将在 2020 年 5 月 31 日（从 2020 年 4 月 30 日起延长）禁用对 TLS 版本 1.0 和 1.1 的支持。 这些较旧的协议提供弱加密，并且容易受到 BEAST 和 POODLE 攻击。 部署到运行 Android 版本 5 或更高版本或 iOS 版本 5 或更高版本的设备的应用程序不受此更改的影响，因为这些操作系统支持 TLS 1.2，客户端和服务器将在连接时协商协议的最高相互支持版本。

我们建议您查看使用 Azure 通知中心的所有应用程序，以确保它们使用支持 TLS 1.2 的最适用的库和 TLS 堆栈。

## <a name="update-apps"></a>更新应用

您可以使用 Apple 的网络安全功能"应用传输安全 （ATS）"确保 iOS 应用使用 TLS 1.2。 ATS 不能用于超过 iOS 9.0 或 macOS 10.11 的 SDK，您也可以从[Apple 的文档](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)中进一步阅读有关它。

对于使用 SSLSocket 实例的 Android 应用程序，在每个 SSLSocket 实例上设置启用的协议，如[set Enabled 协议 中](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))所述。

[TLS 协议兼容性](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility)支持页上的表可帮助映射具有兼容 TLS 版本的操作系统。

有关详细信息，请参阅[Windows 上对 TLS 协议的支持](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows)概述。

## <a name="next-steps"></a>后续步骤

- [通知中心概述](notification-hubs-push-notification-overview.md)