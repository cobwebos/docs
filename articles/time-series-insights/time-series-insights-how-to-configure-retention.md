---
title: 如何在环境中配置保留期 - Azure 时序见解 | Microsoft Docs
description: 了解如何在 Azure 时序见解环境中配置保留期。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569411"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>在 Azure 时序见解第 1 代中配置保留期

> [!CAUTION]
> 这是一篇 Gen1 文章。

本文介绍如何在 Azure 时序见解中配置**数据保留时间**和**超出存储限制时的行为**。

## <a name="summary"></a>总结

每个 Azure 时序见解环境都有用于配置**数据保留时间**的设置。 该值的范围为 1 到 400 天。 将根据环境存储容量或保留期限 (1-400) 删除数据，以先达到的条件为准。

每个 Azure 时序见解环境都有一项附加设置：“超出存储限制时的行为”。 此设置控制达到环境最大容量时的传入和清除行为。 可以从两种行为中进行选择：

-  清除旧数据（默认行为）
- 暂停传入 

要通过详细信息更好地了解这些设置，请参阅[了解 Azure 时序见解中的保留期](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>配置数据保留

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 查找现有的 Azure 时序见解环境。 在 Azure 门户左侧的菜单中，选择“所有资源”  。 选择 Azure 时序见解环境。

1. 在“设置”标题下，选择“存储配置”   。

    [![在“设置”下，选择“存储配置”](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. 选择“数据保留时间(天)”  ，以通过使用滚动条或在文本框中键入数字来配置保留期。

1. 请注意“容量”  设置，因为此配置会影响数据事件的最大数量和用于存储数据的总存储容量。

1. 切换“超出存储限制时的行为”  设置。 选择“清除旧数据”  或“暂停传入”  行为。

    [![暂停传入 - 接受并保存。](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. 查看文档以了解丢失数据的潜在风险。 选择“保存”  以配置更改。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[了解 Azure 时序见解中的保留期](time-series-insights-concepts-retention.md)。

- 了解[如何缩放 Azure 时序见解环境](time-series-insights-how-to-scale-your-environment.md)。

- 了解如何[规划环境](time-series-insights-environment-planning.md)。
