---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102474"
---
- 增量快照当前无法在订阅之间转移。
- 当前，在任何给定时间，只能为某个特定系列的最多 5 个快照生成 SAS URI。
- 不能在磁盘的订阅之外为该磁盘创建增量快照。
- 每五分钟最多可为每个磁盘创建 7 个增量快照。
- 总共可以为单个磁盘创建 200 个增量快照。
- 在将父磁盘的大小更改为 4 TB 边界之前和之后，无法获取所做的快照之间的更改。 例如，拍摄增量快照快照后，磁盘大小为 2 TB。 现在，你已将磁盘大小增加到 6 TB，然后再执行一个增量快照快照-b。 你无法在快照-a 与快照 b 之间获取更改。 必须再次下载在调整大小时创建的快照 b 的完整副本。 然后，你可以获取在快照 b 之后创建的快照 b 和快照之间的更改。 
