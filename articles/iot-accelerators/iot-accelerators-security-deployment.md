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
ms.openlocfilehash: 9422a4ce316053b6560f1e945de7d8018e52c15b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658955"
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
还可以浏览 IoT 解决方案加速器的一些其他特性和功能：

* [预测性维护解决方案加速器概述][lnk-predictive-overview]
* [IoT 解决方案加速器的常见问题解答][lnk-faq]

可以通过 IoT 中心开发人员指南中的[控制 IoT 中心的访问权限][lnk-devguide-security]详细了解 IoT 中心安全性。

[lnk-predictive-overview]:iot-accelerators-predictive-overview.md
[lnk-faq]:iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
