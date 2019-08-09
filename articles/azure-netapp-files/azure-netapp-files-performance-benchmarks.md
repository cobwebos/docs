---
title: Azure NetApp 文件的性能基准测试结果 |Microsoft Docs
description: 描述卷级别的 Azure NetApp 文件的性能基准测试结果。
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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881753"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Azure NetApp 文件的性能基准测试结果

本文介绍了卷级别的 Azure NetApp 文件的性能基准测试的结果。 

## <a name="sample-application-used-for-the-tests"></a>用于测试的示例应用程序

使用 Azure NetApp 文件的示例应用程序运行性能测试。 应用程序具有以下特征: 

* 为云构建的基于 Linux 的应用程序
* 可以通过添加的虚拟机 (Vm) 线性缩放, 以根据需要增加计算能力
* 需要快速访问 data lake
* 具有有时随机且有时为顺序的 i/o 模式 
    * 随机模式对于大量 i/o 需要低延迟。 
    * 顺序模式需要大量带宽。 

## <a name="about-the-workload-generator"></a>关于工作负荷生成器

结果来自 Vdbench 摘要文件。 [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html)是一个命令行实用工具, 用于生成用于验证存储性能的磁盘 i/o 工作负荷。 使用的客户端-服务器配置是可缩放的。  它包括单个混合主/客户端和14个专用客户端 Vm。

## <a name="about-the-tests"></a>关于测试

这些测试旨在确定示例应用程序可能具有的限制以及向上弯曲到限制的响应时间。  

运行以下测试: 

* 100% 8-KiB 随机读取
* 100% 8-KiB 随机写入
* 100% 64-KiB 顺序读取
* 100% 64-KiB 顺序写入
* 50% 64-KiB 顺序读取, 50% 64-KiB 顺序写入
* 50% 8-KiB 随机读取, 50% 8-KiB 随机写入

## <a name="bandwidth"></a>带宽

Azure NetApp 文件提供多个[服务级别](azure-netapp-files-service-levels.md)。 每个服务级别为预配容量的每个 TiB 提供不同的带宽量 (卷配额)。 根据服务级别与卷配额的组合设置卷的带宽限制。 带宽限制只是确定要实现的实际吞吐量量的一个因素。  

目前, 4500 MiB 是工作负荷在测试中针对单个卷实现的最高吞吐量。  使用 "高级" 服务级别时, 70.31 TiB 的卷配额将预配足够的带宽, 以根据以下计算实现此吞吐量: 

![带宽公式](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![配额和服务级别](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>吞吐量密集型工作负荷

吞吐量测试使用 Vdbench 和 12xD32s V3 存储 Vm 的组合。 测试中的示例卷实现了以下吞吐量数字:

![吞吐量测试](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>I/o 密集型工作负荷

I/o 测试使用的是 Vdbench 和 12xD32s V3 存储 Vm 的组合。 测试中的示例卷实现了以下 i/o 号:

![I/o 测试](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>延迟

测试 Vm 与 Azure NetApp 文件卷之间的距离会影响 i/o 性能。  下图比较了两组不同 Vm 的 IOPS 与延迟响应曲线。  一组 Vm 位于 Azure NetApp 文件附近, 而另一组 Vm 会进一步消失。  增加的 Vm 集的延迟会对在给定的并行级别实现的 IOPS 量产生影响。  无论如何, 针对卷的读取可以超过 300000 IOPS, 如下所示: 

![延迟研究](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>总结

受延迟影响的工作负荷 (数据库) 的响应时间为1毫秒。 对于单个卷, 事务性能可以超过30万个的 IOPS。

与吞吐量相关的应用程序 (适用于流式处理和映像) 可以具有 4.5 GiB/s 吞吐量。

## <a name="example-scripts"></a>示例脚本

下面的示例脚本仅用于演示目的。  它们不用于生产目的。  

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
