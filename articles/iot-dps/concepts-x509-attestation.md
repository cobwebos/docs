---
title: Azure IoT 中心设备预配服务 - X.509 证书证明
description: 介绍将 X.509 证书证明用于设备预配服务 (DPS) 和 IoT 中心时特有的概念
author: wesmc7777
ms.author: wesmc
ms.date: 09/14/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 3e06c79b9cbd5643d119974a4ed8628ea1b1cd4f
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096753"
---
# <a name="x509-certificate-attestation"></a>X.509 证书证明

本文概述了使用 x.509 证书证明预配设备时所涉及的设备预配服务 (DPS) 概念。 本文涉及设备部署准备工作中提及的所有角色。

X.509 证书可以存储在硬件安全模块 HSM 中。

> [!TIP]
> 我们强烈建议将 HSM 用于设备，以便在生产环境中的设备上安全地存储机密（例如，X.509 证书）。


## <a name="x509-certificates"></a>X.509 证书

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常是信任证书链中一系列证书中的一个，证书链中的每个证书均通过下一个更高级别证书的私钥进行签名，位于链顶端的证书是自签名的根证书。 此安排会建立一个委托的信任链，该信任链始于受信任根证书颁发机构 (CA) 生成的根证书，期间是每个中间 CA，终结于设备上安装的最终实体“叶”证书。 有关详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](/azure/iot-hub/iot-hub-x509ca-overview)。 

证书链通常代表与设备关联一些逻辑或物理层次结构。 例如，制造商可以：
- 颁发自签名根 CA 证书
- 使用根证书为每个工厂生成唯一的中间 CA 证书
- 使用每个工厂的证书为工厂中的每条生产线生成唯一的中间 CA 证书
- 并最终使用生产线证书为在生产线上制造的每台设备生成唯一的设备（最终实体）证书。 

若要了解详细信息，请参阅[概念性理解 IoT 行业中的 X.509 CA 证书](/azure/iot-hub/iot-hub-x509ca-concept)。 

### <a name="root-certificate"></a>根证书

