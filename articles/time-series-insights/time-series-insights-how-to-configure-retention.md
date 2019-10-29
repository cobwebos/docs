---
title: 如何在 Azure 时序见解环境中配置保留期 | Microsoft Docs
description: 本文介绍如何在 Azure 时序见解环境中配置保留期。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: ff4d326af691ae27894dc94d7581ba68951f090e
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990061"
---
# <a name="configuring-retention-in-time-series-insights"></a>在时序见解中配置保留期

本文介绍如何在 Azure 时序见解中配置**数据保留时间**和**超出存储限制时的行为**。

## <a name="summary"></a>总结

每个 Azure 时序见解环境都有一个设置来配置**数据保留时间**。 该值的范围为 1 到 400 天。 将根据环境存储容量或保留期限 (1-400) 删除数据，以先达到的条件为准。

每个时序见解环境都有一个额外的 "**存储限制超出" 行为**。 此设置控制达到环境最大容量时的传入和清除行为。 可以从两种行为中进行选择：

- 清除旧数据（默认行为）
- 暂停传入

有关有助于更好地了解这些设置的详细信息，请参阅[了解时序见解中的保留期](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>配置数据保留

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 查找现有时序见解环境。 在 Azure 门户左侧的菜单中，选择“所有资源”。 选择时序见解环境。

1. 在“设置”标题下，选择“配置”。

    [![选择设置，然后配置](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. 选择 "**数据保留时间（天）** "，使用滚动条或在文本框中键入一个数字来配置保留期。

1. 请注意“容量”设置，因为此配置会影响数据事件的最大数量和用于存储数据的总存储容量。

1. 切换“超出存储限制时的行为”设置。 选择“清除旧数据”或“暂停传入”行为。

    [![数据保留-接受并保存。](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. 接受此复选框，指出您已经查看了文档并了解丢失数据的潜在风险。 选择“保存”以配置更改。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[了解时序见解中的保留期](time-series-insights-concepts-retention.md)。

- 了解[如何扩展时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

- 了解如何[规划您的环境](time-series-insights-environment-planning.md)。