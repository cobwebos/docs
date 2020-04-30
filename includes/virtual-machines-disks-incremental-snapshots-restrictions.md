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
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204457"
---
- 当前不能在订阅之间移动增量快照。
- 在任何给定时间，当前最多只能生成一个特定快照系列的最多五个快照的 SAS Uri。
- 不能为该磁盘的订阅之外的特定磁盘创建增量快照。
- 每个磁盘最多可以创建7个增量快照，每5分钟一次。
- 总共可以为单个磁盘创建200个增量快照。
- 在父磁盘大小之间的更改发生在 4 TB 边界内之前和之后，不能获取所做的更改。 必须再次下载在调整大小时创建的快照的完整副本。 然后，你可以在大小调整到 4 TB 边界之后，获取在创建的快照之间的更改。 
