---
title: Azure 网络往返延迟统计信息 |Microsoft Docs
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847426"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监视工具以及[ThousandEyes](https://thousandeyes.com)（第三方合成监视服务）收集的度量，持续监视其网络的核心区域的延迟 (速度) 。

## <a name="how-are-the-measurements-collected"></a>度量值的收集方式是什么？

延迟度量是从世界各地的 Azure 云区域中托管的 ThousandEyes 代理收集的，它们在1分钟的时间间隔内持续发送网络探测。 每月滞后时间统计信息派生自每月收集的样本数。

## <a name="july-2020-round-trip-latency-figures"></a>2020年7月的双程延迟图形

在2020年7月31日 (结束的 Azure 区域之间，每月平均往返时间) ，如下所示。 以下度量值由[ThousandEyes](https://thousandeyes.com)提供支持。

[![Azure 区域间延迟统计信息](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
