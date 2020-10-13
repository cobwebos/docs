---
title: 有关 Azure NetApp 文件的 SMB 性能的常见问题解答 |Microsoft Docs
description: 解答有关 Azure NetApp 文件的 SMB 性能的常见问题。
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929450"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>有关 Azure NetApp 文件的 SMB 性能的常见问题解答

本文解答了常见问题 (常见问题解答) 有关 Azure NetApp 文件的 SMB 性能最佳实践。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Smb 共享中是否启用了 SMB 多通道？ 

是的，默认情况下2020，SMB 多通道处于启用状态。 所有 SMB 共享预先可追溯的现有 SMB 卷已启用此功能，并且在创建时，所有新创建的卷还将启用该功能。 

在功能启用之前建立的任何 SMB 连接都需要重置，才能利用 SMB 多通道功能。 若要重置，你可以断开连接并重新连接 SMB 共享。

## <a name="is-rss-supported"></a>RSS 是否受支持？

是的，Azure NetApp 文件支持接收方缩放 (RSS) 。

启用 SMB 多通道后，SMB3 客户端通过网络接口卡（ (NIC) （支持单一 RSS）建立到 Azure NetApp 文件 SMB 服务器的多个 TCP 连接。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支持 SMB 多通道？

Windows 支持 SMB 多通道，因为 Windows 2012 可以实现最佳性能。  有关详细信息，请参阅 [部署 smb 多通道](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) 和 [smb 多通道基础知识](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) 。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虚拟机是否支持 RSS？

若要查看你的 Azure 虚拟机 Nic 是否支持 RSS，请按如下所示运行命令 `Get-SmbClientNetworkInterface` 并检查字段 `RSS Capable` ： 

