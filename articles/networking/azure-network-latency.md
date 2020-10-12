---
title: Azure 网络往返延迟统计信息 |Microsoft Docs
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 72168a56bfb4e08c7f44c84c773d9f6599cfa607
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848870"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监视工具以及 [ThousandEyes](https://thousandeyes.com)（第三方合成监视服务）收集的度量，持续监视其网络的核心区域的延迟 (速度) 。

## <a name="how-are-the-measurements-collected"></a>度量值的收集方式是什么？

延迟度量是从世界各地的 Azure 云区域中托管的 ThousandEyes 代理收集的，它们在1分钟的时间间隔内持续发送网络探测。 每月滞后时间统计信息派生自每月收集的样本数。

## <a name="september-2020-round-trip-latency-figures"></a>2020年9月的往返延迟图形

过去30天的 Azure 区域之间的每月平均往返时间 (于2020年9月30日结束) ，如下所示。 以下度量值由 [ThousandEyes](https://thousandeyes.com)提供支持。

[![Azure 区域间延迟统计信息](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
