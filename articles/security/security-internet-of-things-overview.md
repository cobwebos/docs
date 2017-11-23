---
title: "保护 Azure 中的物联网 (IoT) | Microsoft Docs"
description: " Azure 物联网 (IoT) 服务提供范围广泛的功能。 本文可帮助你了解如何保护 Azure 中的 IoT 解决方案。 "
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
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: baf9c81867f8dccf3897213121888d40b7472d03
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-overview"></a>物联网安全概述
Azure 物联网 (IoT) 服务提供范围广泛的功能。 这些企业级服务让你能够：

* 从设备收集数据
* 分析移动中的数据流
* 存储和查询大型数据集
* 可视化实时和历史数据
* 与后端办公系统集成

为了提供这些功能，Azure IoT 套件将多个 Azure 服务与自定义扩展打包在一起作为预配置解决方案。 预配置解决方案是常见 IoT 解决方案模式的基础实现，可帮助你减少交付 IoT 解决方案所花费的时间。 通过使用 IoT 软件开发工具包，可以自定义和扩展这些解决方案来满足自己的需求。 也可以使用这些解决方案作为开发新 IoT 解决方案时的示例或模板。

Azure IoT 套件是一个针对 IoT 需求的功能强大的解决方案。 但是，设计 IoT 解决方案时一直将安全谨记在心是至关重要的。 由于 IoT 设备的绝对数量很大，任何安全事件都有可能会迅速变成具有严重后果的大范围事件。

为了帮助你了解如何保护 IoT 解决方案，我们提供了以下信息。

## <a name="security-architecture"></a>安全体系结构
在设计系统时，我们应了解该系统的潜在威胁并添加适当的防御机制，以妥善设计系统与其体系结构。 在一开始设计产品时就先考虑安全很重要，因为了解攻击者可能如何破坏系统，有助于从一开始就准备好适当的安全防护功能。

可以通过阅读[物联网安全体系结构](../iot-suite/iot-security-architecture.md)了解有关 IoT 安全体系结构的信息。

本文讨论以下主题：

* [安全从威胁模型开始](../iot-suite/iot-security-architecture.md#security-starts-with-a-threat-model)
* [IoT 中的安全性](../iot-suite/iot-security-architecture.md#security-in-iot)
* [对 Azure IoT 参考体系结构进行威胁建模](../iot-suite/iot-security-architecture.md#threat-modeling-the-azure-iot-reference-architecture)

## <a name="security-from-the-ground-up"></a>安全基础知识
IoT 使全球企业面临独特的安全、隐私权与合规性挑战。 不同于传统网络技术（这些问题是以软件及其实现方式为中心），IoT 在意的是当网络与物理世界融合时会发生什么情况。 保护 IoT 解决方案要求确保安全预配设备，保护这些设备与云之间的连接，以及在处理和存储期间保护云中数据的安全。 但是，针对此类功能运行的是资源受限的设备、根据地理位置分布的部署，以及解决方案中的许多设备。

可以通过阅读[物联网安全基础知识](../iot-suite/securing-iot-ground-up.md)了解如何处理这些领域内的安全问题。

本文讨论以下主题：

* [安全基础结构基础知识](../iot-suite/securing-iot-ground-up.md#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure - 适用于企业的安全 IoT 基础结构](../iot-suite/securing-iot-ground-up.md#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>最佳实践
保护 IoT 基础结构需要严格的深度安全防御策略。 从保护云数据开始，到保护数据通过公共 Internet 传输时的完整性，再到安全地预配设备，整体基础结构的每一层一步步奠定了强大的安全保障。

可以通过阅读[物联网安全最佳实践](../iot-suite/iot-security-best-practices.md)了解有关物联网安全最佳实践的信息。

本文讨论以下主题：

* [IoT 硬件制造商/系统集成商](../iot-suite/iot-security-best-practices.md#iot-hardware-manufacturerintegrator)
* [IoT 解决方案开发人员](../iot-suite/iot-security-best-practices.md#iot-solution-developer)
* [IoT 解决方案部署人员](../iot-suite/iot-security-best-practices.md#iot-solution-deployer)
* [IoT 解决方案操作员](../iot-suite/iot-security-best-practices.md#iot-solution-operator)
