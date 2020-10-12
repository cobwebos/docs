---
title: Azure FarmBeats 体系结构
description: 介绍 Azure FarmBeats 的体系结构
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75482369"
---
# <a name="integration-patterns"></a>集成模式

Azure FarmBeats 是一款可在 Azure Marketplace 中使用的企业到企业产品。 FarmBeats 可通过将数据集组装，通过组装智能 (AI) 或机器学习 (ML) 模型来跨提供商聚合和生成可操作的见解。

![FarmBeats 项目](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

以下部分介绍了 Azure FarmBeats 的集成模式。

## <a name="why-integrate-with-azure-farmbeats"></a>为什么要集成 Azure FarmBeats？

本部分重点介绍想要将其数据系统 (如传感器、无人机、天气电台) 集成到 Azure FarmBeats 中的合作伙伴。

Azure FarmBeats 是一个可扩展的产品/服务，可让农业企业将其不同的历史和实时农业数据集添加到单个平台。 Azure FarmBeats 可帮助农业企业在场的上下文中对其数据进行标准化、contextualize 和聚合。

通过使数据伙伴成为 Azure FarmBeats，你可以将系统扩大到广泛采用，并与你的数据产品提供更多的客户。 Azure FarmBeats 提供了一个名为 Datahub 的可扩展 API 层，可帮助你将设备中的数据系统地引入标准架构。

在客户的 Azure FarmBeats 实例中提供数据后，你的客户可以基于你的数据生成更丰富的分析和工具。

## <a name="next-steps"></a>后续步骤

有关传感器数据集成的详细信息，请参阅[传感器数据集成](sensor-partner-integration-in-azure-farmbeats.md)和图像合作伙伴[集成。](imagery-partner-integration-in-azure-farmbeats.md)
