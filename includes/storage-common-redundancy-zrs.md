---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463944"
---
区域冗余存储 (ZRS) 以同步方式在单个区域中的三个存储群集之间复制数据。 各个存储群集以物理方式相互隔离，并位于其自己的可用性区域 (AZ) 中。 每个可用性区域&mdash;及其中的 ZRS 群集&mdash;都是自治的，包括单独的实用程序和网络功能。 仅在将数据写入到跨三个群集的所有副本后，才成功返回对 ZRS 存储帐户的写入请求。

使用 ZRS 复制将数据存储在存储帐户中时，即使可用性区域不可用，你也可以继续访问和管理数据。 ZRS 提供卓越的性能和较低的延迟。 ZRS 提供与[本地冗余存储（LRS）](../articles/storage/common/storage-redundancy-lrs.md)相同的可伸缩性目标。 有关标准存储帐户的可伸缩性目标的详细信息，请参阅[标准存储帐户的可伸缩性目标](../articles/storage/common/scalability-targets-standard-account.md)。

对于需要一致性、持久性和高可用性的方案，请考虑使用 ZRS。 即使服务中断或自然灾害导致可用性区域不可用，ZRS 也会在给定年份内为存储对象提供至少 99.9999999999%（12 个 9）的持久性。

区域冗余存储（GZRS）（预览版）可跨主要区域的三个 Azure 可用性区域同步复制数据，然后将数据异步复制到次要区域。 GZRS 提供高可用性，同时提供最大持久性。 GZRS 设计为在给定的一年内提供至少99.99999999999999% （16个9）的对象持久性。 若要对次要区域中的数据进行读取访问，请启用读取访问权限异地冗余存储（GZRS）。 有关 GZRS 的详细信息，请参阅[区域冗余存储以获得高可用性和最大持续性（预览版）](../articles/storage/common/storage-redundancy-gzrs.md)。

有关可用性区域的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。
