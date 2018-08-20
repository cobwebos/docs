---
title: 保护物联网部署 | Microsoft Docs
description: 本文详细说明如何保护 IoT 部署
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 00acb08f567dbd50522d0e8a0b7b9a18a6658000
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038056"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>IoT 解决方案加速器密码套件

IoT 解决方案加速器按此顺序支持以下密码套件。

| 密码套件 | Length |
| --- | --- |
| TLS\_ECDHE\_RSA\_ 与 \_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1（等于 7680 位 RSA) FS |256 |
| TLS\_ECDHE\_RSA\_ 与 \_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1（等于 3072 位 RSA) FS |128 |
| TLS\_ECDHE\_RSA\_ 与 \_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1（等于 7680 位 RSA) FS |256 |
| TLS\_ECDHE\_RSA\_ 与 \_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1（等于 3072 位 RSA) FS |128 |
| TLS\_RSA\_ 与 \_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_ 与 \_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_ 与 \_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_ 与 \_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_ 与 \_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_ 与 \_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_ 与 \_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>另请参阅

阅读 IoT 中心开发人员指南中[控制对 IoT 中心的访问](../iot-hub/iot-hub-devguide-security.md)中关于 IoT 中心安全性的内容。 
