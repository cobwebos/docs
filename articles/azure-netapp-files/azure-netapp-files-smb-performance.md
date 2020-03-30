---
title: 有关 Azure NetApp 文件 SMB 性能的常见问题解答*微软文档
description: 回答有关 Azure NetApp 文件的 SMB 性能的常见问题。
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460443"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>有关 Azure NetApp 文件的 SMB 性能的常见问题解答

本文回答了有关 Azure NetApp 文件的 SMB 性能最佳实践的常见问题 （FAQ）。

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB 多通道是否在 SMB 股票中启用？ 

是，默认情况下启用 SMB 多渠道，这是 2020 年 1 月初进行的更改。 所有早于现有 SMB 卷的 SMB 共享都启用了该功能，所有新创建的卷在创建时也将启用该功能。 

在功能启用之前建立的任何 SMB 连接都需要重置才能利用 SMB 多通道功能。 要重置，可以断开连接并重新连接 SMB 共享。

## <a name="is-rss-supported"></a>是否支持 RSS？

是的，Azure NetApp 文件支持接收侧缩放 （RSS）。

启用 SMB 多通道后，SMB3 客户端通过支持单个 RSS 的网络接口卡 （NIC） 建立到 Azure NetApp 文件 SMB 服务器的多个 TCP 连接。 

## <a name="which-windows-versions-support-smb-multichannel"></a>哪些 Windows 版本支持 SMB 多渠道？

自 Windows 2012 年以来，Windows 一直支持 SMB 多渠道，以实现最佳性能。  有关详细信息，请参阅[部署 SMB 多通道](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))和[SMB 多通道的基础知识](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/)。 


## <a name="does-my-azure-virtual-machine-support-rss"></a>我的 Azure 虚拟机是否支持 RSS？

要查看 Azure 虚拟机 NIC 是否支持 RSS，请按照`Get-SmbClientNetworkInterface`如下方式运行该命令并`RSS Capable`检查字段： 

![对 Azure 虚拟机的 RSS 支持](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 文件是否支持 SMB 直接文件？

否，Azure NetApp 文件不支持 SMB Direct。 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 多渠道有什么好处？ 

SMB 多通道功能使 SMB3 客户端能够通过单个网络接口卡 （NIC） 或多个 NIC 建立连接池，并使用它们发送单个 SMB 会话的请求。 相反，根据设计，SMB1 和 SMB2 要求客户端建立一个连接，并通过该连接发送给定会话的所有 SMB 流量。 此单个连接限制可以从单个客户端实现的总体协议性能。

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>我应该为 SMB 配置客户端上的多个 NIC 吗？

不是。 SMB 客户端将与 SMB 服务器返回的 NIC 计数匹配。  每个存储卷可从一个存储终结点访问。  这意味着对于任何给定的 SMB 关系，将只使用一个 NIC。  

如下文的`Get-SmbClientNetworkInterace`输出所示，虚拟机有两个网络接口 - 15 和 12。  如下图所示`Get-SmbMultichannelConnection`，即使有两个支持 RSS 的 NICS，也仅使用接口 12 与 SMB 共享连接;接口 15 未使用。

![支持 RSS 的 NICS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Azure 中是否支持 NIC 团队？

Azure 中不支持 NIC 团队。 尽管 Azure 虚拟机支持多个网络接口，但它们表示逻辑而不是物理构造。 因此，它们不提供容错。  此外，Azure 虚拟机可用的带宽是为计算机本身计算的，而不是为任何单独的网络接口计算的。

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 多渠道的性能如何？

以下测试和图表演示了 SMB 多通道对单实例工作负载的强大功能。

### <a name="random-io"></a>随机 I/O  

在客户端上禁用 SMB 多通道后，使用 FIO 和 40-GiB 工作集执行纯 8-KiB 读写测试。  SMB 共享在每个测试之间分离，SMB 客户端连接计数的增量为每个 RSS 网络`1`接口设置`4`，`8``16`、 `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`、 。 测试显示，的`4`默认设置足以满足 I/O 密集型工作负载;递增至`8`无`16`效果。 

该命令`netstat -na | findstr 445``1`证明，建立其他连接时，从 到`4``8`和 以`16`递增为增量。  在每次测试期间，SMB 都充分利用了四个 CPU 内核，这一点`Per Processor Network Activity Cycles`由 perfmon 统计信息（不包括在本文中）所证实。

![随机 I/O 测试](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 虚拟机不会影响 SMB（或 NFS） 存储 I/O 限制。  如下所示，D16 实例类型的缓存存储 IOPS 的受限速率为 32，000，未缓存存储 IOPS 的速率为 25，600。  但是，上图显示的 I/O 比 SMB 明显多。

![随机 I/O 比较](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>顺序 IO 

与上述随机 I/O 测试类似的测试使用 64-KiB 顺序 I/O 执行。 尽管每个 RSS 网络接口的客户端连接计数增加超过 4' 对随机 I/O 没有明显影响，但顺序 I/O 也不适用。 如下图所示，每次增加都与读取吞吐量的相应增加相关。 由于 Azure 对每个实例类型/大小施加的网络带宽限制，写入吞吐量保持不变。 

![顺序 I/O 测试](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure 对每个虚拟机类型/大小进行网络速率限制。 速率限制仅对出站流量施加限制。 虚拟机上存在的 NIC 数与计算机可用的带宽总量无关。  例如，D16 实例类型施加的网络限制为 8000 Mbps（1，000 MiB/s）。  如上图所示，限制会影响出站流量（写入），但不会影响多通道读取。

![顺序 I/O 比较](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>是否建议加速联网？

为了达到最佳性能，建议尽可能配置[加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)。 请记住以下注意事项：  

* 默认情况下，Azure 门户为支持此功能的虚拟机启用加速网络。  但是，其他部署方法（如 Ansible 和类似的配置工具）可能不会。  如果启用加速网络可能会妨碍机器的性能。  
* 如果由于虚拟机缺少实例类型或大小的支持，因此未在虚拟机的网络接口上启用加速网络，则该接口将保持禁用状态，实例类型较大。 在这种情况下，您将需要手动干预。

## <a name="are-jumbo-frames-supported"></a>支持巨型帧吗？

Azure 虚拟机不支持巨型帧。

## <a name="is-smb-signing-supported"></a>是否支持 SMB 签名？ 

SMB 协议为文件和打印共享以及其他网络操作（如远程 Windows 管理）提供了基础。 为了防止在传输中修改 SMB 数据包的中间人攻击，SMB 协议支持对 SMB 数据包进行数字签名。 

对于 Azure NetApp 文件支持的所有 SMB 协议版本，都支持 SMB 签名。 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 签名对性能有何影响？  

SMB 签名对 SMB 性能有有害影响。 在性能下降的其他潜在原因中，每个数据包的数字签名会消耗额外的客户端 CPU，如下所述 perfmon 输出所示。 在这种情况下，核心 0 似乎负责 SMB，包括 SMB 签名。  与上一节中的非多通道顺序读取吞吐量数字的比较显示，SMB 签名可将总体吞吐量从 875MiB/s 降至大约 250MiB/s。 

![SMB 签名性能影响](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>后续步骤  

- [关于 Azure NetApp 文件的常见问题解答](azure-netapp-files-faqs.md)
- 请参阅 Azure NetApp 文件：有关使用 SMB 文件共享的[SMB 工作负载的托管企业文件共享](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)。
