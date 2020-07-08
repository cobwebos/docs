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
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204457"
---
- 增量快照当前无法在订阅之间转移。
- 当前，在任何给定时间，只能为某个特定系列的最多 5 个快照生成 SAS URI。
- 不能在磁盘的订阅之外为该磁盘创建增量快照。
- 每五分钟最多可为每个磁盘创建 7 个增量快照。
- 总共可以为单个磁盘创建 200 个增量快照。
- 在父磁盘大小之间的更改发生在 4 TB 边界内之前和之后，不能获取所做的更改。 需要再次下载调整大小后创建的快照的完整副本。 然后，你可以在大小调整到 4 TB 边界之后，获取在创建的快照之间的更改。 
