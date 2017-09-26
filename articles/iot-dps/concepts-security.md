---
title: "Azure IoT 中心设备预配服务中的安全性概念 | Microsoft Docs"
description: "介绍安全性预配概念，特定于使用设备预配服务和 IoT 中心的设备"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.contentlocale: zh-cn
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT 中心设备预配服务安全性概念 

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全且可缩放的方式预配数百万台设备。 本文概述了设备预配中涉及的安全性概念。 本文涉及设备部署准备工作中提及的所有角色。

## <a name="attestation-mechanism"></a>证明机制

证明机制是用于确认设备标识的方法。 证明机制也与注册列表相关，注册列表告知预配服务对给定设备使用哪种认证方法。

> [!NOTE]
> IoT 中心将该服务中类似的概念称为“身份验证方案”。

设备预配服务支持两种证明形式：
* 基于标准 X.509 证书身份验证流的 X.509 证书。
* 基于对密钥使用 TPM 标准的 nonce 质询的 SAS 令牌。 这不需要设备上的物理 TPM，但是服务要求按照 [TPM 规范](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)使用认可密钥来证明。

## <a name="hardware-security-module"></a>硬件安全模块

硬件安全模块（或称 HSM）用于安全的、基于硬件的设备机密存储，是最安全的机密存储形式。 X.509 证书和 SAS 令牌都可以存储在 HSM 中。 HSM 可以与预配支持的证明机制一起使用。

> [!TIP]
> 我们强烈建议将 HSM 用于设备，以便安全地存储设备上的机密。

设备机密也可以存储在软件（内存）中，但它是比 HSM 更不安全的存储形式。

## <a name="trusted-platform-module-tpm"></a>受信任的平台模块 (TPM)

TPM 可引用适用于安全存储用于对平台进行身份验证的密钥的标准，或者可引用用于与实现标准的模块交互的 I/O 接口。 TPM 可以作为离散硬件、集成硬件、基于固件或基于软件的方式存在。 详细了解 [TPM 和 TPM 证明](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)。 设备预配服务仅支持 TPM 2.0。

## <a name="endorsement-key"></a>认可密钥

认可密钥是 TPM 内部包含的非对称密钥，其在制造时注入并且对于每个 TPM 是唯一的。 不能更改或删除认可密钥。 认可密钥的私有部分绝不会在 TPM 之外发布，而其公共部分则用于识别 TPM 的真伪。 详细了解[认可密钥](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)。

## <a name="storage-root-key"></a>存储根密钥

存储根密钥存储在 TPM 中，用于保护应用程序创建的 TPM 密钥，这样在没有 TPM 的情况下便无法使用这些密钥。 拥有 TPM 的所有权时，会生成存储根密钥；清除 TPM 以便新用户可以获得所有权时，会生成新的存储根密钥。 详细了解[存储根密钥](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)。

## <a name="root-certificate"></a>根证书

根证书是表示证书颁发机构的 X.509 类型的证书，并且是自签名的。 它是证书链的终点。

## <a name="intermediate-certificate"></a>中间证书

中间证书是已由根证书（或其链中具有根证书的其他证书）签名的 X.509 证书，用于对分支证书进行签名。

## <a name="leaf-certificate"></a>分支证书

分支证书（或称最终实体证书）用于标识证书持有者，其根证书位于证书链中。 分支证书不用于对任何其他证书进行签名。

