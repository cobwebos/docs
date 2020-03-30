---
title: Azure 网络往返延迟统计信息 |微软文档
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082926"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监控工具以及由第三方综合监控服务[千美希望](https://thousandeyes.com)收集的测量值持续监控其网络核心区域的延迟（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集测量值？

延迟测量是从千眼代理收集的，这些代理托管在全球 Azure 云区域中，这些代理每隔 1 分钟一次在它们之间连续发送网络探测。 每月延迟统计信息派生自该月收集的样本的平均值。

## <a name="february-2020-round-trip-latency-figures"></a>2020 年 2 月往返延迟数据

Azure 区域过去 29 天（2020 年 2 月 29 日结束）之间的每月平均往返时间如下所示。 以下测量由[千眼](https://thousandeyes.com)驱动。

[![Azure 区域间延迟统计信息](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
