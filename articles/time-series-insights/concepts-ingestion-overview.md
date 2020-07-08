---
title: 引入概述-Azure 时序见解 |Microsoft Docs
description: 了解如何将数据引入到 Azure 时序见解。
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049779"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Azure 时序见解数据引入概述

你的 Azure 时序见解环境包含一个*引入引擎*，用于收集、处理和存储流时序数据。 当数据到达你的事件源时，Azure 时序见解会以近乎实时的方式使用和存储你的数据。

[![引入概述](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>引入主题

以下文章深入介绍了数据处理，包括要遵循的最佳做法：

* 阅读有关[事件源](concepts-streaming-ingestion-event-sources.md)的信息，以及有关选择事件源时间戳的指导。

* 查看支持的[数据类型](concepts-supported-data-types.md)

* 了解引入引擎如何将一组[规则](./concepts-json-flattening-escaping-rules.md)应用到 JSON 属性，以创建存储帐户列。

* 查看环境[吞吐量限制](concepts-streaming-throughput-limitations.md)以规划规模需求。

## <a name="next-steps"></a>后续步骤

* 继续了解有关 Azure 时序见解环境的[事件源](concepts-streaming-ingestion-event-sources.md)的详细信息。 
