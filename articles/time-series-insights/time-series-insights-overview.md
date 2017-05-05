---
title: "Azure 时序见解概述 | Microsoft Docs"
description: "Azure 时序见解简介：一项用于时序数据分析和 IoT 解决方案的新服务"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Azure 时序见解概述

Azure 时序见解是一项完全托管的云服务，带有存储、分析和可视化效果组件，可以很轻松地同时进行数十亿事件的引入、存储、浏览和分析操作。 时序见解帮助发现隐藏的趋势和异常，并近实时地进行根因分析，从而为你提供数据的全局视图，让你能够快速验证 IoT 解决方案，并避免关键设备昂贵的停机时间。 时序见解根据可配置的保留策略从事件代理（例如 IoT 中心或事件中心）引入时序数据，为数据建立索引，然后重试数据。 用户通过直观的 UX 或 REST 查询 API 将数据用于业务方案。

![时序见解概述](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>主要方案

* 在数分钟内验证和监视 IoT 解决方案
* 直观的可视化效果和大规模 IoT 数据分析
* 方便根本原因分析和异常检测
* 创建多个设备、工厂和数据的全局视图

## <a name="key-capabilities-and-benefits"></a>主要功能和优点

* **轻松入门**：Azure 时序见解不需前期数据准备，速度极快，只需数分钟即可在 Azure IoT 中心连接到数十亿事件。 一旦连接，即可在几秒钟内可视化传感器数据并与之进行交互，从而快速验证 IoT 解决方案。 时序见解相当直观且易于使用；不需编写一行代码即可与数据交互。  此外，也不需要学习新语言，因为时序见解既为高级用户提供精细的自定义文本查询图面，又为新手提供点击浏览体验。

* **快速提供近实时见解**：通过时序见解的存储、分析和可视化效果组件，集中利用时序数据。 时序见解每天可以引入数亿传感器事件，且延迟极小，方便你快速应对各种变化。 你可以通过时序见解快速洞察各种趋势和异常，对传感器数据进行深入分析，从而轻松执行复杂的根本原因分析，避免代价高昂的停机。 时序见解可以在实时数据和历史数据之间进行交叉关联，为用户发现数据中隐藏的趋势。

* **生成自定义解决方案**：将 Azure 时序见解数据嵌入现有应用程序，或通过时序见解 REST API 创建新的自定义解决方案。  另外，创建和共享个性化视图也很简单，他人可以轻松地浏览你的发现成果。

* **可伸缩性**：时序见解旨在支持 IoT 缩放。 预览版的时序见解每天可以引入 1 百万到 1 亿事件，默认保留时间为 31 天。 时序见解允许用户以近实时方式可视化和分析实时数据流以及大量的历史数据。 往前看，引入率和保留率会提高，以便应对不断增加的企业规模。

## <a name="taxonomy-of-time-series-insights"></a>时序见解分类

* **环境**：环境是一种 Azure 资源，具有入口容量和存储容量。  客户通过 Azure 门户使用所需容量对环境进行预配。
* **事件源**：事件源派生自事件代理（例如 Azure 事件中心）。  时序见解直接连接到事件源，不需用户编写一行代码即可引入数据流。 时序见解目前支持 Azure 事件中心和 Azure IoT 中心，以后会添加更多的事件源。
* **引用数据**：时序见解允许用户联接时序数据和引用数据。  引用数据可能包括有关设备的元数据，或者其他更改频率相对缓慢的静态数据。 时序见解联接引用数据和数据流，让用户以近实时方式可视化和分析该数据。

