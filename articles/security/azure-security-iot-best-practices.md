---
title: "物联网安全最佳实践 | Microsoft Docs"
description: "本文提供了 Microsoft 物联网安全最佳实践和一般建议的特选列表。"
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: yurid
ms.openlocfilehash: 8efc0053458e338ac1afe98d9ce970c1d5cbfa81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="internet-of-things-security-best-practices"></a>物联网安全最佳实践
保护物联网 (IoT) 基础结构是参与 IoT 解决方案的所有人员需要执行的一项关键任务。 由于涉及的设备数和这些设备的分布式性质，危害数以百万计的 IoT 设备的相关安全事件不容小视，具有广泛的影响。

因此，IoT 安全需要一个深入安全的方法。 由于数据在专用和公用网络上传输，需要在云中确保数据安全。 需要准备方法来安全地预配 IoT 设备本身。 每一层（从设备到网络，再到云后端）都需要强大的安全保证。

IoT 最佳实践可以按照以下方式分类：

* IoT 硬件制造商或系统集成商
* IoT 解决方案开发商
* IoT 解决方案部署人员
* IoT 解决方案操作员

此文章汇总了[物联网安全最佳实践](../iot-suite/iot-security-best-practices.md)。 有关更多详细信息，请参阅此文章。

## <a name="iot-hardware-manufacturer-or-integrator"></a>IoT 硬件制造商或系统集成商
如果是 IoT 硬件制造商或硬件集成商，请遵循以下最佳实践：

* **设置符合最低要求的硬件范围**：硬件设计应包括硬件工作时所需的最少功能，仅此而已。 
* **让硬件具有防篡改功能**：检测硬件物理篡改（例如打开设备护盖、拆下设备构件，等等）的内置机制。 
* **围绕安全硬件构建功能**：如果 [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) 允许，请构建安全功能，例如，安全与加密存储，以及基于受信任的平台模块 (TPM) 的引导功能。
* **安全升级**：在设备生存期内，升级固件是不可避免的事。

## <a name="iot-solution-developer"></a>IoT 解决方案开发商
如果是 IoT 解决方案开发商，请遵循以下最佳实践：

* **遵循安全软件开发方法**：开发安全软件需要从项目开始，到项目实施、测试和部署为止，随时考量安全相关事项。
* **谨慎选择开源软件**：开源软件有时可以加速解决方案的开发。
* **谨慎集成**：库和 API 的边界上存在许多软件安全漏洞。 

## <a name="iot-solution-deployer"></a>IoT 解决方案部署人员
如果是 IoT 解决方案部署人员，请遵循以下最佳实践：

* **安全部署硬件**：IoT 部署可能需要将硬件部署在不安全的位置，例如公共场所或不受监督的区域。
* **保持身份验证密钥处于安全状态**：在部署期间，每个设备需要使用云服务生成的设备 ID 和关联的身份验证密钥。 即使是在部署之后，也必须以物理方式保护这些密钥的安全。 恶意设备可能会使用任何透漏的密钥伪装成现有设备。

## <a name="iot-solution-operator"></a>IoT 解决方案操作员
如果是 IoT 解决方案操作员，请遵循以下最佳实践：

* **使系统保持最新状态**：确保设备的操作系统和所有设备驱动程序都已更新到最新版本。 
* **防御恶意活动**：如果操作系统允许，请在每个设备操作系统上加装最新的防病毒或反恶意软件功能。 
* **经常审核**：响应安全事件时，针对安全相关问题审核 IoT 基础结构是关键所在。
* **以物理方式保护 IoT 基础结构**：针对 IoT 基础结构的最严重安全攻击是通过物理访问设备的方式发起的。
* **保护云凭据**：用于配置和操作 IoT 部署的云身份验证凭据可能是访问和损坏 IoT 系统的最简单手段。 

