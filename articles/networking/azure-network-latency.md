---
title: Azure 网络往返延迟统计信息 |Microsoft Docs
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893073"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监视工具和第三方合成监视服务[ThousandEyes](https://thousandeyes.com)收集的度量值来持续监视其网络核心区域的延迟（速度）。

## <a name="how-are-the-measurements-collected"></a>度量值的收集方式是什么？

延迟度量是从世界各地的 Azure 云区域中托管的 ThousandEyes 代理收集的，它们在1分钟的时间间隔内持续发送网络探测。 每月滞后时间统计信息派生自每月收集的样本数。

## <a name="november-2019-latency-figures"></a>11月2019延迟数字

**11 月更新：** 添加了3个区域。

* 挪威东部
* 挪威西部
* 澳大利亚

过去30天（截至2019年11月30日）的 Azure 区域之间的每月平均往返时间，如下所示。 以下度量值由[ThousandEyes](https://thousandeyes.com)提供支持。

![Azure 区域间延迟统计信息](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>后续步骤

了解[Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
