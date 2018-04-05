---
title: Azure IoT 中心设备预配服务中的安全性概念 | Microsoft Docs
description: 介绍安全性预配概念，特定于使用设备预配服务和 IoT 中心的设备
services: iot-dps
keywords: ''
author: nberdy
ms.author: nberdy
ms.date: 03/27/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 5e35a802349bd85b50a13a3d9a7e0c78945937bd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT 中心设备预配服务安全性概念 

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全且可缩放的方式预配数百万台设备。 本文概述了设备预配中涉及的安全性概念。 本文涉及设备部署准备工作中提及的所有角色。

## <a name="attestation-mechanism"></a>证明机制

证明机制是用于确认设备标识的方法。 证明机制也与注册列表相关，注册列表告知预配服务对给定设备使用哪种认证方法。

> [!NOTE]
> IoT 中心将该服务中类似的概念称为“身份验证方案”。

设备预配服务支持两种证明形式：
* 基于标准 X.509 证书身份验证流的 X.509 证书。
* 基于 nonce 质询的受信任平台模块 (TPM)，使用密钥的 TPM 标准显示已签名的共享访问签名 (SAS) 令牌。 这不需要设备上的物理 TPM，但是服务要求按照 [TPM 规范](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)使用认可密钥来证明。

## <a name="hardware-security-module"></a>硬件安全模块

硬件安全模块（或称 HSM）用于安全的、基于硬件的设备机密存储，是最安全的机密存储形式。 X.509 证书和 SAS 令牌都可以存储在 HSM 中。 HSM 可以与预配支持的证明机制一起使用。

> [!TIP]
> 我们强烈建议将 HSM 用于设备，以便安全地存储设备上的机密。

设备机密也可以存储在软件（内存）中，但它是比 HSM 更不安全的存储形式。

## <a name="trusted-platform-module"></a>受信任的平台模块

TPM 可引用适用于安全存储用于对平台进行身份验证的密钥的标准，或者可引用用于与实现标准的模块交互的 I/O 接口。 TPM 可以作为离散硬件、集成硬件、基于固件或基于软件的方式存在。 详细了解 [TPM 和 TPM 证明](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)。 设备预配服务仅支持 TPM 2.0。

### <a name="endorsement-key"></a>认可密钥

认可密钥是 TPM 内部包含的非对称密钥，该密钥在制造时在内部生成或注入并且对于每个 TPM 是唯一的。 不能更改或删除认可密钥。 认可密钥的私有部分绝不会在 TPM 之外发布，而其公共部分则用于识别 TPM 的真伪。 详细了解[认可密钥](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)。

### <a name="storage-root-key"></a>存储根密钥

存储根密钥存储在 TPM 中，用于保护应用程序创建的 TPM 密钥，这样在没有 TPM 的情况下便无法使用这些密钥。 拥有 TPM 的所有权时，会生成存储根密钥；清除 TPM 以便新用户可以获得所有权时，会生成新的存储根密钥。 详细了解[存储根密钥](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)。

## <a name="x509-certificates"></a>X.509 证书

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常是信任证书链中一系列证书中的一个，证书链中的每个证书均通过下一个更高级别证书的私钥进行签名，位于链顶端的证书是自签名的根证书。 这会建立一个委托的信任链，该信任链始于受信任的根证书颁发机构 (CA) 生成的根证书，期间是每个中间 CA，终结于设备上安装的最终实体证书。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)。 

证书链通常代表与设备关联一些逻辑或物理层次结构。 例如，制造商可能会颁发自签名的根 CA 证书，使用该证书生成用于每个工厂的唯一中间 CA 证书，使用每个工厂的证书生成工厂中每个生产线的唯一中间 CA 证书，最后使用生产线证书为生产线上制造的每个设备生成唯一的设备（最终实体）证书。 若要了解详细信息，请参阅[概念性理解 IoT 行业中的 X.509 CA 证书](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)。 

### <a name="root-certificate"></a>根证书

根证书是表示证书颁发机构 (CA) 的自签名的 X.509 证书。 它是证书链的终点或信任定位点。 根证书可由组织自行颁发或从根证书颁发机构购买。 若要了解详细信息，请参阅[获取 X.509 CA 证书](https://docs.microsoft.com/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)。 根证书也可称为根 CA 证书。

### <a name="intermediate-certificate"></a>中间证书

中间证书是已由根证书（或其链中具有根证书的另一个中间证书）签名的 X.509 证书。 链中的最后一个中间证书用于对分支证书进行签名。 中间证书也可称为中间 CA 证书。

### <a name="leaf-certificate"></a>分支证书

分支证书或最终实体证书标识证书持有者。 它具有其证书链中的根证书以及零个或多个中间证书。 分支证书不用于对任何其他证书进行签名。 它向设置服务唯一标识设备，有时称为设备证书。 在身份验证期间，设备使用与此证书关联的私钥响应来自服务的所有权证明质询。 有关详细信息，请参阅[对使用 X.509 CA 证书签名的设备进行身份验证](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)。

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>使用 X.509 证书控制设备对设置服务的访问权限

设置服务公开两种类型的注册条目，可用于控制使用 X.509 证明机制的设备的访问权限：  

- [单个注册](./concepts-service.md#individual-enrollment)条目使用与特定设备关联的设备证书进行配置。 这些条目控制特定设备的注册。
- [注册组](./concepts-service.md#enrollment-group)条目与特定的中间或根 CA 证书关联。 这些条目控制其证书链中具有中间或根证书的所有设备的注册。 

当设备连接到设置服务时，服务会优先应用更具体的注册条目，接着才应用不那么具体的注册条目。 也就是说，如果存在单个设备注册，设置服务会应用该条目。 如果没有单个设备注册，而存在设备证书链中第一个中间证书的一个注册组，服务会应用该条目，并以此类推遍历到根证书。 服务会应用找到的第一个适用的条目：

- 如果找到的第一个注册条目已启用，服务会对设备进行设置。
- 如果找到的第一个注册条目为禁用状态，服务不会对设备进行设置。  
- 如果没有为设备证书链中的任何证书找到注册条目，服务不会对设备进行设置。 

通过此机制和证书链的层次结构，在控制单个设备及一组设备的访问权限时可实现极大的控制上的灵活性。 例如，假设有五台设备具有以下证书链： 

- *设备 1*：根证书 -> 证书 A -> 设备 1 证书
- *设备 2*：根证书 -> 证书 A -> 设备 2 证书
- *设备 3*：根证书 -> 证书 A -> 设备 3 证书
- *设备 4*：根证书 -> 证书 A -> 设备 4 证书
- *设备 5*：根证书 -> 证书 A -> 设备 5 证书

最开始，可为根证书创建单个启用的组注册条目，让五台设备均获得访问权限。 如果之后证书 B 出现安全风险，可以为证书 B 创建一个禁用的注册组条目，以防止设备 4 和设备 5 进行注册。 如果之后设备 3 出现安全风险，可为其证书创建一个禁用的单个注册条目。 这会撤消设备 3 的访问权限，但仍允许设备 1 和设备 2 进行注册。