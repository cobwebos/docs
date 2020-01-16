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
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046417"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>有关 Azure NetApp 文件的 SMB 性能的常见问题解答

本文解答了有关 Azure NetApp 文件的 SMB 性能最佳做法的常见问题（Faq）。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>Smb 共享中是否启用了 SMB 多通道？ 

是的，默认情况下2020，SMB 多通道处于启用状态。 所有 SMB 共享预先可追溯的现有 SMB 卷已启用此功能，并且在创建时，所有新创建的卷还将启用该功能。 

在功能启用之前建立的任何 SMB 连接都需要重置，才能利用 SMB 多通道功能。 若要重置，你可以断开连接并重新连接 SMB 共享。

## <a name="is-rss-supported"></a>RSS 是否受支持？

是的，Azure NetApp 文件支持接收方缩放（RSS）。

启用 SMB 多通道后，SMB3 客户端通过网络接口卡（NIC）与支持单一 RSS 的网络接口卡（NIC）建立多个 TCP 连接。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支持 SMB 多通道？

Windows 支持 SMB 多通道，因为 Windows 2012 可以实现最佳性能。  有关详细信息，请参阅[部署 smb 多通道](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))和[smb 多通道基础知识](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/)。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虚拟机是否支持 RSS？

若要查看你的 Azure 虚拟机 Nic 是否支持 RSS，请按如下所示运行命令 `Get-SmbClientNetworkInterface`，并检查字段 `RSS Capable`： 

![Azure 虚拟机的 RSS 支持](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 文件是否支持 SMB Direct？

不能，Azure NetApp 文件不支持 SMB Direct。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多通道的好处是什么？ 

使用 SMB 多通道功能，SMB3 客户端可以通过单个网络接口卡（NIC）或多个 Nic 建立连接池，并使用它们来发送单个 SMB 会话的请求。 相反，根据设计，SMB1 和 SMB2 要求客户端建立一个连接，并通过该连接发送给定会话的所有 SMB 流量。 此单个连接可限制单个客户端可以实现的总体协议性能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>是否应该在客户端上为 SMB 配置多个 Nic？

不。 SMB 客户端将匹配 SMB 服务器返回的 NIC 计数。  每个存储卷只能从一个存储终结点进行访问。  这意味着，对于任何给定的 SMB 关系，只使用一个 NIC。  

由于下面 `Get-SmbClientNetworkInterace` 的输出所示，虚拟机有两个网络接口--15 和12。  如下面的命令 `Get-SmbMultichannelConnection`中所示，即使有两个支持 RSS 的 NIC，与 SMB 共享的连接中仅使用了 interface 12;接口15未被使用。

![支持 RSS 的 NIC](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 中是否支持 NIC 组合？

Azure 中不支持 NIC 组合。 虽然 Azure 虚拟机支持多个网络接口，但它们表示逻辑而不是物理构造。 因此，它们不提供容错。  此外，Azure 虚拟机的可用带宽是针对计算机本身计算的，而不是针对任何单个网络接口进行计算。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>适用于 SMB 多通道的性能是什么？

以下测试和关系图演示了单实例工作负荷上 SMB 多通道的强大功能。

### <a name="random-io"></a>随机 i/o  

如果在客户端上禁用 SMB 多通道，则使用 FIO 和 40-GiB 工作集执行纯 8 KiB 读写测试。  SMB 共享已在每个测试之间分离，其中每个 RSS 网络接口设置的 SMB 客户端连接计数 `1`，`4`，`8`，`16`，`set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`。 测试表明 `4` 的默认设置足以满足 i/o 密集型工作负荷的要求;递增到 `8` 和 `16` 不起作用。 

命令 `netstat -na | findstr 445` 证明已建立了其他连接，因为 `1` 要 `4` 到 `8` 和 `16`。  根据 perfmon `Per Processor Network Activity Cycles` 统计信息（本文中未包含），在每次测试期间，有四个 CPU 核心完全利用了 SMB。

![随机 i/o 测试](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虚拟机不会影响 SMB （也不会）存储 i/o 限制。  如下所示，对于缓存的存储 IOPS，D16 实例类型的速率为32000，而对于非缓存存储 IOPS，则为25600。  但是，上图显示了与 SMB 相比明显更多的 i/o。

![随机 i/o 比较](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>顺序 IO 

与上述随机 i/o 测试类似的测试是通过 64-KiB 顺序 i/o 来执行的。 尽管每个 RSS 网络接口增加的客户端连接数增加了 "4"，但对随机 i/o 没有明显影响，但这一点不适用于顺序 i/o。 如下图所示，每个增加都与读取吞吐量的相应增加相关联。 由于 Azure 每个实例类型/大小的网络带宽限制，写入吞吐量仍保持不变。 

![顺序 i/o 测试](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 会对每个虚拟机类型/大小施加网络速率限制。 仅对出站流量施加速率限制。 虚拟机上存在的 Nic 数与计算机的可用带宽总量无关。  例如，D16 实例类型的施加网络限制为 8000 Mbps （1000 MiB/秒）。  如上图所示，该限制会影响出站流量（写入），但不影响多通道读取。

![顺序 i/o 比较](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>是否建议使用高级网络？

为了获得最佳性能，建议尽可能配置高级网络。 请记住以下注意事项：  

* 默认情况下，Azure 门户为支持此功能的虚拟机启用高级网络。  但是，其他部署方法（如 Ansible 和类似的配置工具）可能不会。  未能启用高级网络可能会 hobble 计算机的性能。  
* 如果未在虚拟机的网络接口上启用高级网络，因为它不支持实例类型或大小，则它将保持为较大的实例类型。 在这些情况下，你将需要手动干预。

## <a name="are-jumbo-frames-supported"></a>是否支持巨型帧？

Azure 虚拟机不支持巨型帧。

## <a name="is-smb-signing-supported"></a>SMB 签名是否受支持？ 

SMB 协议为文件和打印共享以及其他网络操作（如远程 Windows 管理）提供了基础。 为了防止在传输中修改 SMB 数据包的中间人攻击，SMB 协议支持对 SMB 数据包进行数字签名。 

Azure NetApp 文件支持的所有 SMB 协议版本都支持 SMB 签名。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 签名对性能的影响有哪些？  

SMB 签名在 SMB 性能方面具有产生负面的效果。 在性能下降的其他可能原因中，每个数据包的数字签名将使用额外的客户端 CPU，如下面的 perfmon 输出所示。 在这种情况下，核心0会出现负责 SMB 的责任，包括 SMB 签名。  与上一节中的非多通道顺序读取吞吐量数字相比，这一比较表明，SMB 签名将从 875MiB/s 到大约 250MiB/秒的总体吞吐量降低。 

![SMB 签名性能影响](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>后续步骤  

- [有关 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
- 有关将 SMB 文件共享与 Azure NetApp 文件配合使用的[Smb 工作负荷，请参阅 Azure Netapp 文件：托管企业文件共享](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)。