---
title: IoT 中心将弃用 TLS 1.0 和 1.1 | Microsoft Docs
description: 关于 IoT 中心将弃用 TLS 1.0 和 1.1 以及支持的密码的指南。
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: fcf7620f53c9bfdb51eb62598f2c8b441574eca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90006074"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub"></a>IoT 中心将弃用 TLS 1.0 和 1.1

为了提供更好的加密功能，IoT 中心将改用传输层安全性 (TLS) 1.2 作为 IoT 设备和服务的加密机制。 

## <a name="timeline"></a>时间线

在进一步通知之前，IoT 中心将继续支持 TLS 1.0/1.1。 但是，我们建议所有客户尽快改用 TLS 1.2。

## <a name="deprecating-tls-11-ciphers"></a>弃用 TLS 1.1 密码

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SH`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="deprecating-tls-10-ciphers"></a>弃用 TLS 1.0 密码

* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="tls-12-cipher-suites"></a>TLS 1.2 密码套件

请参阅 [IoT 中心 TLS 1.2 密码套件](iot-hub-tls-support.md#cipher-suites)。
 
## <a name="customer-feedback"></a>客户反馈

尽管强制实施 TLS 1.2 是业内的最佳加密选择，并且即将按计划启用该协议，但我们仍希望收到客户在特定部署和 TLS 1.2 采用难题方面的反馈。 为此，可将意见发送至 [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com)。
