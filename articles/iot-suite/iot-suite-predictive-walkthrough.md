---
title: "预见性维护演练 | Microsoft Docs"
description: "Azure IoT 预见性维护预配置解决方案演练。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3c48a716-b805-4c99-8177-414cc4bec3de
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: a68a8fdc3976ade0d1036d5ed58c8b2eb6d32a5d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="predictive-maintenance-preconfigured-solution-walkthrough"></a>预见性维护预配置解决方案演练

预测性维护预配置解决方案是一个用于商业应用场景的端到端解决方案，可预测可能发生故障的时间点。 可以主动对优化维护等活动运用此预配置解决方案。 解决方案结合了关键的 Azure IoT 套件服务，包括 IoT 中心、流分析和 [Azure 机器学习][lnk-machine-learning]工作区。 此工作区包含基于公用示例数据集的模型，用于预测飞机引擎的剩余使用寿命 (RUL)。 此解决方案全面实施了完整的 loT 商业应用场景作为规划和实施解决方案的起点，以满足自己特定的业务需求。

## <a name="logical-architecture"></a>逻辑体系结构

下图概述该预配置解决方案的逻辑组件：

![][img-architecture]

蓝色项是在部署预配置解决方案时，在同一区域中预配的 Azure 服务。 [预配页][lnk-azureiotsuite]显示了可部署预配置解决方案的区域列表。

绿色项是表示飞机引擎的模拟设备。 可以在以下部分中了解有关这些模拟设备的详细信息。

灰色项表示实现*设备管理*功能的组件。 当前的预见性维护预配置解决方案版本不会预配这些资源。 若要了解有关设备管理的详细信息，请参阅[远程监视预配置解决方案][lnk-remote-monitoring]。

## <a name="simulated-devices"></a>模拟设备

在该预配置解决方案中，模拟设备代表飞机引擎。 该解决方案预配了 2 个映射到单架飞机的引擎。 每个引擎发出 4 种遥测数据：传感器 9、传感器 11、传感器 14 和传感器 15 为机器学习模型提供所需的数据来计算引擎的 RUL。 每个模拟设备会将下列遥测消息发送到 IoT 中心：

*周期计数*。 一个周期表示 2 至 10 小时不等的已完成飞行。 在飞行期间，每半小时捕获一次遥测数据。

*遥测*。 有 4 个代表引擎属性的传感器。 这些传感器一般标记为传感器 9、传感器 11、传感器 14 和传感器 15。 这四个传感器代表足以从 RUL 模型获取有用结果的遥测装置。 预配置解决方案中使用模型是基于包含实际引擎传感器数据的公共数据集创建的。 有关如何根据原始数据集创建该模型的详细信息，请参阅 [Cortana Intelligence 库预见性维护模板][lnk-cortana-analytics]。

模拟设备可以处理在解决方案中通过 IoT 中心发送的以下命令：

| 命令 | 说明 |
| --- | --- |
| StartTelemetry |控制模拟的状态。<br/>使设备开始发送遥测 |
| StopTelemetry |控制模拟的状态。<br/>使设备停止发送遥测 |

IoT 中心会提供设备命令确认。

## <a name="azure-stream-analytics-job"></a>Azure 流分析作业

作业：遥测会使用两个语句来操作传入设备遥测流：

* 第一个语句会从设备选择所有遥测，然后将这些数据 从在 Web 应用中可视化的位置发送到 Blob 存储。
* 第二个语句会通过两分钟的滑动窗口计算平均传感器值，然后通过事件中心将这些数据发送到事件处理器。

## <a name="event-processor"></a>事件处理器
**事件处理器主机**在 Azure Web 作业中运行。 **事件处理器** 为已完成的周期获取平均传感器值。 然后，将这些值传递给某个 API，后者可公开用于计算引擎 RUL 的训练模型。 该 API 由预配为解决方案一部分的机器学习工作区公开。

## <a name="machine-learning"></a>机器学习
机器学习组件使用派生自数据的模型，这些数据是从实际飞机引擎收集的。 可以导航到预配解决方案 [azureiotsuite.com][lnk-azureiotsuite] 页面的磁贴中的机器学习工作区。 当解决方案处于“就绪”状态时，会提供一个磁贴。


## <a name="next-steps"></a>后续步骤
了解预见性维护预配置解决方案的关键组件后，可对其进行自定义。 请参阅[预配置解决方案自定义指南][lnk-customize]。

还可以浏览 IoT 套件预配置的解决方案的一些其他特性和功能：

* [有关 IoT 套件的常见问题][lnk-faq]
* [从源头保障 IoT 的安全][lnk-security-groundup]

[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png

[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
