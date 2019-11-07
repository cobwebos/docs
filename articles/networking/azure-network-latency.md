---
title: Azure 网络往返延迟统计信息 |Microsoft Docs
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587584"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监视工具和第三方合成监视服务[ThousandEyes](https://thousandeyes.com)收集的度量值来持续监视其网络核心区域的延迟（速度）。

## <a name="how-are-the-measurements-collected"></a>度量值的收集方式是什么？

延迟度量是从全球 Azure 云区域中托管的 ThousandEyes 代理收集的，它在1分钟的时间间隔内持续发送网络探测。 每月滞后时间统计信息派生自每月收集的样本数。

## <a name="october-2019-latency-figures"></a>10月2019延迟图形

对于在2019年10月31日结束的31天，聚合区域内的每月最小和最大双程延迟时间为：

- 对于**北美**区域内的往返行程，为**5 ms**到**72 毫秒**。
- 对于**欧洲**区域内的往返行程，为**3 ms**到**28 毫秒**。
- 对于**亚洲**区域内的往返行程， **4 毫秒**到**134 毫秒**。

以下区域间延迟测量由[ThousandEyes](https://thousandeyes.com)提供支持。 下表中的度量单位是以毫秒为单位（毫秒）。

![Azure 区域间延迟统计信息](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>后续步骤
- 了解[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。