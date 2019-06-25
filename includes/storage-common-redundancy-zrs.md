---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133646"
---
区域冗余存储 (ZRS) 以同步方式在单个区域中的三个存储群集之间复制数据。 各个存储群集以物理方式相互隔离，并位于其自己的可用性区域 (AZ) 中。 每个可用性区域&mdash;及其中的 ZRS 群集&mdash;都是自治的，包括单独的实用程序和网络功能。 只有在数据写入三个群集之间的所有副本后才成功返回到 ZRS 存储帐户的写入请求。

使用 ZRS 复制将数据存储在存储帐户中时，即使可用性区域不可用，你也可以继续访问和管理数据。 ZRS 提供卓越的性能和较低的延迟。 ZRS 提供与[本地冗余存储 (LRS)](../articles/storage/common/storage-redundancy-lrs.md) 相同的[可伸缩性目标](../articles/storage/common/storage-scalability-targets.md)。

对于需要一致性、持久性和高可用性的方案，请考虑使用 ZRS。 即使服务中断或自然灾害导致可用性区域不可用，ZRS 也会在给定年份内为存储对象提供至少 99.9999999999%（12 个 9）的持久性。

有关可用性区域的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。