---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027441"
---
区域冗余存储 (ZRS) 以同步方式在单个区域中的三个存储群集之间复制数据。 各个存储群集以物理方式相互隔离，并驻留在自身的可用性区域 (AZ) 中。 每个可用性区域及其中的 ZRS 群集是自主性的，具有独立的实用工具和网络功能。

将数据存储在 ZRS 帐户中可以确保当某个区域不可用时，能够访问和管理自己的数据。 ZRS 提供卓越的性能和较低的延迟。 ZRS 提供与[本地冗余存储 (LRS)](../articles/storage/common/storage-redundancy-lrs.md) 相同的[可伸缩性目标](../articles/storage/common/storage-scalability-targets.md)。

如果场景中要求满足以下要求，请考虑使用 ZRS：即使服务中断或自然灾难导致局域数据中心不可用，也要求提供强一致性、强持久性和高可用性。 ZRS 在一年中提供至少 99.9999999999%（12 个 9）的存储对象持久性。

有关可用性区域的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。