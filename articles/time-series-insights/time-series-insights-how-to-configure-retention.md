---
title: 如何在 Azure 时序见解环境中配置保留期 | Microsoft Docs
description: 本文介绍如何在 Azure 时序见解环境中配置保留期。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f5f34983d1818679450249aa40bbb325e743cc57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239003"
---
# <a name="configuring-retention-in-time-series-insights"></a>在时序见解中配置保留期

本文介绍如何在 Azure 时序见解中配置**数据保留时间**和**超出存储限制时的行为**。

## <a name="summary"></a>摘要

每个时序见解 (TSI) 环境都有用于配置**数据保留时间**的设置。 该值的范围为 1 到 400 天。 将根据环境存储容量或保留期限 (1-400) 删除数据，以先达到的条件为准。

每个 TSI 环境都有一项附加设置：“超出存储限制时的行为”。  此设置控制达到环境最大容量时的传入和清除行为。 可以从两种行为中进行选择：

-  清除旧数据（默认行为）
- **暂停传入**

有关有助于更好地了解这些设置的详细信息，请参阅[了解时序见解中的保留期](time-series-insights-concepts-retention.md)。  

## <a name="configure-data-retention"></a>配置数据保留

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 查找现有时序见解环境。 在 Azure 门户左侧的菜单中，选择“所有资源”  。 选择时序见解环境。

1. 在“设置”标题下，选择“配置”   。

1. 选择“数据保留时间”  ，以通过使用滚动条或在文本框中键入数字来配置保留期。

1. 请注意“容量”  设置，因为此配置会影响数据事件的最大数量和用于存储数据的总存储容量。

1. 切换“超出存储限制时的行为”  设置。 选择“清除旧数据”  或“暂停传入”  行为。

1. 选择“保存”  以配置更改。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请参阅[了解时序见解中的保留期](time-series-insights-concepts-retention.md)。