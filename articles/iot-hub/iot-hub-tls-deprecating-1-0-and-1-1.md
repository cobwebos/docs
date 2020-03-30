---
title: 在 IoT 中心和设备配置服务 （DPS） 中弃用 TLS 1.0 和 1.1 |微软文档
description: 关于在 IoT 中心和 DPS 中弃用 TLS 1.0 和 1.1 以及支持的密码的准则。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402795"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>在 IoT 中心和设备配置服务中弃用 TLS 1.0 和 1.1

为了提供同类最佳的加密，IoT 中心和设备预配服务 (DPS) 将迁移到传输层安全性 (TLS) 1.2，这是 IoT 设备和服务的首选加密机制。 

## <a name="supported-ciphers"></a>支持的密码

TLS 握手中使用的各种密码的可用性时间表如下：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256（当前支持）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384（将在2020年下半年得到支持）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256（2020年下半年将予以支持）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384（2020年下半年将予以支持）


## <a name="customer-feedback"></a>客户反馈

尽管 TLS 1.2 强制是行业范围内的最佳加密选择，并将按计划启用，但我们仍希望了解客户有关其特定部署的反馈以及在采用 TLS 1.2 时遇到哪些困难。 为此，您可以将注释发送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
