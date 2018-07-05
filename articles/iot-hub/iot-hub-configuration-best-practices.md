---
title: Azure IoT 中心的设备配置最佳做法 | Microsoft Docs
description: 了解有关大规模配置 IoT 设备的最佳做法
author: chrisgre
manager: briz
ms.author: chrisgre
ms.date: 06/24/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7314c5ec05bec61e5bbbc406b6a39a7c5a8f011f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034306"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>IoT 解决方案中设备配置的最佳做法

Azure IoT 中心内的自动设备管理功能可将许多复杂且重复性的任务自动化，包括在大型设备阵列的整个生命周期内对其进行管理。 本文定义在开发和操作 IoT 解决方案时涉及到的各种角色的多种最佳做法。

* **IoT 硬件制造商/集成商**：IoT 硬件的制造商、组装来自各个制造商的硬件的集成商，或针对其他供应商制造或集成的 IoT 部署提供硬件的供应商。 参与固件、嵌入式操作系统和嵌入式软件的开发与集成。
* **IoT 解决方案开发人员：** IoT 解决方案的开发通常由解决方案开发人员完成。 此开发人员可能是内部团队成员或专门从事此活动的系统集成商。 IoT 解决方案开发人员可从头开始开发 IoT 解决方案的各个组件、集成各种标准组件或开源组件，或自定义 [IoT 解决方案加速器][lnk-solution]。
* **IoT 解决方案操作员：** IoT 解决方案部署完成之后，需要长期的操作、监视、升级和维护。 这些任务可由内部团队来完成，该团队由信息技术专业人员、硬件操作和维护团队，以及负责监督整体 IoT 基础结构行为是否正常的领域专业人员组成。

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>了解大规模配置 IoT 设备时的自动设备管理

自动设备管理包括[设备孪生][lnk-device-twins]和[模块孪生][lnk-module-twins]提供的、在云与设备之间同步所需状态和报告状态的诸多好处。  [自动设备配置][lnk-auto-device-config]自动更新大量的孪生，并汇总进度与符合性。 以下概要步骤描述如何开发和使用自动设备管理：

* **IoT 硬件制造商/系统集成商**使用[设备孪生][lnk-device-twins]在嵌入式应用程序中实施设备管理功能。 这些功能可能包括固件更新、软件安装和更新，以及设置管理。
* **IoT 解决方案开发人员**使用[设备孪生][lnk-device-twins]和[自动设备配置][lnk-auto-device-config]实施设备管理操作的管理层。 该解决方案应该定义操作员界面来执行设备管理任务。
* **IoT 解决方案操作员**使用 IoT 解决方案执行设备管理任务，具体而言，包括将设备分组在一起、启动配置更改（例如固件更新）、监视进度，以及解决出现的问题。

## <a name="iot-hardware-manufacturerintegrator"></a>IoT 硬件制造商/系统集成商

下面是处理嵌入式软件开发的硬件制造商和系统集成商的最佳做法：

* **实施[设备孪生][lnk-device-twins]：** 使用设备孪生可以从云同步所需配置，以及报告当前配置和设备属性。  最好是通过 [Azure IoT SDK][lnk-azure-sdk] 在嵌入式应用程序内部实施设备孪生。  设备孪生非常适合用于配置，因为它们：a. 支持双向通信；b. 允许联网和离线设备状态；c. 遵循最终一致性的原则；d. 在云中完全可查询
* **为设备管理构建设备孪生：** 构建设备孪生的方式应该以逻辑方式将设备管理属性一起分组到节中。 这可以实现配置更改的隔离，且不影响孪生中的其他节。 例如，在所需属性中为固件创建一个节，为软件创建另一个节，并为网络设置创建第三个节。 
* **报告对设备管理有用的设备特性：** 物理设备制造商和型号、固件、操作系统、序列号和其他标识符等特性可用于报告，并可用作配置更改的目标参数。
* **为报告状态和进度定义主要状态：** 应该枚举顶级状态，以便可以向操作员报告这些状态。 例如，固件更新会将状态报告为“当前”、“正在下载”、“正在应用”、“正在进行”和“错误”。  定义其他字段，以获取有关每种状态的详细信息。  

## <a name="iot-solution-developer"></a>IoT 解决方案开发人员

下面是构建基于 Azure 的系统的 IoT 解决方案开发人员的最佳做法：

