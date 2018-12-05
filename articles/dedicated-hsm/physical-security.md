---
title: Azure 专用 HSM 物理安全性 | Microsoft Docs
description: Azure 专用 HSM 在 Azure 中提供符合 FIPS 140-2 级别 3 认证的密钥存储功能
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: barclayn
ms.openlocfilehash: a0f85c755b269d95720137368a4ff438031a9fae
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2018
ms.locfileid: "52318916"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure 专用 HSM 物理安全性

Azure 专用 HSM 帮助满足密钥存储的高级安全要求。 它在整个生命周期内遵循严格的安全实践进行管理，以满足客户的需求。

## <a name="security-through-procurement"></a>采购安全性

Microsoft 遵循安全的采购流程。 我们对保管链进行管理，确保订购和发运的特定设备与到达数据中心的设备完全一致。 这些设备采用防篡改塑料后盖。 在数据中心的数据库内执行委托之前，它们将存储在安全的存储区域中。  机架包含 HSM 设备，被视为具有严重业务影响 (HBI)。 设备已锁定，且前后始终处于视频监控之下。

## <a name="security-through-deployment"></a>部署安全性

HSM 和关联的网络组建一起安装在机架中。 安装后，必须先进行配置，然后才能作为 Azure 专用 HSM 服务中的一部分运作。 此配置活动由经过背景检查的 Microsoft 员工执行。 “及时”(JIT) 管理用于限制访问权限，仅对相应的员工开放，且开放时间仅限访问所需时长。 过程和系统也用于确保记录了所有和 HSM 设备相关联的活动。

## <a name="security-in-operations"></a>操作安全性

HSM 属于硬件设备（HSM 实际是设备内的 PCI 卡），因此可能出现组件问题。 潜在的问题包括但不限于风扇和电源故障。 对于此类事件，需维护或中断/修复活动，更换任何可换组件。

### <a name="component-replacement"></a>组件更换

设备已预配且处于客户管理下后，热插拔电源时唯一可更换的组件。 此组件在安全边界之外，且不会导致篡改事件。 票证系统用于授权 Microsoft 工程师访问 HBI 机架背面。 处理票证时，会签发临时物理密钥。 此密钥为工程师提供访问设备的权限，并允许他们更换受影响的组件。 当设备未分配给客户时，需完成任何其他访问（即导致篡改事件），从而最小化安全性和可用性风险。  

### <a name="device-replacement"></a>设备更换

如果整个设备发生故障，需遵循与组件故障处理过程类似的过程进行更换。 如果客户无法将设备归零，或者设备处于未知状态，则数据承载设备将被移除并放置在机架内销毁箱中。 将以受控且安全的方式销毁放入该箱中的设备。 HBI 机架中的数据承载设备不会离开 Microsoft 数据中心。

### <a name="other-rack-access-activities"></a>其他机架访问活动

如果 Microsoft 工程师必须访问 HSM 设备使用的机架（例如进行网络设备维护时），需使用标准的安全过程来获取 HBI 安全机架的访问权限。 所有访问都将受视频监控。 HSM设备已验证为 [FIPS 140-2 级别 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf)，因此出现任何未经授权的 HSM 设备访问时，都将向客户发出信号并将数据归零。

## <a name="logical-level-security-considerations"></a>逻辑级别的安全注意事项

将 HSM 预配到客户创建的虚拟网络中。 这是客户的专用 IUP 地址空间。  此配置可提供有价值的逻辑网络级别隔离，并确仅客户可进行访问。 这意味着所有逻辑级别的安全控制都由客户负责。

## <a name="next-steps"></a>后续步骤

建议在进行设备预配、应用程序设计或部署之前，先深入了解此服务的所有重要概念（例如高可用性、安全性和可支持性）。

* [高可用性](high-availability.md)
* [网络](networking.md)
* [可支持性](supportability.md)
* [监视](monitoring.md)
* [部署体系结构](deployment-architecture.md)