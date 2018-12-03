---
title: 常见问题解答 - Avere vFXT for Azure
description: 有关 Avere vFXT for Azure 的常见问题解答
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 21b5dae6fda857a3a69aa06e461fdf3b716b5eb8
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163311"
---
# <a name="avere-vfxt-for-azure-faq"></a>Avere vFXT for Azure FAQ

本文回答了系列问题，帮助你确定 Avere vFXT 解决方案是否满足所需。 本文提供了有关 Avere vFXT 的功能的基本信息，并解释了它如何与其他 Azure 组件以及来自外部供应商的产品一起工作。 

## <a name="general"></a>常规 

### <a name="what-is-avere-vfxt-for-azure"></a>什么是 Avere vFXT for Azure？

Avere vFXT for Azure 是高性能的文件系统，可在 Azure 计算中缓存活动数据，以便有效地处理关键工作负荷。

### <a name="is-the-avere-vfxt-a-storage-solution"></a>Avere vFXT 是存储解决方案吗？

不是。 Avere vFXT 是文件系统缓存，它附加到 EMC 或 NetApp NAS 或 Blob 容器等存储环境。 vFXT 高效处理来自客户端的数据请求，并缓存它所服务的数据，以便随着时间推移大幅度提高性能。 vFXT 本身不存储数据。 它没有关于其后所存储的数据量的信息。

### <a name="is-the-avere-vfxt-a-tiering-solution"></a>Avere vFXT 是分层的解决方案吗？

Avere vFXT 不会在热存储层和冷存储层之间自动对数据执行分层。  

### <a name="how-do-i-know-if-an-environment-is-right-for-the-avere-vfxt"></a>如何知道环境是否适合 Avere vFXT 运行？

想了解这个问题，不如问“工作负荷是否可以缓存？” 也就是说，工作负荷是否具有高读写比 - 例如，80/20 或 70/30 的读取/写入。

如果你有基于文件的分析管道，并且它在大量 Azure 虚拟机之间运行，还满足以下一个或多个条件，请考虑使用 Avere vFXT for Azure：

* 由于文件访问时间长（数十毫秒或几秒，具体取决于要求），整体性能缓慢或不一致。 最终客户无法接受这种延迟。

* 需要处理的数据位于 WAN 环境的远端且永久移动该数据并不现实。 数据可能位于不同的 Azure 区域或客户数据中心。

* 大量的客户端在请求数据 - 例如，高性能计算 (HPC) 群集中的数据。 大量的并发请求数会增加延迟。

* 客户想要在 Azure 虚拟机上“按原样”运行其当前管道，并且需要基于 POSIX 的共享存储（或缓存）解决方案以实现可缩放性。 通过使用 Avere vFXT for Azure，无需重新构建工作管道即可对 Azure Blob 存储进行本机调用。

