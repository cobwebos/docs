---
title: Azure FarmBeats 体系结构
description: 介绍 Azure FarmBeats 的体系结构
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8eaba4c2a987f47c56169af138ef380835d56740
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797980"
---
# <a name="integration-patterns"></a>集成模式

Azure FarmBeats 是一款可在 Azure Marketplace 中使用的企业到企业产品。 FarmBeats 通过融合数据集，通过构建人工智能（AI）或机器学习（ML）模型，实现跨提供商的可再组装数据集，并生成可操作的见解。

![项目场节拍](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

以下部分介绍了 Azure FarmBeats 的集成模式。

## <a name="why-integrate-with-azure-farmbeats"></a>为什么要集成 Azure FarmBeats？

本部分重点介绍想要将其数据系统（如传感器、无人机、天气工作站）集成到 Azure FarmBeats 的合作伙伴。

Azure FarmBeats 是一个可扩展的产品/服务，可让农业企业将其不同的历史和实时农业数据集添加到单个平台。 Azure FarmBeats 可帮助农业企业在场的上下文中对其数据进行标准化、contextualize 和聚合。

通过使数据伙伴成为 Azure FarmBeats，你可以将系统扩大到广泛采用，并与你的数据产品提供更多的客户。 Azure FarmBeats 提供了一个名为 "数据中心" 的可扩展 API 层，可帮助你将设备中的数据系统地引入标准架构。

在客户的 Azure FarmBeats 实例中提供数据后，你的客户可以基于你的数据生成更丰富的分析和工具。

## <a name="next-steps"></a>后续步骤

有关传感器数据集成的详细信息，请参阅[传感器数据集成](sensor-partner-integration.md)和图像合作伙伴[集成。](imagery-partner-integration.md)
