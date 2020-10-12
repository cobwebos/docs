---
title: 使用 Azure NetApp 文件实现电子设计自动化的好处 |Microsoft Docs
description: 介绍 Azure NetApp 文件为满足半导体和芯片设计行业的需求而提供的解决方案。 演示如何使用 Azure NetApp 文件 (EDA) 为电子设计自动化运行标准行业基准测试方案。
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
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82160147"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>使用 Azure NetApp 文件实现电子设计自动化的好处

面市 (TTM) 是半导体和芯片设计行业的一项重要考虑因素。 行业具有高带宽和低延迟的存储需求。 本文介绍 Azure NetApp 文件为满足行业需求而提供的解决方案。 它提供使用 Azure NetApp 文件 (EDA) ，为电子设计自动化 EDA 提供标准行业基准测试方案。 

## <a name="test-scenario-configurations"></a>测试方案配置

测试涉及三个方案，其中包含以下配置。 

|    方案    |    卷    |    客户端<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    一个         |    1          |    1                           |
|    两个         |    6          |    24                          |
|    三级       |    12         |    24                          |

第一种情况是解决单个卷的驱动情况。  

第二个和第三个方案计算单个 Azure NetApp 文件端点的限制。 它们调查 i/o 上限和延迟的潜在优势。

## <a name="test-scenario-results"></a>测试方案结果

下表总结了测试方案的结果。

|    方案       |    I/o 速率<br>  2毫秒     |    I/o 速率<br>  边缘     |    吞吐量<br>  2毫秒     |    吞吐量<br>  边缘     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1个卷       |    39601                 |    49502                      |    692 MiB/秒                 |    866 MiB/秒                      |
|    6卷      |    255613                |    317000                     |    4577 MiB/秒               |    5568 MiB/秒                    |
|    12个卷     |    256612                |    319196                     |    4577 MiB/秒               |    5709 MiB/秒                    |

单卷方案表示基本应用程序配置。 这是跟进测试方案的基准应用场景。  

六卷方案演示了相对于单卷工作负荷 (600% ) 的线性增长。  单个虚拟网络内的所有卷都可通过单个 IP 地址进行访问。  

12卷方案说明了在六个卷方案下的一般延迟。 但它不会增加可实现的吞吐量。   

下图说明了 Azure NetApp 文件上的 EDA 工作负荷的延迟和操作速率。  

![Azure NetApp 文件上的 EDA 工作负荷的延迟和操作速率](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

下图说明了 Azure NetApp 文件上的 EDA 工作负荷的延迟和吞吐量。  

![Azure NetApp 文件上的 EDA 工作负载的延迟和吞吐量](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>测试方案的布局 

下表总结了测试方案的布局。

|    测试方案     |    目录总数     |    文件总数     |
|----------------------|------------------------------------|------------------------------|
|    1个卷          |    88000                          |    880000                   |
|    6卷         |    568000                         |    5680000                 |
|    12个卷        |    568000                         |    5680000                 |

完整的工作负荷混合了并发运行的功能和物理阶段。 它表示从一组 EDA 工具到另一组工具的典型流。   

功能阶段包含初始规范和逻辑设计。 逻辑设计转换为物理芯片时，会发生物理阶段。 在注销和磁带传出阶段，最终检查已完成，设计将交付到 foundry 进行制造。  

功能阶段包含顺序和随机读写 i/o。 功能阶段是元数据密集型，如文件状态和访问调用。 尽管元数据操作实际上没有大小，但读写操作的范围介于 1 K 和 16 K 之间。大多数读数介于 4 K 和 16 K 之间。 大多数写入量为 4 K 或更小。 物理阶段包含完全按顺序读取和写入操作，混合了 32 K 和 64 K OP 大小。  

在上面的图形中，大部分吞吐量都来自工作负荷的顺序物理阶段。 I/o 来自小型随机和元数据密集型功能阶段。 这两个阶段都是并行发生的。 

总之，可以将 Azure 计算与用于 EDA 设计的 Azure NetApp 文件配对，以获得可伸缩的带宽。 

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
