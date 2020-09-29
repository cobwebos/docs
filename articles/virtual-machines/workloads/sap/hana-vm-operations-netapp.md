---
title: SAP HANA Azure 虚拟机和配置 |Microsoft Docs
description: 适用于 SAP HANA 的 Azure NetApp 文件存储建议。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP，Azure，和，HANA，Azure NetApp 文件，快照
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d0df2199a2afdbea1ee1c6c543df324e9fe3cc3e
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450692"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>适用于 SAP HANA 的 Azure NetApp 文件上的 NFS 4.1 卷

Azure NetApp 文件提供可用于 **/hana/shared**、 **/hana/data**和 **/hana/log** 卷的本机 NFS 共享。 对 **/hana/data** 和 **/hana/log** 卷使用基于和的 NFS 共享需要使用版本 4.1 nfs 协议。 如果在和上基于共享，则不支持使用 NFS 协议 v3 作为 **/hana/data** 和 **/hana/log** 卷。 


> [!IMPORTANT]
> **不**支持在 Azure NetApp 文件上实现的 NFS v3 协议适用于 **/hana/data**和 **/hana/log**。 从功能角度来看，NFS 4.1 对于 **/hana/data** 和 **/hana/log** 卷是必需的。 但对于 **/hana/shared** 卷，nfs V3 或 nfs v2.0 协议可从功能角度来使用。

## <a name="important-considerations"></a>重要注意事项

在考虑将 Azure NetApp 文件用于 SAP Netweaver 和 SAP HANA 时，请注意以下重要注意事项：

- 最小容量池为 4 TiB  
- 最小卷大小为 100 GiB
- Azure NetApp 文件和所有虚拟机（其中装入了 Azure NetApp 文件的卷）必须位于同一个 Azure 虚拟网络中，或位于同一区域中的 [对等互连虚拟网络](../../../virtual-network/virtual-network-peering-overview.md) 中
- 将虚拟机部署在靠近 Azure NetApp 存储的位置以降低延迟，这一点非常重要。  
- 所选虚拟网络必须具有子网，并委托给 Azure NetApp 文件
- 请确保度量从数据库服务器到和卷的延迟，并且低于1毫秒
- Azure NetApp 卷的吞吐量是卷配额和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量符合 HANA 系统要求
- 请尝试 "合并" 卷以在更大的卷中实现更高的性能，例如，将一个卷用于/sapmnt、/usr/sap/trans .。。 如果可能  
- Azure NetApp 文件提供[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)：你可以对允许的客户端、访问类型（读写、只读等）进行控制。 
- Azure NetApp 文件功能尚没有区域感知性。 当前，Azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。   
- 虚拟机上 sidadm 的用户 ID 和 `sapsys` 的组 ID 必须与 Azure NetApp 文件中的配置相匹配<b></b>。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

