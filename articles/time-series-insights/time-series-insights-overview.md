---
title: "Azure 时序见解概述 | Microsoft Docs"
description: "Azure 时序见解简介：一项用于时序数据分析和 IoT 解决方案的新服务"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: abd66208ab7ac30831f3f1eddb2891ed7bcd3995
ms.contentlocale: zh-cn
ms.lasthandoff: 07/21/2017

---

# <a name="what-is-azure-time-series-insights"></a>什么是 Azure 时序见解

Azure 时序见解是一项托管的云服务，带有存储、分析和可视化效果组件，可以轻松地同时进行数十亿事件的引入、存储、浏览和分析操作。 时序见解有助于发现隐藏的趋势和异常，并能够近实时地进行根因分析，为你提供数据的全局视图，让你能够快速验证 IoT 解决方案，避免因设备停机而造成费用损失。 时序见解根据可配置的保留策略从事件代理（例如 IoT 中心或事件中心）引入时序数据，为数据建立索引，然后重试数据。 用户通过直观的 UX 或 REST 查询 API 来使用数据。

![时序见解概述](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>主要方案

* 快速监视和验证 IoT 解决方案。
* 大规模可视化和分析 IoT 数据。
* 方便根因分析和异常检测。
* 创建多个设备、工厂和数据的全局视图。

## <a name="capabilities-and-benefits"></a>功能和优点

* 易于入门：Azure 时序见解不需前期数据准备，速度极快。 只需数分钟即可连接到 Azure IoT 中心或事件中心的数十亿事件。 一旦连接，即可在几秒钟内可视化传感器数据并与之进行交互，从而快速验证 IoT 解决方案。 时序见解易于使用；不需编写一行代码即可与数据交互。  不需要学习新语言；时序见解既为高级用户提供精细的自定义文本查询图面，又为所有用户提供点击浏览体验。

* 近实时见解：时序见解每天可以引入数亿传感器事件，且延迟极小，方便你快速应对各种变化。 可以通过时序见解快速洞察各种趋势和异常，对传感器数据进行深入分析，并执行复杂的根因分析，避免代价高昂的停机。 时序见解可以在实时数据和历史数据之间进行交叉关联，有助于发现数据中隐藏的趋势。

* **生成自定义解决方案**：将 Azure 时序见解数据嵌入现有应用程序，或通过时序见解 REST API 创建新的自定义解决方案。 创建和共享个性化视图，以便通过这种共享方便他人浏览你的发现。

* 可伸缩性：时序见解旨在支持大规模 IoT。 预览版的时序见解每天可以引入 1 百万到 1 亿事件，默认保留时间为 31 天。 可以通过近实时方式可视化和分析实时数据流以及大量的历史数据。 往前看，引入率和保留率会提高，以便应对不断增加的企业规模。

## <a name="time-series-insights-glossary"></a>时序见解术语表

* **环境**：环境是一种 Azure 资源，具有入口容量和存储容量。  客户通过 Azure 门户使用所需容量对环境进行预配。
* **事件源**：事件源派生自事件代理（例如 Azure 事件中心）。  时序见解直接连接到事件源，不需编写任何代码即可引入数据流。 时序见解目前支持 Azure 事件中心和 Azure IoT 中心。
* **引用数据**：时序见解允许用户联接时序数据和引用数据。  引用数据可能包括有关设备的元数据，或者其他更改频率相对缓慢的静态数据。 时序见解联接引用数据和数据流，让用户以近实时方式可视化和分析该数据。

