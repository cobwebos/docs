---
title: 保护 Azure 物联网 (IoT) 部署 | Microsoft Docs
description: 本文详细说明如何保护 Azure IoT 部署
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: dobett
ms.openlocfilehash: de4896ac1022cbd2bc19af102c96cfbfa89c0041
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099014"
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
