---
title: Azure FarmBeats 概述
description: 概述 Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797153"
---
# <a name="overview-of-azure-farmbeats"></a>Azure FarmBeats 概述

Azure FarmBeats 是 Azure 服务和功能的集合，旨在帮助你快速构建由数据驱动的农业智能解决方案。 Azure FarmBeats 是一款 Azure 市场产品/服务，使用它，你可以获取、聚合和处理来自各种来源（例如传感器、无人机、照相机和卫星）的农业相关数据，无需投资大量的深度数据工程资源。

> [!NOTE]
> Azure FarmBeats 目前提供公开预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 服务级别协议未随 Azure FarmBeats 一起提供。 使用 [Azure FarmBeats 论坛](https://aka.ms/FarmBeatsMSDN )可获得支持。

使用 Azure FarmBeats，你将能够从传感器、卫星、无人机等各种来源获取数据，所有这些都是在农场（感兴趣的地理区域）的背景下进行的。

你将能够：

- 聚合来自不同提供商的农业数据集
- 通过融合不同的数据集，快速构建人工智能/机器语言 (AI/ML) 模型

Azure FarmBeats 提供了一种强大而简单的方法来执行以下操作：

- 使用 GeoJSON 文件为农场创建地图。
- 使用基于卫星图像的植被覆盖指数和降水指数评估农场健康状况。
- 获取有关要使用的传感器数量以及放置位置的建议。
- 通过可视化由各种传感器供应商提供的传感器所收集的地面数据来跟踪农场状况。
- 根据合成的卫星和传感器数据，获得土壤湿度图。
- 根据聚合数据集构建 AI/ML 模型，以此获取可操作见解。
- 通过提供农场健康咨询来构建或补充数字农业解决方案。

本文的以下各节中讨论了 Azure FarmBeat 组件。

## <a name="data-hub"></a>数据中心

一个 API 层，可以跨提供商对各种农业数据集进行聚合、规范化和语境化。 在本预览版中，可以使用两个传感器提供商（[Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/)、[Teralytic](https://teralytic.com/)），一个卫星图像提供商（[Sentinel-2](https://sentinel.esa.int/web/sentinel/home)）和两个无人机图像提供商（[senseFly](https://www.sensefly.com/)、[SlantRange](https://slantrange.com/)）。 数据中心被设计为 API 平台，我们将与更多提供商合作以与 Azure FarmBeats 集成，因此在构建解决方案时你拥有更多选择。

## <a name="accelerator"></a>加速器

一个基于数据中心的示例解决方案，可快速启动用户界面和模型开发。 该 Web 应用程序使用 API 以图表的形式展示引入的传感器数据的可视化效果，并以地图的形式展示模型输出的可视化效果。 例如可以使用加速器快速创建农场，并轻松获取该农场的植被覆盖指数图或传感器放置图。

## <a name="resources"></a>资源

请访问 FarmBeats [博客](https://aka.ms/AzureFarmBeats)和[论坛](https://aka.ms/FarmBeatsMSDN)。

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure FarmBeats，请访问 [Azure 市场](https://aka.ms/FarmBeatsMarketplace)进行部署。
