---
title: 远程监视解决方案加速器常见问题解答 | Microsoft Docs
description: 远程监视解决方案加速器的常见问题解答
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: d1cc260710d025428a1ca77c41c104dc172447e6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>远程监视解决方案加速器的常见问题解答

请同时参阅一般的[常见问题解答](iot-suite-faq.md)。

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>预配新的远程监视解决方案的费用是多少？

新的解决方案加速器提供两个部署选项：

* 基本选项适用于寻求更低开发成本的开发人员，或想要生成演示或概念证明的客户。
* 标准选项适用于想要部署生产就绪基础结构的企业。

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>如何确保在开发解决方案的同时不断降低成本？

除了提供两种不同的部署以外，新的远程监视解决方案还提供一项设置来按需启用或禁用所有模拟设备。 禁用模拟可以减少解决方案中引入的数据，因此可以降低总体成本。

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>基本和标准部署选项有什么差别？ 如何在这两个部署选项之间做出选择？

每个部署选项对应于不同的需求。 基本部署旨在满足入门需要以及开发 PoC 和小型试验。 它提供一个简化的体系结构，包含最少量的必要资源，并且费用较低。 标准部署旨在生成和自定义生产就绪的解决方案，为实现部署提供所需的元素。 在可靠性和缩放，微服务应用程序构建为 Docker 容器，并使用某个业务流程协调程序（默认为 Kubernetes）进行部署。 该业务流程协调程序负责部署、缩放和管理应用程序。 应根据当前需求选择其中一个选项。 可以根据项目所处的阶段，使用其中的一个选项或两者的组合。

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>如何在仪表板上配置动态映射？

有关详细信息，请参阅 [Upgrade map key to see devices on a dynamic map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)（升级映射键，以查看动态映射上的设备）。

### <a name="next-steps"></a>后续步骤

还可以浏览 IoT 解决方案加速器的一些其他特性和功能：

* [探索远程监视解决方案加速器的功能](iot-suite-remote-monitoring-explore.md)
* [预测性维护解决方案加速器概述](iot-suite-predictive-overview.md)
* [连接的工厂解决方案加速器概述](iot-suite-connected-factory-overview.md)
* [从头保障 IoT 的安全](securing-iot-ground-up.md)