---
title: Azure 网络往返延迟统计信息 |微软文档
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886937"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监控工具以及由第三方综合监控服务[千美希望](https://thousandeyes.com)收集的测量值持续监控其网络核心区域的延迟（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集测量值？

延迟测量是从千眼代理收集的，这些代理托管在全球 Azure 云区域中，这些代理每隔 1 分钟一次在它们之间连续发送网络探测。 每月延迟统计信息派生自该月收集的样本的平均值。

## <a name="march-2020-round-trip-latency-figures"></a>2020年3月往返延迟数据

Azure 区域过去 31 天（2020 年 3 月 31 日结束）之间的每月平均往返时间如下所示。 以下测量由[千眼](https://thousandeyes.com)驱动。

[![Azure 区域间延迟统计信息](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