> [!IMPORTANT]
> 如果 <b>sid</b>Adm 的用户 id 与 `sapsys` 虚拟机和 Azure NetApp 配置之间的组 id 不匹配，则在 Azure netapp 卷上装载到 VM 的文件的权限将显示为 `nobody` 。 在[载入新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)到 Azure NetApp 文件时，请确保指定的 sidadm 的用户 ID 和 `sapsys` 的组 ID<b></b> 准确无误。


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的函数，如 [Azure NetApp 文件服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

重要的是，性能关系是大小，而 SVM (存储虚拟机) 的 LIF (逻辑接口) 的物理限制。

下表说明了创建大的 "标准" 卷来存储备份，并且创建大于 12 TB 的 "超" 卷是有意义的，因为将会超出单个 LIF 的物理带宽容量。 

LIF 和单个 Linux 会话的最大吞吐量介于1.2 到 1.4 GB/秒之间。 

| 大小  | 吞吐量标准 | 吞吐量高级 | 超吞吐量 |
| --- | --- | --- | --- |
| 1 TB | 每秒 16 MB | 64 MB/秒 | 128 MB/秒 |
| 2 TB | 32 MB/秒 | 128 MB/秒 | 256 MB/秒 |
| 4 TB | 64 MB/秒 | 256 MB/秒 | 512 MB/秒 |
| 10 TB | 160 MB/秒 | 640 MB/秒 | 1.280 MB/秒 |
| 15 TB | 240 MB/秒 | 960 MB/秒 | 1.400 MB/秒 |
| 20 TB | 320 MB/秒 | 1.280 MB/秒 | 1.400 MB/秒 |
| 40 TB | 640 MB/秒 | 1.400 MB/秒 | 1.400 MB/秒 |

必须了解的是，将数据写入存储后端中的相同 Ssd。 创建了容量池中的性能配额，以便能够管理该环境。
存储 Kpi 对于所有 HANA 数据库大小都相等。 在几乎所有情况下，此假设并不反映事实和客户预期。 HANA 系统的大小并不一定意味着小系统需要较低的存储吞吐量–并且大型系统需要高存储吞吐量。 但一般情况下，对于较大的 HANA 数据库实例，我们可能需要更高的吞吐量要求。 由于 SAP 的底层硬件的大小调整规则的结果，较大的 HANA 实例还在实例重新启动后加载数据等任务中提供了更多的 CPU 资源和更高的并行性。 因此，应将卷大小采用为客户的期望和要求。 并且不仅由纯容量要求驱动。

在 Azure 中设计 SAP 基础结构时，应注意到 (SAP) 的生产系统的最低存储吞吐量要求，这些要求转换为最小吞吐量特征：

| 卷类型和 i/o 类型 | SAP 所需的最小 KPI | 高级服务级别 | 超服务级别 |
| --- | --- | --- | --- |
| 日志卷写入 | 250 MB/秒 | 4 TB | 2 TB |
| 数据卷写入 | 250 MB/秒 | 4 TB | 2 TB |
| 数据量读取 | 400 MB/秒 | 6.3 TB | 3.2 TB |

由于这三个 Kpi 都是必需的，因此需要将 **/hana/data** 卷的大小调整为更大的容量，以满足最少的读取要求。

对于不需要高带宽的 HANA 系统，和的卷大小可能会较小。 如果 HANA 系统需要更高的吞吐量，则可以通过调整容量来调整卷的大小。 没有为备份卷定义 Kpi。 但是，备份卷吞吐量对于执行良好的环境是必不可少的。 日志和数据卷性能必须针对客户期望而设计。

> [!IMPORTANT]
> 无论在单个 NFS 卷上部署的容量如何，预计吞吐量都将 1.2-1.4 GB/秒的带宽范围（由虚拟机中的使用者利用）内稳定下来。 这与 ANF 产品/服务的基础体系结构以及围绕 NFS 的相关 Linux 会话限制有关。 [Azure NetApp 文件的性能基准测试结果](../../../azure-netapp-files/performance-benchmarks-linux.md)一文中记录的性能和吞吐量数针对具有多个客户端 VM 的一个共享 NFS 卷而执行，因此具有多个会话。 这种情况不同于我们在 SAP 中测量的情况。 我们针对 NFS 卷衡量单个 VM 的吞吐量的位置。 托管在和上。

为了满足数据和日志的 SAP 最小吞吐量要求，以及 **/hana/shared**的准则，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared 纵向扩展 | 最小 (1 TB，1 x RAM)   | 最小 (1 TB，1 x RAM)  | v3 或 v4.1 |
| /hana/shared 扩展 | 1 x RAM 的工作节点<br /> 每4个辅助角色节点  | 1 x RAM 的工作节点<br /> 每4个辅助角色节点  | v3 或 v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 或 v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 或 v4.1 |

对于所有卷，强烈建议使用 NFS v2。0

备份卷的大小为 "估计"。 需要根据工作负荷和操作过程来定义精确要求。 对于备份，你可以将不同 SAP HANA 实例的多个卷整合到一个 (或两个) 大的卷，这可能会降低服务级别的和。

> [!NOTE]
> 本文档中所述的 Azure NetApp 文件、大小调整建议针对 SAP 向其基础结构提供商提供的最低要求。 在实际客户部署和工作负载场景中，这可能还不够。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。  

因此，可以考虑对已为超级磁盘存储列出的 ANF 卷部署类似的吞吐量。 同时请考虑为不同 VM SKU 的卷列出的大小情况，正如超级磁盘表中所做的那样。

> [!TIP]
> 可以动态调整 Azure NetApp 文件卷的大小，而无需 `unmount` 卷、停止虚拟机或停止 SAP HANA。 这具有灵活性，可同时满足应用程序的预期和不可预见的吞吐量需求。

有关如何使用 ANF 中托管的 NFS v4.1 卷部署带备用节点的 SAP HANA 横向扩展配置的文档，已在以下位置发布：[使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上进行带备用节点的 SAP HANA 横向扩展](./sap-hana-scale-out-standby-netapp-files-suse.md)。


## <a name="availability"></a>可用性
和系统更新和升级在不影响客户环境的情况下应用。 定义的 [SLA 为 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/)。


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>卷和 IP 地址和容量池
对于和，必须了解底层基础结构的构建方式。 容量池只是一种结构，这使得为和创建计费模型变得更加简单。 容量池与底层基础结构没有物理关系。 如果创建容量池，则只会创建可进行收费的 shell，而不是更多。 创建卷时，将在多个 NetApp 系统的群集上创建第一个 SVM (存储虚拟机) 。 将为此 SVM 创建单个 IP 来访问卷。 如果创建多个卷，则通过此多控制器 NetApp 群集在此 SVM 中分配所有卷。 即使只获得一个 IP，数据也会分布在多个控制器上。 和有一个逻辑，该逻辑在已配置的存储的卷或/和容量达到内部预定义级别后自动分发客户工作负荷。 你可能会注意到，因为已分配新的 IP 地址来访问卷。

##<a name="log-volume-and-log-backup-volume"></a>日志卷和日志备份卷
 (**/hana/log**) "日志卷" 用于写入联机重做日志。 因此，此卷中有打开的文件，因此无法对此卷进行快照。 联机重做日志文件已满或执行重做日志备份后，联机重做日志文件将存档或备份到日志备份卷。 为了提供合理的备份性能，日志备份卷需要合理的吞吐量。 若要优化存储成本，可以合并多个 HANA 实例的日志备份量。 以便多个 HANA 实例利用同一卷，并将其备份写入不同的目录。 使用这样的合并，你可以获得更大的吞吐量，因为你需要使卷更大一些。 

同样适用于将完整的 HANA 数据库备份写入到的卷。  
 

## <a name="backup"></a>备份
除了对 [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)一文中所述的 SAP HANA 数据库进行流式备份和 azure 备份服务备份以外，Azure NetApp 文件也可以用于执行基于存储的快照备份。 

SAP HANA 支持：

- 基于存储的快照备份 SAP HANA 1.0 SPS7
- 基于存储的快照备份支持多数据库容器 (MDC) HANA 环境 SAP HANA 2.0 SPS4


创建基于存储的快照备份是一个简单的四步过程， 
1. 创建 HANA (内部) 数据库快照-你或工具需要执行的活动 
1. SAP HANA 将数据写入数据文件以在存储中创建一致的状态。创建 HANA 快照后，将执行此步骤
1. 在存储中的 **/hana/data** 卷上创建一个快照，即你或工具需要执行的步骤。 无需在 **/hana/log** 卷上执行快照
1. 删除 HANA (内部) 数据库快照并恢复正常操作-你或工具需要执行的步骤

> [!WARNING]
> 缺少最后一步或未能执行最后一个步骤会对 SAP HANA 的内存需求产生严重影响，并可能导致 SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA 的和快照备份](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA 第2部分的和快照备份](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

可以通过多种方式使用各种工具来管理此快照备份过程。 例如，GitHub 上提供的 python 脚本 ntaphana_azure "py" [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) 是示例代码，"按原样" 提供，没有任何维护或支持。



> [!CAUTION]
> 快照本身不是受保护的备份，因为它与你刚拍摄快照的卷位于同一物理存储中。 必须将至少一个快照每天 "保护" 到不同的位置。 这可以在同一环境、远程 Azure 区域或 Azure Blob 存储中完成。


对于 Commvault 备份产品的用户，第二个选项是 Commvault IntelliSnap 11.21 和更高版本。 此版本或更高版本的 Commvault 提供 Azure NetApp 文件支持。 [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html)一文提供了详细信息。


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>使用 Azure blob 存储备份快照
备份到 Azure blob 存储是一种经济高效且快速的方法，可用于保存基于和的 HANA 数据库存储快照备份。 若要将快照保存到 Azure Blob 存储，请首选 azcopy 工具。 下载此工具的最新版本并将其安装在安装了 GitHub 中的 python 脚本的 bin 目录中。
下载最新的 azcopy 工具：

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

最高级的功能是同步选项。 如果使用 SYNC 选项，azcopy 将保持源目录和目标目录同步。 参数的使用 **--删除-目标** 很重要。 如果没有此参数，则 azcopy 不会删除目标站点上的文件，并且目标端的空间利用率会增长。 在 Azure 存储帐户中创建块 Blob 容器。 然后，为 blob 容器创建 SAS 密钥，并将快照文件夹同步到 Azure Blob 容器。

例如，如果每日快照应同步到 Azure blob 容器以保护数据，则为。 只有一个快照应保留，可以使用以下命令。

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>后续步骤
阅读以下文章：

- [Azure 虚拟机的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)