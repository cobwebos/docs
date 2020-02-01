---
title: 什么是 Azure FarmBeats
description: 概述 Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767968"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Azure FarmBeats（预览版）概述

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品/服务。 它允许跨提供商聚合农业数据集。 借助 Azure FarmBeats，你可以基于融合的数据集构建人工智能 (AI) 或机器学习 (ML) 模型。 通过使用 Azure FarmBeats，农业企业可以将精力集中在核心价值而非无差别的数据工程工作负荷上。

> [!NOTE]
> Azure FarmBeats 目前提供公开预览版。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 服务级别协议未随 Azure FarmBeats 一起提供。 使用 [Azure FarmBeats 论坛](https://aka.ms/FarmBeatsMSDN )可获得支持。

![FarmBeats 项目](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Azure FarmBeats 预览版的功能包括：

- 使用基于卫星图像的植被覆盖指数和降水指数评估农场健康状况。
- 获取有关要使用的土壤湿度传感器数量以及放置位置的建议。
- 通过可视化由各种供应商提供的传感器所收集的地面数据来跟踪农场状况。
- 根据合成的卫星和传感器数据，获得土壤湿度图。
- 根据聚合数据集构建 AI/ML 模型，以此获取可操作见解。
- 通过提供农场健康咨询来构建或补充数字农业解决方案。

## <a name="datahub"></a>数据中心

Azure FarmBeats 数据中心一个 API 层，可以跨提供商对各种农业数据集进行聚合、规范化和语境化。 可以使用 Azure FarmBeats 获取以下内容：
- 三个传感器提供商（[Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/)、[Teralytic](https://teralytic.com/)、[Pessl Instruments](https://metos.at/)）提供的**传感器数据**
- 欧洲空间代理商的 [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) 卫星任务提供的**卫星影像**
- 三家无人机图像提供商（[senseFly](https://www.sensefly.com/)、[SlantRange](https://slantrange.com/)、[DJI](https://dji.com/)）提供的**无人机图像**

数据中心被设计为可扩展的 API 平台。 我们将与更多提供商合作以与 Azure FarmBeats 集成，因此在构建解决方案时你拥有更多选择。

## <a name="accelerator"></a>加速器

Azure FarmBeats 加速器是一个基于数据中心的示例 Web 应用程序。 此加速器可快速启动用户界面和模型开发。 Azure FarmBeats 加速器使用 Azure FarmBeats 的 API。 它将引入的传感器数据直观显示为图表，将输出建模为地图。 例如，可以使用加速器快速创建农场，并轻松获取该农场的植被覆盖指数图或传感器放置图。

## <a name="resources"></a>资源

Azure FarmBeats 免费提供，你只需为所使用的 Azure 资源付费。 可以使用以下资源详细了解此产品/服务：

- 访问我们的 [Azure FarmBeats 博客](https://aka.ms/farmbeatsblog)，随时了解最新的 Azure FarmBeats 新闻。
- 在 [Azure FarmBeats 支持论坛](https://aka.ms/farmbeatssupport)上发布问题，通过这种方式寻求帮助。
- 在我们的 [Azure FarmBeats 反馈论坛](https://aka.ms/farmbeatsfeedback)上发布某种功能想法或投票支持该想法，通过这种方式提供反馈。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [安装 Azure FarmBeats](install-azure-farmbeats.md)
