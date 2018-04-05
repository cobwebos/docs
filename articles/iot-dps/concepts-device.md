---
title: Azure 设备预配中的设备概念 | Microsoft Docs
description: 介绍设备预配概念，特定于使用设备预配服务和 IoT 中心的设备
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 5482801461e2afea33d65d559723116f37a35d1f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT 中心设备预配服务设备概念

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全且可缩放的方式预配数百万台设备。

本文概述了设备预配中涉及的设备概念。 本文与设备部署准备工作的[制造步骤](about-iot-dps.md#manufacturing-step)中提及的角色最为相关。

## <a name="attestation-mechanism"></a>证明机制

证明机制是用于确认设备标识的方法。 证明机制也与注册列表相关，注册列表告知预配服务对给定设备使用哪种认证方法。

> [!NOTE]
> IoT 中心将该服务中类似的概念称为“身份验证方案”。

设备预配服务支持两种证明形式：
* 基于标准 X.509 证书身份验证流的 X.509 证书。
* 基于 nonce 质询的受信任平台模块 (TPM)，使用密钥的 TPM 标准显示已签名的共享访问签名 (SAS) 令牌。 这不需要设备上的物理 TPM，但是服务要求按照 [TPM 规范](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)使用认可密钥来证明。

## <a name="hardware-security-module"></a>硬件安全模块

硬件安全模块（或称 HSM）用于安全的、基于硬件的设备机密存储，是最安全的机密存储形式。 X.509 证书和 SAS 令牌都可以存储在 HSM 中。 HSM 可以与预配服务支持的证明机制一起使用。

> [!TIP]
> 我们强烈建议将 HSM 用于设备，以便安全地存储设备上的机密。

设备机密也可以存储在软件（内存）中，但它是比 HSM 更不安全的存储形式。

## <a name="registration-id"></a>注册 ID

注册 ID 用于唯一标识设备预配服务中的设备。 设备 ID 在预配服务 [ID范围](#id-scope)中必须是唯一的。 每个设备必须具有注册 ID。 注册 ID 是小写的字母数字，并且可包含连字符。

* 对于使用 TPM 的情况，注册 ID 由 TPM 本身提供。
* 对于使用基于 X.509 证明的情况，提供注册 ID 作为证书的使用者名称。

## <a name="device-id"></a>设备 ID

设备 ID 是设备在 IoT 中心中显示的 ID。 可以在注册项目中设置所需的设备 ID，但不需要进行设置。 如果注册列表中未指定所需设备 ID，注册设备时将使用注册 ID 作为设备 ID。 详细了解 [IoT 中心中的设备 ID](../iot-hub/iot-hub-devguide-identity-registry.md)。

## <a name="id-scope"></a>ID 范围

ID 范围在由用户创建时分配给设备预配服务，用于唯一标识设备将通过其注册的特定预配服务。 ID 范围由服务生成且不可变，这保证了唯一性。

> [!NOTE]
> 唯一性对于长期运行的部署操作以及合并和收购方案而言非常重要。
