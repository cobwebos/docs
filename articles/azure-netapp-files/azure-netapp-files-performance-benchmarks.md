---
title: Azure NetApp 文件的性能基准 |Microsoft Docs
description: 介绍 Azure NetApp 文件在卷级别的性能基准测试结果。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: aca0668fc364518fe45d9fe94d089ee366b25676
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870877"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Azure NetApp 文件的性能基准

本文介绍 Azure NetApp 文件在卷级别的性能基准测试结果。 

## <a name="sample-application-used-for-the-tests"></a>用于测试的示例应用程序

使用 Azure NetApp 文件的示例应用程序运行性能测试。 应用程序具有以下特征： 

* 针对云构建的基于 Linux 的应用程序
* 可以缩放以线性方式与添加虚拟机 (Vm) 以根据需要增加计算能力
* 需要快速的 data lake 的可访问性
* 具有有时是随机的并且有时顺序的 I/O 模式 
    * 一个随机图案大量的 I/O 要求较低的延迟。 
    * 顺序模式需要大量的带宽。 

## <a name="about-the-workload-generator"></a>有关工作负荷生成器

结果来自 Vdbench 摘要文件。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html)是一个命令行实用工具，生成用于验证存储性能的磁盘 I/O 工作负荷。 使用的客户端-服务器配置为可缩放。  它包括单个混合母版/客户端和 14 的专用客户端 Vm。

## <a name="about-the-tests"></a>有关测试

这些测试旨在识别示例应用程序可能具有的限制和响应时间达到的限制该曲线。  

运行以下测试： 

* 100%的 8 KiB 随机读取
* 100 %8 KiB 随机写入
* 100 %64 KiB 顺序读取
* 100 %64 KiB 顺序写入
* 50 %64 KiB 顺序读取、 50 %64 KiB 顺序写入
* 8 KiB 随机读取的 50%、 50 %8 KiB 随机写入

## <a name="bandwidth"></a>带宽

Azure 的 NetApp 文件提供多个[服务级别](azure-netapp-files-service-levels.md)。 每个服务级别提供了不同数量的每个 TiB 的预配的容量 （卷配额） 的带宽。 卷的带宽限制是预配基于服务级别和卷配额的组合。 请注意，带宽限制是确定的实际吞吐量会意识到量只有一个因素。  

目前，4500 MiB 是通过工作负荷对在测试中的单个卷的最高吞吐量。  具有高级服务级别，70.31 TiB 的卷配额将预配足够的带宽来实现此吞吐量为每个以下计算： 

![带宽公式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![配额和服务级别](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>吞吐量密集型工作负荷

吞吐量测试使用 Vdbench 和 12xD32s V3 存储 Vm 的组合。 在测试中的示例卷来实现以下的吞吐量数字：

![吞吐量测试](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>O 密集的工作负载

I/O 测试使用 Vdbench 和 12xD32s V3 存储 Vm 的组合。 在测试中的示例卷来实现以下的 I/O 数量：

![I/O 测试](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latency

在测试 Vm 和 Azure NetApp 文件卷之间的距离会对 I/O 性能的影响。  下面的图表比较了与延迟响应曲线的两组不同的 Vm 的 IOPS。  一组 Vm 是 Azure NetApp 文件附近和另一组较远。  请注意增加延迟时间更多组 Vm 会在给定级别的并行度来实现的 IOPS 量的影响。  无论如何，读取卷可以超过 300,000 IOPS，如下图所示： 

![延迟研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>摘要

易受延迟影响工作负荷 （数据库） 可以具有一个的毫秒级响应时间。 事务处理性能可以超过 300 k IOPS 为单个卷。

（适用于流式处理和图像处理） 的吞吐量敏感型应用程序可以具有 4.5GiB / 秒的吞吐量。