* **实施[设备孪生][lnk-device-twins]：** 使用设备孪生可以从云同步所需配置，以及报告当前配置和设备属性。  最好是通过 [Azure IoT SDK][lnk-azure-sdk] 在云解决方案应用程序内部实施设备孪生。  设备孪生非常适合用于配置，因为它们：a. 支持双向通信；b. 允许联网和离线设备状态；c. 遵循最终一致性的原则；d. 在云中完全可查询
* **使用设备孪生标记组织设备：** 解决方案应允许操作员根据不同的部署策略（例如 canary）定义质量环或其他设备集。 可以使用设备孪生标记和[查询][lnk-queries]在解决方案内部实施设备组织。  需要通过设备组织以安全准确的方式实现配置实施。
* **实施[自动设备配置][lnk-auto-device-config]：** 自动设备配置通过设备孪生在大型 IoT 设备集上部署和监视配置更改。  自动设备配置通过**目标条件**（针对设备孪生标记或报告属性执行的查询）将设备孪生集指定为目标。 **目标内容**是要在目标设备孪生内部设置的所需属性集。 目标内容应与 IoT 硬件制造商/系统集成商定义的设备孪生结构相符。  **指标**是针对设备孪生报告属性执行的查询，也应该与 IoT 硬件制造商/集成商定义的设备孪生结构相符。 自动设备配置还具有 IoT 中心的优势。IoT 中心以永不超过设备孪生读取和更新[限制][lnk-throttling]的速率执行设备孪生操作。
* **使用[设备预配服务][lnk-dps]：** 解决方案开发人员应使用设备预配服务将设备孪生标记分配到新设备，以便通过**自动设备配置**（针对具有该标记的孪生）自动配置这些设备。 

## <a name="iot-solution-operator"></a>IoT 解决方案操作员

下面是使用基于 Azure 的 IoT 解决方案的 IoT 解决方案操作员的最佳做法：

* **组织管理设备：** IoT 解决方案应该定义或者允许根据不同的部署策略（例如 canary）创建质量环或其他设备集。 设备集将用于实施配置更改，以及执行其他大规模的设备管理操作。
* **使用分阶段实施执行配置更改**：分阶段实施是指操作员将更改逐渐部署到更大范围的 IoT 设备的整个过程。 这样做的目的是逐渐进行更改，降低进行大规模重大更改的风险。  操作员应使用解决方案的界面来创建[自动设备配置][lnk-auto-device-config]，目标条件应该针对最初的设备集（例如 canary 组）。  然后，操作员应验证最初设备集中的配置更改。  完成验证后，操作员将更新自动设备配置，以包含更大的设备集。 操作员还应将配置的优先级设置为高于当前针对这些设备的配置。  可以使用自动设备配置报告的指标来监视实施。 
* **出错或配置不当时执行回滚：** 可以通过更改**目标条件**，使设备不再符合目标条件，来回滚导致出错或配置不当的自动设备配置。  确保优先级更低的另一自动设备配置仍然针对这些设备。  通过查看指标来验证回滚是否成功：回滚后的配置应该不再显示未针对的设备的状态，第二个配置的指标现在应该包含仍然针对的设备的计数。


## <a name="next-steps"></a>后续步骤

* 在[了解并在 IoT 中心内使用设备孪生][lnk-device-twins]中了解如何实施设备孪生
* 在[大规模配置和监视 IoT 设备][lnk-auto-device-config]中演练创建、更新或删除自动设备配置的步骤。
* 在[教程：实施设备固件更新过程][lnk-firmware-update]中使用设备孪生和自动设备配置实施固件更新模式。

<!-- Links -->
[lnk-device-twins]: iot-hub-devguide-device-twins.md
[lnk-module-twins]: iot-hub-devguide-module-twins.md
[lnk-auto-device-config]: iot-hub-auto-device-config.md
[lnk-firmware-update]: tutorial-firmware-update.md
[lnk-throttling]: iot-hub-devguide-quotas-throttling.md
[lnk-queries]: iot-hub-devguide-query-language.md
[lnk-dps]: ../iot-dps/how-to-manage-enrollments.md
[lnk-azure-sdk]: https://github.com/Azure/azure-iot-sdks
[lnk-solution]: https://azure.microsoft.com/features/iot-accelerators/