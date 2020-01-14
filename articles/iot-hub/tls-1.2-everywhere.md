---
title: 在 IoT 中心和设备预配服务（DPS）中弃用 TLS 1.0 和 1.1 |Microsoft Docs
description: IoT 中心和 DPS 中有关弃用 TLS 1.0 和1.1 以及支持的密码的准则。
author: rezas
ms.author: reza
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d45221d7ae084c9dde71489df016b5798c53c7f1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931129"
---
# <a name="tls-10-and-11-deprecation-in-iot-hub-and-device-provisioning-service"></a>IoT 中心和设备预配服务中的 TLS 1.0 和1.1 弃用

为了提供同类最佳的加密，IoT 中心和设备预配服务（DPS）将迁移到传输层安全性（TLS）1.2，作为 IoT 设备和服务的首选加密机制。 这种情况下，在**2020 年7月1日，** 将不再支持 tls 1.0 和 tls 1.1 以及多个非建议的旧密码。


## <a name="impact"></a>影响
根据客户的特定环境和配置，不推荐使用 TLS 1.0 和1.1，并且不建议使用的旧式密码可能是与 IoT 中心或 DPS 通信的 IoT 设备和服务的有影响力更改。 在某些情况下，与这些更改不兼容的设备和服务将无法在上述截止日期之后连接到 IoT 中心或 DPS。


## <a name="supported-ciphers"></a>支持的密码

TLS 握手期间只允许以下密码：

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="customer-feedback"></a>客户反馈

尽管 TLS 1.2 的强制实施是行业范围内同类最佳的加密选项并将按计划启用，但我们仍然希望了解客户在采用 TLS 1.2 时的具体部署和遇到的困难。 为此，你可以将你的评论发送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。