![显示 Azure 虚拟机 RSS 输出的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 文件是否支持 SMB Direct？

不能，Azure NetApp 文件不支持 SMB Direct。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多通道的好处是什么？ 

使用 SMB 多通道功能，SMB3 客户端可以通过单个网络接口卡 (NIC) 或多个 Nic 建立连接池，并使用它们来发送单个 SMB 会话的请求。 相反，根据设计，SMB1 和 SMB2 要求客户端建立一个连接，并通过该连接发送给定会话的所有 SMB 流量。 此单个连接可限制单个客户端可以实现的总体协议性能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>是否应该在客户端上为 SMB 配置多个 Nic？

不是。 SMB 客户端将匹配 SMB 服务器返回的 NIC 计数。  每个存储卷只能从一个存储终结点进行访问。  这意味着，对于任何给定的 SMB 关系，只使用一个 NIC。  

由于下面的输出所 `Get-SmbClientNetworkInterace` 示，虚拟机具有2个网络接口--15 和12。  如下面的命令中所示，尽管有 `Get-SmbMultichannelConnection` 两个支持 RSS 的 nic，但与 SMB 共享的连接中仅使用了 interface 12; 接口15未在使用中。

![屏幕截图 notificationhubs，用于显示支持 RSS 的 NIC 的输出。](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 中是否支持 NIC 组合？

Azure 中不支持 NIC 组合。 虽然 Azure 虚拟机支持多个网络接口，但它们表示逻辑而不是物理构造。 因此，它们不提供容错。  此外，Azure 虚拟机的可用带宽是针对计算机本身计算的，而不是针对任何单个网络接口进行计算。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>适用于 SMB 多通道的性能是什么？

以下测试和关系图演示了单实例工作负荷上 SMB 多通道的强大功能。

### <a name="random-io"></a>随机 i/o  

如果在客户端上禁用 SMB 多通道，则使用 FIO 和 40 GiB 工作集执行纯 4 KiB 读写测试。  SMB 共享已在每个测试之间分离，同时每个 RSS 网络接口设置为、、、、、、、、和 `1` `4` `8` `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` 。 这些测试表明的默认设置足以满足 `4` i/o 密集型工作负荷的需求; 递增到 `8` 并且具有可 `16` 忽略的影响。 

此命令 `netstat -na | findstr 445` 证明，已建立了其他连接，并递增了到到和之间的连接 `1` `4` `8` `16` 。  在每个测试过程中，SMB 已充分利用了四个 CPU 核心，这是由 perfmon `Per Processor Network Activity Cycles` 统计信息确认 (本文不包含在内的。 ) 

![显示 SMB 多通道的随机 i/o 比较的图表。](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虚拟机不会影响 SMB (和 NFS) 存储 i/o 限制。  如下图所示，对于缓存的存储 IOPS，D32ds 实例类型的最小速率为308000，对于非缓存存储 IOPS，则为51200。  但是，上图显示了与 SMB 相比明显更多的 i/o。

![显示随机 i/o 比较测试的图表。](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>顺序 IO 

类似于前面所述的随机 i/o 测试的测试是通过64顺序 i/o 执行的 KiB。 尽管每个 RSS 网络接口增加的客户端连接数增加了 "4"，但对随机 i/o 没有明显影响，但这一点不适用于顺序 i/o。 如下图所示，每个增加都与读取吞吐量的相应增加相关联。 由于 Azure 每个实例类型/大小的网络带宽限制，写入吞吐量仍保持不变。 

![显示吞吐量测试比较的图表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 会对每个虚拟机类型/大小施加网络速率限制。 仅对出站流量施加速率限制。 虚拟机上存在的 Nic 数与计算机的可用带宽总量无关。  例如，D32ds 实例类型的施加网络限制为 16000 Mbps (2000 MiB/秒) 。  如上图所示，该限制会影响出站流量 (写入) 但不是多个多通道读取。

![显示顺序 i/o 比较测试的图表。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>使用 1 TB 数据集的单个实例所需的性能是多少？

为了更详细地了解具有读/写混合的工作负荷，以下两个图表显示了具有 1 TB 数据集且 SMB 多通道为4的单个超级服务级别云卷（50 TB）的性能。 使用的是16的最佳 IODepth，而灵活的 IO (FIO) 参数用于确保 () 充分利用网络带宽 `numjobs=16` 。

下图显示了4k 随机 i/o 的结果，其中单个 VM 实例和读/写混合的时间间隔为10%：

![显示 Windows 2019 standard _D32ds_v4 4K 随机 IO 测试的图表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

下图显示了顺序 i/o 的结果：

![显示 Windows 2019 standard _D32ds_v4 64K 顺序吞吐量的图表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>使用具有 1 TB 数据集的5个 Vm 进行扩展时，预期的性能是什么？

使用5个 Vm 的这些测试将使用与单个 VM 相同的测试环境，每个进程将写入其自己的文件。

下图显示了随机 i/o 的结果：

![显示 Windows 2019 standard _D32ds_v4 4K 5 实例 randio IO 测试的图表。](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

下图显示了顺序 i/o 的结果：

![显示 Windows 2019 standard _D32ds_v4 64K 5 实例顺序吞吐量的图表。](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>如何监视 Hyper-v 以太网适配器并确保最大程度地提高网络容量？  

用于测试 FIO 的一种策略是设置 `numjobs=16` 。 这样做会将每个作业分叉为16个特定实例，以将 Microsoft Hyper-V 的网络适配器最大化。

可以通过选择 " **性能监视器" > > 网络接口 > Microsoft Hyper-V 网络适配器中添加计数器**来检查 Windows 性能监视器中的每个适配器上的活动。

![显示性能监视器添加计数器接口的屏幕截图。](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

在卷中运行数据流量后，可以在 Windows 性能监视器中监视适配器。 如果不使用这两个虚拟适配器，可能无法最大程度地提高网络带宽容量。

![显示性能监视器输出的屏幕截图。](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>是否建议加速网络？

为了获得最佳性能，建议在可能的情况下配置 [加速网络](../virtual-network/create-vm-accelerated-networking-powershell.md) 。 请记住以下注意事项：  

* 默认情况下，Azure 门户为支持此功能的虚拟机启用加速网络。  但是，其他部署方法（如 Ansible 和类似的配置工具）可能不会。  未能启用加速网络可能会 hobble 计算机的性能。  
* 如果虚拟机的网络接口上未启用加速网络，原因是它不支持实例类型或大小，则它将保持为较大的实例类型。 在这些情况下，你将需要手动干预。

## <a name="are-jumbo-frames-supported"></a>是否支持巨型帧？

Azure 虚拟机不支持巨型帧。

## <a name="is-smb-signing-supported"></a>SMB 签名是否受支持？ 

SMB 协议为文件和打印共享以及其他网络操作（如远程 Windows 管理）提供了基础。 为了防止在传输中修改 SMB 数据包的中间人攻击，SMB 协议支持对 SMB 数据包进行数字签名。 

Azure NetApp 文件支持的所有 SMB 协议版本都支持 SMB 签名。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 签名对性能的影响有哪些？  

SMB 签名在 SMB 性能方面具有产生负面的效果。 在性能下降的其他可能原因中，每个数据包的数字签名将使用额外的客户端 CPU，如下面的 perfmon 输出所示。 在这种情况下，核心0会出现负责 SMB 的责任，包括 SMB 签名。  与上一节中的非多通道顺序读取吞吐量数字相比，这一比较表明，SMB 签名将从 875MiB/s 到大约 250MiB/秒的总体吞吐量降低。 

![显示 SMB 签名性能影响的图表。](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>后续步骤  

- [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
- 有关将 SMB 文件共享与 Azure NetApp 文件配合使用的 [Smb 工作负荷，请参阅 Azure Netapp 文件：托管企业文件共享](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) 。