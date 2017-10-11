---
title: "保护你物联网 (IoT) 在 Azure 中 |Microsoft 文档"
description: " Azure 物联网 (IoT) 服务提供广泛的能力。 这篇文章可帮助你了解如何保护你在 Azure 中的 IoT 解决方案。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
ms.openlocfilehash: 3793f5453b74b6c06d9e58b426d89099298e1288
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="internet-of-things-security-overview"></a>物联网安全概述
Azure 物联网 (IoT) 服务提供广泛的能力。 这些企业级服务，您可以：

* 从设备中收集数据
* 分析流中的移动数据
* 存储和查询大型数据集
* 实现实时和历史数据可视化效果
* 与后端系统集成

若要提供这些功能，Azure IoT 套件包一起包含自定义扩展的多个 Azure 服务作为预配置解决方案。 这些预配置的解决方案的常见的帮助以减少你需要提供你的 IoT 解决方案的时间的 IoT 解决方案模式的基实现。 使用 IoT 软件开发工具包，你可以自定义并扩展这些解决方案来实现您自己的要求。 在开发新的 IoT 解决方案时，你还可以作为示例或模板中使用这些解决方案。

Azure IoT 套件是针对 IoT 需要功能强大的解决方案。 但是，它是至关重要的 IoT 解决方案旨在从一开始考虑到了安全性。 由于 IoT 设备的大量，任何安全事件很快就具有非常严重的后果的大范围的事件。

若要帮助你了解如何保护你的 IoT 解决方案，我们具有以下信息。

## <a name="security-architecture"></a>安全体系结构
设计系统时，务必了解对该系统的潜在威胁并相应地，添加适当的防御，如设计和构建系统。 请务必从一开始产品设计考虑到了安全性，因为了解如何，攻击者可能能够破坏系统有助于使确保合适的缓解措施位于从开始处的位置。

您可以了解有关 IoT 安全体系结构通过阅读[Internet 的内容安全体系结构](../iot-suite/iot-security-architecture.md)。

本文讨论以下主题：

* [安全威胁模型开头](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [IoT 中的安全性](../iot-suite/iot-security-architecture.md#security-in-iot)
* [威胁建模的 Azure IoT 参考体系结构](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>从头开始的安全
IoT 会带来到全球范围的公司的唯一安全、 隐私和合规性挑战。 与传统的网络技术，这些问题围绕软件和实现方式，不同 IoT 涉及网络和物理体系聚合时，会发生什么情况。 保护 IoT 解决方案需要确保设备，这些设备和云，以及在云中处理和存储过程的安全数据保护之间的安全连接的安全设置。 使用针对此类功能，但是，可资源有限的设备、 地理分布的部署，并且在解决方案内的多个设备。

你可以了解如何通过阅读处理这些领域内的安全[从头开始的物联网安全](../iot-suite/securing-iot-ground-up.md)。

本文还讨论了以下主题：

* [从头开始的安全基础结构](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – 对你的业务安全 IoT 基础结构](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>最佳方案
保护 IoT 基础结构需要严格的防御安全策略。 从保护数据在云中，在传输过程中的数据完整性保护通过公共 internet，安全地预配设备，每个层生成总体的基础结构中的大于安全保障。

你可以了解有关物联网安全最佳方案通过阅读[物联网安全最佳方案](../iot-suite/iot-security-best-practices.md)。

本文还讨论了以下主题：

* [IoT 硬件制造商/系统集成商](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [IoT 解决方案开发人员](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [IoT 解决方案 deployer](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [IoT 解决方案运算符](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
