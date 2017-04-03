---
title: "预测车辆的运行状况和行驶习惯 - Azure | Microsoft Docs"
description: "通过 Cortana Intelligence 的功能获得对车辆运行状况和驾驶习惯的实时和预测性深入了解。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 3467c5549381f1354987fead424646afe847739c
ms.lasthandoff: 01/24/2017


---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>车辆遥测分析解决方案操作手册
此“菜单”链接至此操作手册的此章节。 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>概述
超级电脑已经搬出了实验室，现在停在我们的车库中！ 这些先进的汽车包含无数的传感器，让它们能够每秒跟踪和监控数百万的事件。 我们预计到 2020 年，这些汽车中的大部分将连接到 Internet。 想象挖掘这些丰富的数据，从而提供更高的安全性、可靠性和更好的驾驶体验！ Microsoft 通过 Cortana Intelligence 使得这个美梦成真。

Microsoft 的 Cortana Intelligence 是一个完全托管的大数据和高级分析套件，使你得以将数据转换为智能操作。 我们要向您介绍 Cortana Intelligence 车辆遥测分析解决方案模板。 此解决方案演示了汽车经销商、汽车制造商和保险公司如何使用Cortana Intelligence 的功能获得车辆运行状况和驾驶习惯的实时和预测性深入了解。 

该解决方案的实现为 [lambda architecture pattern](https://en.wikipedia.org/wiki/Lambda_architecture)（lambda 体系结构模式），显示了 Cortana Intelligence 平台在实时和批处理方面的全部潜力。 解决方案： 

* 提供车辆远程信息处理模拟器
* 利用事件中心将数百万模拟车辆遥测事件引入 Azure 
* 使用流分析获得车辆运行状况的实时深入了解
* 将数据保存到长期存储中，以实现更丰富的批处理分析。 
* 利用机器学习对异常情况检测进行实时和批处理，以获得预测性的深入了解。
* 利用 HDInsight 打量转换数据，利用数据工厂处理批处理管道的业务流程、计划、资源管理和监视 
* 使用 Power BI 为此解决方案提供了丰富的仪表板，用于实时数据和预测分析可视化

## <a name="architecture"></a>体系结构
![解决方案体系结构图](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*图 1 - 车辆遥测分析解决方案体系结构*

此解决方案包括以下 **Cortana Intelligence 组件**并展示了它们的端到端集成：

* **事件中心**，用于将数百万车辆遥测事件引入 Azure。
* **流分析**，用于获得对车辆运行状况的实时深入了解，并将数据持续存储到长期存储中，以进行更丰富的批处理分析。
* **机器学习**，用于实时和批处理的异常情况检测，以获得预测性的深入了解。
* **HDInsight**，用于大规模转换数据
* ，用于处理批处理管道的业务流程、计划、资源管理和监视。
* **Power BI **为该解决方案提供了丰富的仪表板，用于实时数据和预测分析可视化。

该解决方案访问两个不同的**数据源**： 

* **模拟车辆信号和诊断**：车辆远程信息处理模拟器在给定时间点发出对应于车辆的状态和驾驶模式的诊断信息和信号。 
* **车辆目录**：是包含 VIN 到模型映射的引用数据集。


