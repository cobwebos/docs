---
title: IoT 中心的弃用 TLS 1.0 和 1.1 |Microsoft Docs
description: 在 IoT 中心中弃用 TLS 1.0 和1.1 以及支持的密码的相关准则。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81381300"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>在 IoT 中心弃用 TLS 1.0 和1。1

为了提供最佳的类加密，IoT 中心将迁移到传输层安全性（TLS）1.2，作为 IoT 设备和服务的选择的加密机制。 

## <a name="timeline"></a>时间线

在进一步通知之前，IoT 中心将继续支持 TLS 1.0/1.1。 但是，我们建议所有客户尽快迁移到 TLS 1.2。

## <a name="supported-ciphers"></a>支持的密码

TLS 握手中使用的各种密码可用性的时间线如下所示：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 （当前支持）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 （将在2020的下半年支持）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 （将在2020的下半年支持）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 （将在2020的下半年支持）

## <a name="customer-feedback"></a>客户反馈

尽管 TLS 1.2 强制是行业范围内的最佳加密选择，并将按计划启用，但我们仍希望了解客户有关其特定部署的反馈以及在采用 TLS 1.2 时遇到哪些困难。 为此，可以将注释发送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