* HPC 应用程序基于 NFSv3 客户端。 （在某些情况下，可以使用 SMB 2.1 客户端，但性能有限。）

   下图尝试简化对此问题的解答。 工作流越靠近右上角，Avere 缓存解决方案就越有可能适合你的环境。

   ![图表显示具有数千个客户端的读取密集型负载更适合 Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Avere vFXT 解决方案在哪种规模的客户端最有意义？

vFXT 缓存解决方案旨在处理数百、数千或成千上万的计算核心。 如果只有几台运行轻型工作的计算机，Avere vFXT 解决方案不适用。

典型的 Avere vFXT 客户运行要求很高的工作负荷，起点为 1,000 个 CPU 核心左右。 这些环境可以达到 50,000 个核心或更多。 由于 vFXT 是可缩放的，因此可以随着工作负荷变得越发要求更多吞吐量或更多 IOPS 来添加节点以支持这些工作负荷。

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Avere vFXT 环境可存储多少数据？

Avere vFXT 是一种缓存，不专门存储数据。 它联合使用 RAM 和 SSD 来存储缓存的数据。 数据永久存储在后端存储系统（例如，NetApp NAS 系统或 Blob 容器）上。 Avere vFXT 系统没有关于其后所存储的数据量的信息；vFXT 仅缓存客户端请求的数据的子集。  

### <a name="what-regions-are-supported"></a>哪些区域受支持？

从 2018 年 11 月 1 日起，除主权区域（中国、德国）和政府领域外，所有地区都支持 Avere vFXT for Azure。 请确保要使用的区域可以支持大量的计算核心以及创建 Avere vFXT 群集所需的 VM 实例。 主权区域和政府云尚未得到支持。

### <a name="how-do-i-get-help-with-the-avere-vfxt"></a>如何获取 Avere vFXT 的相关帮助？

专门的支持组提供了有关 Avere vFXT for Azure 的帮助。 按照[获取系统帮助](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)中的说明从 Azure 门户打开支持票证。 

### <a name="is-the-avere-vfxt-highly-available"></a>Avere vFXT 是否高度可用？

是的，Avere vFXT 专门作为 HA 解决方案运行。

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT for Azure 是否也支持其他云服务？

是的，客户可以在 Avere vFXT 集群中使用多个云提供商的产品/服务。 它支持 AWS S3 标准存储桶和 Google Cloud Services 标准存储桶以及 Azure Blob 容器。 

> [!NOTE] 
> 在 AWS 或 Google 云中使用 Avere vFXT 需要支付软件费用，但在 Azure 中则无需付费。

## <a name="technical---compute"></a>技术方面 - 计算

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>可否描述下 Avere vFXT 环境是什么样的？

Avere vFXT 是由多个 Azure 虚拟机组成的集群设备。 Python 库可处理群集创建、删除和修改。 请参阅[什么是 Avere vFXT for Azure](avere-vfxt-overview.md)，了解更多信息。 

### <a name="what-kind-of-azure-virtual-machines-does-the-avere-vfxt-run-on"></a>Avere vFXT 在哪种 Azure 虚拟机上运行？  

Avere vFXT for Azure 群集使用 Microsoft Azure E32s_v3 或 D16s_v3 虚拟机。 

### <a name="can-i-mix-and-match-virtual-machine-types-for-my-cluster"></a>可否为我的群集混搭虚拟机类型？

否，只能选择一种虚拟机类型或另一种。
    
### <a name="can-i-move-between-virtual-machine-types"></a>可否虚拟机类型之间切换？

是，存在从一种 VM 类型迁移到另一种 VM 类型的途径。 [打开支持票证](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)，了解如何操作。

### <a name="does-the-avere-vfxt-environment-scale"></a>Avere vFXT 环境是否可以缩放？

Avere vFXT 群集可以小到三个虚拟机节点或大到 24 个节点。 如果你认为需要超过九个节点的群集，请与 Azure 技术支持部门联系以帮助规划。 较大数量的节点需要更大的部署体系结构。

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Avere vFXT 环境是否“自动缩放”？

不是。 可以缩放群集大小，但添加或删除群集节点要手动操作。

### <a name="can-i-run-the-vfxt-cluster-as-a-virtual-machine-scale-set"></a>可否将 vFXT 群集作为虚拟机规模集运行？

Avere vFXT 不支持虚拟机规模集 (VMSS) 部署。 仅针对参与群集的原子 VM 设计了几种内置的可用性支持机制。  

### <a name="can-i-run-the-vfxt-cluster-on-low-priority-vms"></a>可否在低优先级 VM 中运行 vFXT 群集？

否，系统要求一组稳定的基础虚拟机。

### <a name="can-i-run-the-vfxt-cluster-in-containers"></a>可否在容器中运行 vFXT 群集？

否，必须将 Avere vFXT 部署为独立应用程序。

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Avere vFXT VM 是否会消耗计算配额？

是的。 请确保在区域中有足够的配额以支持群集。  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>是否可以在不同的可用性区域中运行 Avere vFXT 群集计算机？

不是。 Avere vFXT 中使用的高可用性模型目前不支持位于不同的可用区域中的单个 vFXT 集群成员。

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>可否可以克隆 Avere vFXT 虚拟机？

否，必须使用受支持的 Python 脚本来添加或删除 Avere vFXT 群集中的节点。 有关详细信息，请参阅[管理 Avere vFXT 群集](avere-vfxt-manage-cluster.md)。  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>有没有可以在自己的本地环境中运行的“VM”的版本的软件？

否，该系统是作为群集设备提供，并在特定的虚拟机类型上进行测试。 此限制可帮助客户避免创建无法支持典型 Avere vFXT 工作流的高性能要求的系统。 

## <a name="technical---disks"></a>技术方面 - 磁盘

### <a name="what-type-of-disks-are-supported-for-the-azure-vms"></a>Azure VM 支持哪种类型的磁盘？

Avere vFXT for Azure 可以使用 1 TB 或 4 TB 的高级 SSD 配置。 高级 SSD 配置可部署为多个托管磁盘。

### <a name="does-the-cluster-support-unmanaged-disks"></a>群集是否支持非托管磁盘？

否，群集需要托管磁盘。

### <a name="does-the-system-support-local-attached-ssds"></a>系统是否支持本地（附加）SSD？

Avere vFXT for Azure 当前不支持本地 SSD。 用于 Avere vFXT 的磁盘必须能够关闭并重启，但只是此配置中的本地附加 SSD 能够被终止。

### <a name="does-the-system-support-ultra-ssds"></a>系统是否支持超级 SSD？

否，系统仅支持高级 SSD 配置。

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>可否分离高级 SSD 并稍后重新附加他们以保留使用之间的缓存内容？

分离和重新附加 SSD 不受支持。 源上的元数据或文件内容可能在使用之间发生了更改，这可能会导致数据完整性问题。

### <a name="does-the-system-encrypt-the-cache"></a>系统是否加密缓存？

数据在磁盘间进行条带化，但未加密。 但是，磁盘本身可以加密。 可在[此处](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption)找到更多信息。

## <a name="technical---networking"></a>技术方面 - 网络

### <a name="what-network-is-recommended"></a>建议使用哪种网络？

如果将本地存储用于 Avere vFXT，则应具有 1 Gbp 或更好的网络连接。 如果有少量数据且愿意在运行作业之前将数据复制到云，则 VPN 连接可能足够。 

> [!TIP] 
> 网络链接越慢，初始冷读取的速度就越慢。 缓慢的读取操作会增加工作管道的延迟。 

### <a name="can-i-run-the-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>是否可以在不同于计算群集的虚拟网络中运行 Avere vFXT？

是，可在不同的虚拟网络中创建 Avere vFXT 系统。 请参阅[规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)，了解详细信息。

### <a name="does-the-avere-vfxt-require-its-own-subnet"></a>Avere vFXT 是否需要自己的子网？

是的。 Avere vFXT 严格作为 HA 群集运行且需要多个 IP 地址以进行操作。 如果群集位于自己的子网中，则可以避免 IP 地址冲突的风险，冲突可能导致安装和正常操作出现问题。 只要 IP 地址没有重叠，群集的子网就可以位于现有的 VNet 中。

### <a name="can-i-run-the-avere-vfxt-on-infiniband"></a>可否在 Infiniband 上运行 Avere vFXT？

否，Avere vFXT 仅使用以太网/IP。

### <a name="how-do-i-access-my-on-premises-nas-environment-from-the-avere-vfxt"></a>如何从 Avere vFXT 访问本地 NAS 环境？

Avere vFXT 环境与任何其他 Azure VM 没有什么不同，因为它需要通过网络网关或 VPN 路由访问客户数据中心（并返回）。 如果在你的环境中可用，请考虑使用 Azure ExpressRoute 连接。

### <a name="what-are-the-bandwidth-requirements-for-the-avere-vfxt"></a>Avere vFXT 带宽要求是什么？

整体的带宽要求取决于两个因素： 

* 正从源请求的数据量 
* 客户端系统在初始数据加载期间对延迟的容忍度  

对于易受延迟影响的环境，应使用最低链接速度为 1 Gbp 的纤程解决方案。 如果可用，请使用 ExpressRoute。  

### <a name="can-i-run-the-vfxt-with-public-ip-addresses"></a>可否使用公共 IP 地址运行 vFXT？

否，vFXT 旨在在采用最佳做法的安全网络环境中运行。  

## <a name="technical---backend-storage-core-filers"></a>技术层面 - 后端存储（核心文件管理器）

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>单个 Avere vFXT 环境支持多少核心文件管理器？

Avere vFXT 群集支持多达 20 个核心文件管理器。 

### <a name="how-does-the-avere-environment-store-data"></a>Avere 环境如何存储数据？

Avere vFXT 不是存储。 它是用于从多个存储目标（称为核心文件管理器）读取和写入数据的缓存。 Avere vFXT 的高级 SSD 盘上存储的数据是瞬态的，最终冲往后端核心文件管理器存储。

### <a name="which-core-filers-does-avere-vfxt-support"></a>Avere vFXT 支持哪些核心文件管理器？

概括地说，Avere vFXT for Azure 支持以下系统作为核心文件管理器： 

* Dell EMC Isilon（OneFS 7.1、7.2、8.0 和 8.1） 
* NetApp ONTAP（群集模式 9.4、9.3、9.2、9.1P1、8.0 - 8.3）和（7-Mode 7.*、8.0 - 8.3） 
* Azure Blob 容器（仅 LRS） 
* AWS S3 存储桶 
* Google Cloud 存储桶

### <a name="why-doesnt-the-avere-vfxt-support-all-nfs-filers"></a>为何 Avere vFXT 不支持所有 NFS 文件管理器？

尽管所有 NFS 平台都满足相同的 IETF 标准，但实际上每种实施有其自己的特点。 这些细节会影响 Avere vFXT 与存储系统的交互方式。 受支持的系统是市场上使用最广泛的平台。

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT 是否支持专用对象存储（例如 Swiftstack）？

Avere vFXT 不支持专用对象存储。

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>如何在支持下获得特定存储产品？

支持基于领域中的需求量。 如果有足够的基于收入的请求希望支持给定的 NAS 解决方案，则将考虑该请求。 通过 Azure 支持部门发出请求。

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>可否将 Azure Blob 存储用作核心文件管理器？

是，Avere vFXT for Azure 可以使用块 Blob 容器作为云核心文件管理器。  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Blob 核心文件管理器的存储帐户要求有哪些？

存储帐户必须是常规用途 v2 (GPv2) 帐户且仅配置为本地冗余存储 (LRS)。 GRS 和 ZRS 不受支持。

### <a name="can-i-use-archive-blob-storage"></a>可否使用存档 Blob 存储？

不是。 存档存储的 SLA 与 vFXT 系统的实时目录和文件访问需求不兼容。 

### <a name="can-i-use-cool-blob-storage"></a>可否使用冷 Blob 存储？

可以使用冷存储层，但请注意操作率会高得多。 

### <a name="how-do-i-encrypt-the-blob-container"></a>如何对 Blob 容器进行加密？

可以在 Azure（首选）或 vFXT 核心文件管理器级配置 Blob 加密。  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>可否将自己的加密密钥用于 Blob 核心文件管理器？

默认情况下，使用 Azure Blob、表、文件和队列的 Microsoft 托管密钥对数据进行加密。 可以使用自己的密钥对 Azure Blob 和文件进行加密。 如果选择使用 vFXT 加密，则必须使用 Avere 生成密钥并将其存储在本地。 

## <a name="purchasing"></a>购买

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>如何获取 Avere vFXT for Azure 许可？

通过 Azure 市场可轻松获得 Avere vFXT for Azure 许可证。 注册 Azure 帐户，然后按照[部署 vFXT 群集](avere-vfxt-deploy.md)中的说明创建 Avere vFXT 群集。 

### <a name="how-much-does-the-avere-vfxt-cost"></a>Avere vFXT 的价格是多少？

在 Azure 中使用 Avere vFXT 群集无需支付额外的许可费用。 客户为存储和其他 Azure 使用付费。

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Avere vFXT VM 可否作为低优先级运行？

否，Avere vFXT 群集需要“始终可用”服务。 可以在不需要时关闭群集。 

## <a name="next-steps"></a>后续步骤

要开始使用 Avere vFXT for Azure，请参阅以下链接，了解如何规划和部署自己的系统：

* [规划 Avere vFXT 系统](avere-vfxt-deploy-plan.md)
* [部署概述](avere-vfxt-deploy-overview.md)
* [准备创建 Avere vFXT](avere-vfxt-prereqs.md)
* [部署 vFXT 群集](avere-vfxt-deploy.md)

要了解有关 Avere vFXT 功能和用例的详细信息，请访问 [Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/)。