根证书是表示证书颁发机构 (CA) 的自签名的 X.509 证书。 它是证书链的终点或信任定位点。 根证书可由组织自行颁发或从根证书颁发机构购买。 若要了解详细信息，请参阅[获取 X.509 CA 证书](/azure/iot-hub/iot-hub-security-x509-get-started#get-x509-ca-certificates)。 根证书也可称为根 CA 证书。

### <a name="intermediate-certificate"></a>中间证书

中间证书是已由根证书（或其链中具有根证书的另一个中间证书）签名的 X.509 证书。 链中的最后一个中间证书用于对分支证书进行签名。 中间证书也可称为中间 CA 证书。

##### <a name="why-are-intermediate-certs-useful"></a>为什么中间证书有用？
可以通过多种方式使用中间证书。 例如，可使用中间证书按产品系列、购买设备、公司部门或工厂的客户对设备进行分组。 

假设 Contoso 是一个具有自己的公钥基础结构的大型企业 (PKI) 使用名为 *ContosoRootCert*的根证书。 Contoso 的每个子公司都有自己的由 *ContosoRootCert*签署的中间证书。 然后，每个子公司都将使用其中间证书对每个设备的叶证书进行签名。 在此方案中，Contoso 可以使用单个 DPS 实例，其中 *ContosoRootCert* 已使用 [所有权证明](./how-to-verify-certificates.md)进行验证。 它们可以具有每个子公司的注册组。 这样，每个子公司都无需担心验证证书。


### <a name="end-entity-leaf-certificate"></a>最终实体“叶”证书

分支证书或最终实体证书标识证书持有者。 它具有其证书链中的根证书以及零个或多个中间证书。 分支证书不用于对任何其他证书进行签名。 它向设置服务唯一标识设备，有时称为设备证书。 在身份验证期间，设备使用与此证书关联的私钥响应来自服务的所有权证明质询。

与[单个注册](./concepts-service.md#individual-enrollment)条目配合使用的页证书有一个要求：必须将“所有者名称”**** 设置为“单个注册”条目的注册 ID。 与 [注册组](./concepts-service.md#enrollment-group) 条目一起使用的叶证书应将 " **使用者名称** " 设置为所需的设备 ID，该 ID 将显示在注册组中经过身份验证的设备的 **注册记录** 中。

有关详细信息，请参阅[对使用 X.509 CA 证书签名的设备进行身份验证](/azure/iot-hub/iot-hub-x509ca-overview#authenticating-devices-signed-with-x509-ca-certificates)。

## <a name="controlling-device-access-to-the-provisioning-service-with-x509-certificates"></a>使用 X.509 证书控制设备对设置服务的访问权限

预配服务公开了两个注册类型，可用于通过 x.509 证明机制来控制设备访问：  

- [单个注册](./concepts-service.md#individual-enrollment)条目使用与特定设备关联的设备证书进行配置。 这些条目控制特定设备的注册。
- [注册组](./concepts-service.md#enrollment-group)条目与特定的中间或根 CA 证书关联。 这些条目控制其证书链中具有中间或根证书的所有设备的注册。 

#### <a name="dps-device-chain-requirements"></a>DPS 设备链要求

当设备尝试使用注册组通过 DPS 注册时，设备必须将证书链从叶证书发送到使用 [所有权证明](how-to-verify-certificates.md)验证的证书。 否则，身份验证将失败。

例如，如果只验证根证书并将中间证书上传到注册组，则设备应将证书链从叶证书一直显示到验证的根证书。 此证书链将包含中间的任何中间证书。 如果 DPS 无法遍历验证证书的证书链，则身份验证将失败。

例如，为设备使用以下设备链。

![设备证书链示例](./media/concepts-x509-attestation/example-device-cert-chain.png) 

仅验证根证书，并在注册组上上传 *intermediate2* 证书。

![根验证示例](./media/concepts-x509-attestation/example-root-verified.png) 

如果设备在预配期间只发送以下设备链，则身份验证将失败。 因为 DPS 无法尝试进行身份验证，假定 *intermediate1* 证书的有效性

![证书链的示例失败](./media/concepts-x509-attestation/example-fail-cert-chain.png) 

如果设备在预配过程中按如下所示发送完整的设备链，则 DPS 可以尝试对设备进行身份验证。

![设备证书链示例](./media/concepts-x509-attestation/example-device-cert-chain.png) 




> [!NOTE]
> 还可以通过 [所有权证明](how-to-verify-certificates.md)验证中间证书。


#### <a name="dps-order-of-operations-with-certificates"></a>DPS 操作的证书顺序
当设备连接到设置服务时，服务会优先应用更具体的注册条目，接着才应用不那么具体的注册条目。 也就是说，如果存在单个设备注册，设置服务会应用该条目。 如果设备没有单独注册，并且设备证书链中第一个中间证书的注册组存在，则该服务会将该条目应用到根节点，依此类推。 服务会应用找到的第一个适用的条目：

- 如果找到的第一个注册条目已启用，服务会对设备进行设置。
- 如果找到的第一个注册条目为禁用状态，服务不会对设备进行设置。  
- 如果没有为设备证书链中的任何证书找到注册条目，服务不会对设备进行设置。 

通过此机制和证书链的层次结构，在控制单个设备及一组设备的访问权限时可实现极大的控制上的灵活性。 例如，假设有五台设备具有以下证书链： 

- *设备 1*：根证书 -> 证书 A -> 设备 1 证书
- *设备 2*：根证书 -> 证书 A -> 设备 2 证书
- *设备 3*：根证书 -> 证书 A -> 设备 3 证书
- *设备 4*：根证书 -> 证书 A -> 设备 4 证书
- *设备 5*：根证书 -> 证书 A -> 设备 5 证书

最开始，可为根证书创建单个启用的组注册条目，让五台设备均获得访问权限。 如果之后证书 B 出现安全风险，可以为证书 B 创建一个禁用的注册组条目，以防止设备 4 和设备 5 进行注册****。 如果之后设备 3 ** 出现安全风险，可为其证书创建一个禁用的单个注册条目。 这会撤消设备 3 的访问权限，但仍允许设备 1 和设备 2 进行注册******。