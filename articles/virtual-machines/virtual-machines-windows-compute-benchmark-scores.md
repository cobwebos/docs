---
title: "Windows VM 的计算基准测试分数 | Microsoft Docs"
description: "比较运行 Windows Server 的 Azure VM 的 SPECint 计算基准测试分数"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 69ae72ec-e8be-4e46-a8f0-e744aebb5cc2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 37b20ab17cc2b241e0d8a7d73dcd4433317cc0d8
ms.openlocfilehash: 835399cfa3d6b9ce8e45cec76e5e8dc17cb59855


---
# <a name="compute-benchmark-scores-for-windows-vms"></a>Windows VM 的计算基准测试分数
以下 SPECInt 基准测试分数显示运行 Windows Server 的 Azure 高性能 VM 产品阵容的计算性能。 此外，还提供了 [Linux VM](virtual-machines-linux-compute-benchmark-scores.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的计算基准测试分数。

## <a name="a-series---compute-intensive"></a>A 系列 - 计算密集型
| 大小 | vCPU | NUMA 节点 | CPU | 运行次数 | 平均基本速率 | 标准偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |10 |236.1 |1.1 |
| Standard_A9 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |10 |450.3 |7.0 |
| Standard_A10 |8 |1 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |5 |235.6 |0.9 |
| Standard_A11 |16 |2 |Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 |454.7 |4.8 |

## <a name="dv2-series"></a>Dv2 系列
| 大小 | vCPU | NUMA 节点 | CPU | 运行次数 | 平均基本速率 | 标准偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |83 |36.6 |2.6 |
| Standard_D2_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |27 |70.0 |3.7 |
| Standard_D3_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |19 |130.5 |4.4 |
| Standard_D4_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |19 |238.1 |5.2 |
| Standard_D5_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |14 |460.9 |15.4 |
| Standard_D11_v2 |2 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |19 |70.1 |3.7 |
| Standard_D12_v2 |4 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |2 |132.0 |1.4 |
| Standard_D13_v2 |8 |1 |Intel Xeon E5-2673 v3 @ 2.4 GHz |17 |235.8 |3.8 |
| Standard_D14_v2 |16 |2 |Intel Xeon E5-2673 v3 @ 2.4 GHz |15 |460.8 |6.5 |

## <a name="g-series-gs-series"></a>G 系列、GS 系列
| 大小 | vCPU | NUMA 节点 | CPU | 运行次数 | 平均基本速率 | 标准偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1、Standard_GS1 |2 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |31 |71.8 |6.5 |
| Standard_G2、Standard_GS2 |4 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |5 |133.4 |13.0 |
| Standard_G3、Standard_GS3 |8 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |6 |242.3 |6.0 |
| Standard_G4、Standard_GS4 |16 |1 |Intel Xeon E5-2698B v3 @ 2 GHz |15 |398.9 |6.0 |
| Standard_G5、Standard_GS5 |32 |2 |Intel Xeon E5-2698B v3 @ 2 GHz |22 |762.8 |3.7 |

## <a name="h-series"></a>H 系列
| 大小 | vCPU | NUMA 节点 | CPU | 运行次数 | 平均基本速率  | 标准偏差 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |5 |297.4 |0.9 |
| Standard_H16 |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |5 |575.8 |6.8 |
| Standard_H8m |8 |1 |Intel Xeon E5-2667 v3 @ 3.2 GHz |5 |297.0 |1.2 |
| Standard_H16m |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |5 |572.2 |3.9 |
| Standard_H16r |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |5 |573.2 |2.9 |
| Standard_H16mr |16 |2 |Intel Xeon E5-2667 v3 @ 3.2 GHz |7 |569.6 |2.8 |

## <a name="about-specint"></a>关于 SPECint
Windows 分数是通过在 Windows Server 上运行 [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) 计算得出的。 SPECint 是使用基本速率选项 (SPECint_rate2006) 运行的，每个核心一个副本。 SPECint 包括 12 项单独的测试，每项测试运行三次，取每次测试的中间值并为值加权，形成综合分数。 然后，在多个 VM 上运行这些测试，提供表中所示的平均分数。

## <a name="next-steps"></a>后续步骤
* 有关存储容量、磁盘详细信息以及选择 VM 大小的注意事项，请参阅[虚拟机的大小](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。




<!--HONumber=Feb17_HO2-->


