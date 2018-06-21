---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 383c4a53913333a3e25006980dd7533b9e243a4a
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236666"
---
从 2018 年 7 月 1 日开始，Azure VPN 网关将不再支持 TLS 1.0 和 1.1。 VPN 网关将仅支持 TLS 1.2。 若要对使用 TLS 的 Windows 7 和 Windows 8 点到站点客户端保留 TLS 支持和连接性，建议安装以下更新：

•   [支持使用 TLS 的 Microsoft EAP 实现的更新](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

•   [启用 TLS 1.1 和 TLS 1.2 作为 WinHTTP 中的默认安全协议的更新](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

TLS 也将于 2018 年 7 月 1 日起弃用以下旧算法：

* RC4 (Rivest Cipher 4)
* DES（数据加密算法）
* 3DES（三重数据加密算法）
* MD5（消息摘要 5）
* SHA-1（安全哈希算法 1）