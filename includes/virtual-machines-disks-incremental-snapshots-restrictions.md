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
ms.openlocfilehash: 8e4c1c05b133f85b7473c3ce97c9f509ac8f7d37
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299140"
---
- 当前不能在订阅之间移动增量快照。
- 在任何给定时间，当前最多只能生成一个特定快照系列的最多五个快照的 SAS Uri。
- 不能为该磁盘的订阅之外的特定磁盘创建增量快照。
- 每个磁盘最多可以创建7个增量快照，每5分钟一次。
- 总共可以为单个磁盘创建200个增量快照。