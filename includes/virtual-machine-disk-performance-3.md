---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016430"
---
![度量值菜单](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

但是，Standard_D8s_v3 可实现总 28600 IOPs，使用度量值可调查正在进行的操作，并识别存储 IO 瓶颈。 首先，找到 "指标" 按钮左侧菜单并选择它：

![度量值菜单](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

首先，让我们看一下我们的 **VM 缓存 IOPS 消耗百分比** 指标：

![使用的 VM 缓存 IOPS 百分比](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

此指标告诉我们，在使用61% 的情况下，已分配给 VM 上缓存 IOPs 的 16000 IOPs。 这意味着存储 IO 瓶颈与缓存的磁盘无关，因为它不在100%。 现在，让我们看一下我们的 **VM 未缓存 IOPS 消耗百分比** 指标：

![使用的 VM 未缓存 IOPS 百分比](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

此指标处于100%，告诉我们，所有分配给 VM 上的未缓存 IOPs 的 12800 IOPs 都是使用。 我们可以通过将 VM 的大小更改为可处理额外 IO 的更大的大小，来纠正这种情况。 但在执行此操作之前，让我们先看一下附加的磁盘，看看他们看到了多少 IOPs。 首先，让我们看一下 OS 磁盘的 **使用百分比**：

![OS 磁盘 IOPS 消耗百分比](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

此指标告诉我们，为此 P30 OS 磁盘预配了 5000 IOPs （约90% 的使用）。 这意味着操作系统磁盘上没有瓶颈。 现在，让我们看看已 **使用的数据磁盘 IOPS 百分比**连接到 VM 的数据磁盘：

![使用的数据磁盘 IOPS 百分比](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

此指标告诉我们，每个附加磁盘上使用的平均 IOPs 百分比约为42%。 此百分比是根据磁盘使用的 IOPs 计算得出的，并且不是从主机缓存中提供的。 让我们深入了解此指标，通过对这些指标应用 **拆分** 并按 LUN 值进行拆分来查看：

![数据磁盘 IOPS 使用拆分的百分比](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

此指标告诉我们，LUN 3 和2上附加的数据磁盘的使用约85% 的预配 IOPs。 下面是 VM 和磁盘体系结构中 IO 的外观示意图：

![存储 IO 指标示例图示](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
