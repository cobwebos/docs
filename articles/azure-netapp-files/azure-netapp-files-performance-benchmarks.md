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
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449506"
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

* 100 %8 KiB 随机读取
* 100 %8 KiB 随机写入
* 100 %64 KiB 顺序读取
* 100 %64 KiB 顺序写入
* 50 %64 KiB 顺序读取，50 %64 KiB 顺序写入
* 50 %8 KiB 随机读取，50 %8 KiB 随机写入

## <a name="bandwidth"></a>带宽

Azure 的 NetApp 文件提供多个[服务级别](azure-netapp-files-service-levels.md)。 每个服务级别提供了不同数量的每个 TiB 的预配的容量 （卷配额） 的带宽。 卷的带宽限制是预配基于服务级别和卷配额的组合。 带宽限制是确定的实际吞吐量会意识到量只有一个因素。  

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

在测试 Vm 和 Azure NetApp 文件卷之间的距离会对 I/O 性能的影响。  下面的图表比较了与延迟响应曲线的两组不同的 Vm 的 IOPS。  一组 Vm 是 Azure NetApp 文件附近和另一组较远。  更多组 Vm 增加延迟时间会影响在给定级别的并行度来实现的 IOPS 量。  无论如何，读取卷可以超过 300,000 IOPS，如下图所示： 

![延迟研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>摘要

易受延迟影响工作负荷 （数据库） 可以具有一个毫秒级响应时间。 事务处理性能可以超过 300 k IOPS 为单个卷。

（适用于流式处理和图像处理） 的吞吐量敏感型应用程序可以具有 4.5GiB / 秒的吞吐量。

## <a name="example-scripts"></a>示例脚本

下面的示例脚本是仅用于演示目的。  它们不用于生产目的。  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
