---
title: 在 IoT 中心中弃用 TLS 1.0 和 1.1 |微软文档
description: 有关 TLS 1.0 和 1.1 的弃用以及 IoT 中心中支持的密码的准则。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: a887dd4df44ba58b0e6646ffb1c10eb21edf3e69
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381300"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>在 IoT 中心中弃用 TLS 1.0 和 1.1

为了提供一流的加密，IoT 中心正在转向传输层安全 （TLS） 1.2，作为 IoT 设备和服务的首选加密机制。 

## <a name="timeline"></a>时间线

IoT 中心将继续支持 TLS 1.0/1.1，直到另行通知。 但是，我们建议所有客户尽快迁移到 TLS 1.2。

## <a name="supported-ciphers"></a>支持的密码

TLS 握手中使用的各种密码的可用性时间表如下：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256（当前支持）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384（将在2020年下半年得到支持）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256（2020年下半年将予以支持）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384（2020年下半年将予以支持）

## <a name="customer-feedback"></a>客户反馈

尽管 TLS 1.2 强制是行业范围内的最佳加密选择，并将按计划启用，但我们仍希望了解客户有关其特定部署的反馈以及在采用 TLS 1.2 时遇到哪些困难。 为此，您可以将注释发送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
