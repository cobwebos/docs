---
title: 用于 Azure IoT 中心设备预配服务的术语 |Microsoft Docs
description: 描述在 (DPS) 和 IoT 中心的设备预配服务中使用的常见术语
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531604"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>IoT 中心设备预配服务 (DPS) 术语

IoT 中心设备预配服务是一项 IoT 中心帮助程序服务，该服务用于将零接触设备预配到指定 IoT 中心。 使用设备预配服务，可以通过安全且可缩放的方式 [预配](about-iot-dps.md#provisioning-process) 数百万台设备。

设备预配是一个两部分过程。 第一部分是通过注册设备来建立设备和 IoT 解决方案之间的初始连接  。 第二部分是根据解决方案的具体要求将适当的配置应用于设备  。 在这两个步骤都完成后，设备已完全预配  。 设备预配服务自动执行这两个步骤，为设备提供无缝的预配体验。

本文概述了最适用于管理服务的预配概念  。 本文与设备部署准备工作的[云设置步骤](about-iot-dps.md#cloud-setup-step)中提及的角色最为相关。

## <a name="service-operations-endpoint"></a>服务操作终结点

服务操作终结点是用于管理服务设置和维护注册列表的终结点。 此终结点仅由服务管理员使用，设备不使用它。

## <a name="device-provisioning-endpoint"></a>设备预配终结点

设备预配终结点是单一终结点，所有设备都使用它进行自动预配。 此 URL 对于所有预配服务实例都是相同 ，因而无需使用供应链方案中的新连接信息来刷新设备。 ID 范围可确保租户隔离。

## <a name="linked-iot-hubs"></a>链接 IoT 中心

设备预配服务只能将设备预配到已链接到它的 IoT 中心。 将 IoT 中心链接到设备预配服务实例可以为 IoT 中心的设备注册表提供服务读/写权限；通过该链接，设备预配服务可以注册设备 ID 并在设备孪生中设置初始配置。 链接 IoT 中心可能位于任何 Azure 区域。 可将其他订阅中的中心链接到预配服务。


## <a name="allocation-policy"></a>分配策略

用于确定设备预配服务如何将设备分配给 IoT 中心的服务级别设置。 支持三种分配策略：

* **均匀加权分发**：链接的 IoT 中心等可能地获得预配到它们的设备。 默认设置。 如果只将设备预配到一个 IoT 中心，则可以保留此设置。

* **最低延迟**：将设备预配到具有最低延迟的 IoT 中心。 如果多个链接 IoT 中心提供相同的最低延迟，则预配服务将在这些中心上散列设备

* **通过注册列表进行静态配置**：注册列表中所需 IoT 中心的规范优先于服务级别分配策略。

* **自定义 (使用 Azure 函数) **：自定义分配策略使你可以更好地控制如何将设备分配到 IoT 中心。 它是通过使用 Azure 函数中的自定义代码将设备分配到 IoT 中心来实现的。 设备预配服务调用 Azure Function 代码，提供有关设备和代码注册的所有相关信息。 将执行函数代码并返回用于预配设备的 IoT 中心信息。

## <a name="enrollment"></a>注册

注册是指可以通过自动预配注册的设备或设备组的记录。 注册记录包含有关设备或设备组的信息，包括：
- 设备使用的[证明机制](#attestation-mechanism)
- 可选的初始所需配置
- 所需的 IoT 中心
- 所需的设备 ID

设备预配服务支持两种类型的注册：

### <a name="enrollment-group"></a>注册组

注册组是一组共享特定证明机制的设备。 注册组支持 X.509 和对称。 X.509 注册组中的所有设备都提供已由同一根或中间证书颁发机构 (CA) 签名的 X.509 证书。 对称密钥注册组中的每个设备都提供派生自组对称密钥的 SAS 令牌。 注册组名称和证书名称必须是小写的字母数字，并可包含连字符。

> [!TIP]
> 建议对共享所需初始配置的大量设备，或者全部转到同一租户的设备使用注册组。

### <a name="individual-enrollment"></a>单独注册

单独注册是用于可注册的单一设备的条目。 个人注册可使用 X.509 叶证书或 SAS 令牌（来自物理或虚拟 TPM）作为证明机制。 单独注册中的注册 ID 是小写的字母数字，并且可包含连字符。 单独注册可能会指定所需 IoT 中心设备 ID。

> [!TIP]
> 对于需要唯一初始配置的设备或仅能通过 TPM 证明使用 SAS 令牌进行身份验证的设备，建议为其使用个人注册。


## <a name="attestation-mechanism"></a>证明机制

证明机制是用于确认设备标识的方法。 在注册项上配置证明机制，并通知预配服务在注册期间验证设备的身份时使用的方法。

> [!NOTE]
> IoT 中心将该服务中类似的概念称为“身份验证方案”。

设备预配服务支持以下证明形式：
* 基于标准 X.509 证书身份验证流的 X.509 证书****。 有关详细信息，请参阅 [x.509 证明](concepts-x509-attestation.md)。
* 基于 nonce 质询的受信任平台模块 (TPM)，使用密钥的 TPM 标准显示已签名的共享访问签名 (SAS) 令牌****。 这不需要在设备上使用物理 TPM，但服务需要根据 [TPM 规范](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)使用认可密钥进行证明。有关详细信息，请参阅 [TPM 证明](concepts-tpm-attestation.md)。
* 基于共享访问签名的**对称密钥** (SAS) [安全令牌](../iot-hub/iot-hub-devguide-security.md#security-tokens)，其中包括哈希签名和嵌入的过期时间。 有关详细信息，请参阅[对称密钥证明](concepts-symmetric-key-attestation.md)。


## <a name="hardware-security-module"></a>硬件安全模块

硬件安全模块（或称 HSM）用于安全的、基于硬件的设备机密存储，是最安全的机密存储形式。 X.509 证书和 SAS 令牌都可以存储在 HSM 中。 HSM 可以与预配服务支持的证明机制一起使用。

> [!TIP]
> 我们强烈建议将 HSM 用于设备，以便安全地存储设备上的机密。

设备机密也可以存储在软件（内存）中，但它是比 HSM 更不安全的存储形式。



## <a name="id-scope"></a>ID 范围

ID 范围在由用户创建时分配给设备预配服务，用于唯一标识设备将通过其注册的特定预配服务。 ID 范围由服务生成且不可变，这保证了唯一性。

> [!NOTE]
> 唯一性对于长期运行的部署操作以及合并和收购方案而言非常重要。


## <a name="registration"></a>注册

注册是设备通过设备预配服务成功注册/预配到 IoT 中心的记录。 注册记录自动创建，可以删除，但不能更新。


## <a name="registration-id"></a>注册 ID

注册 ID 用于唯一标识设备预配服务的设备注册。 设备 ID 在预配服务 [ID范围](#id-scope)中必须是唯一的。 每个设备必须具有注册 ID。 注册 ID 是字母数字、不区分大小写，并可以包含特殊字符（包括冒号、句点、下划线和连字符）。

* 对于使用 TPM 的情况，注册 ID 由 TPM 本身提供。
* 对于使用基于 X.509 证明的情况，提供注册 ID 作为证书的使用者名称。

## <a name="device-id"></a>设备 ID

设备 ID 是设备在 IoT 中心中显示的 ID。 可以在注册项目中设置所需的设备 ID，但不需要进行设置。 设置所需设备 ID 仅在单独注册中受支持。 如果注册列表中未指定所需设备 ID，注册设备时将使用注册 ID 作为设备 ID。 详细了解 [IoT 中心中的设备 ID](../iot-hub/iot-hub-devguide-identity-registry.md)。



## <a name="operations"></a>操作

操作是设备预配服务的计费单位。 成功完成到服务的一条指令即为一次操作。 操作包括设备注册和重新注册，还包括服务侧更改（例如添加注册列表条目和更新注册列表条目）。
