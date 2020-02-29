---
title: IoT 中心和设备预配服务（DPS）中的弃用 TLS 1.0 和 1.1 |Microsoft Docs
description: IoT 中心和 DPS 中有关弃用 TLS 1.0 和1.1 以及支持的密码的准则。
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: 8e2f8fd7f99c359949b02959cc442f2f3d3ebfff
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912148"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>弃用 IoT 中心和设备预配服务中的 TLS 1.0 和1。1

为了提供同类最佳的加密，IoT 中心和设备预配服务（DPS）将迁移到传输层安全性（TLS）1.2，作为 IoT 设备和服务的首选加密机制。 这种情况下，在**2020 年7月1日，** 将不再支持 tls 1.0 和 tls 1.1 以及多个非建议的旧密码。


## <a name="impact"></a>影响
根据客户的特定环境和配置，不推荐使用 TLS 1.0 和1.1，并且不建议使用的旧式密码可能是与 IoT 中心或 DPS 通信的 IoT 设备和服务的有影响力更改。 在某些情况下，与这些更改不兼容的设备和服务将无法在上述截止日期之后连接到 IoT 中心或 DPS。


## <a name="supported-ciphers"></a>支持的密码

TLS 握手中使用的各种密码可用性的时间线如下所示：

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 （当前支持）
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 （将在2020的下半年支持）
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 （将在2020的下半年支持）
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 （将在2020的下半年支持）


## <a name="customer-feedback"></a>客户反馈

尽管 TLS 1.2 强制是行业范围内的最佳加密选择，并将按计划启用，但我们仍希望收到有关其特定部署的客户，以及采用 TLS 1.2 的问题。 为此，你可以将你的评论发送到[iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
