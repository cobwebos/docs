---
title: 使用 Azure 中的文件共享对 WSFC 上的群集 SAP ASCS/SCS 进行分类 |Microsoft Docs
description: 了解如何使用 Azure 中的文件共享在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2df092d49f2dfe9153b52be677e8ee6314dd9b60
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982966"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>使用 Azure 中的文件共享在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例

> ![Windows][Logo_Windows] Windows
>

Windows Server 故障转移群集是 Windows 中高可用性 SAP ASCS/SCS 安装和 DBMS 的基础。

故障转移群集是由 1+n 个独立服务器（节点）构成的组，这些服务器配合工作以提高应用程序和服务的可用性。 如果发生节点故障，Windows Server 故障转移群集会计算可能发生的故障数并保留正常运行的群集以提供应用程序和服务。 可从不同的仲裁模式中选择，以实现故障转移群集。

## <a name="prerequisites"></a>必备条件
在开始本文所述的任务之前，请先查看此文：

* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 中的 Windows Server 故障转移群集

相比于裸机或私有云部署，Azure 虚拟机要求执行额外的步骤来配置 Windows Server 故障转移群集。 生成群集时，需要为 SAP ASCS/SCS 实例设置多个 IP 地址和虚拟主机名。

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure 中的名称解析和群集虚拟主机名

Azure 云平台不提供配置虚拟 IP 地址（例如浮动 IP 地址）的选项。 需要一个替代解决方案来设置虚拟 IP，以便连接到云中的群集资源。 

Azure 负载均衡器服务提供适用于 Azure 的内部负载均衡器**。 借助内部负载均衡器，客户端通过群集虚拟 IP 地址访问群集。 

在包含群集节点的资源组中部署内部负载均衡器。 然后，使用内部负载均衡器的探测端口配置所有必要的端口转发规则。 客户端可以通过虚拟主机名连接。 DNS 服务器解析群集 IP 地址。 内部负载均衡器处理目标为群集活动节点的端口转发。

![图 1：Azure 中未使用共享磁盘的 Windows Server 故障转移群集配置][sap-ha-guide-figure-1001]

_图 1：Azure 中未使用共享磁盘的 Windows Server 故障转移群集配置_****

## <a name="sap-ascsscs-ha-with-file-share"></a>采用文件共享的 SAP ASCS/SCS HA

SAP 开发了可以取代群集共享磁盘的新方法和新方案，用于将 Windows 故障转移群集上的 SAP ASCS/SCS 实例群集化。 可以使用 SMB 文件共享来部署 SAP 全局主机文件，不必使用群集共享磁盘。

> [!NOTE]
> 在将 SAP ASCS/SCS 实例群集化时，可以使用 SMB 文件共享来替代群集共享磁盘。  
>

此体系结构的具体特征如下：

* SAP 中心服务（具有自身的文件结构以及消息和排队进程）与 SAP 全局主机文件相互独立。
* SAP 中心服务在 SAP ASCS/SCS 实例下运行。
* SAP ASCS/SCS 实例已群集化，可以通过 \<ASCS/SCS 虚拟主机名\> 虚拟主机名来访问。
* SAP 全局文件放置在 SMB 文件共享中，可使用 \<SAP 全局主机\> 主机名: \\\\&lt;SAP 全局主机&gt;\sapmnt\\&lt;SID&gt;\SYS\... 进行访问。
* SAP ASCS/SCS 实例安装在两个群集节点的本地磁盘上。
* \<ASCS/SCS 虚拟主机名\> 网络名称不同于 &lt;SAP 全局主机&gt;。

![图 2：采用 SMB 文件共享的 SAP ASCS/SCS HA 体系结构][sap-ha-guide-figure-8004]

**图 2：** 采用 SMB 文件共享的新 SAP ASCS/SCS HA 体系结构__

SMB 文件共享的先决条件：

* SMB 3.0（或更高版本）协议。
* 能够设置 Active Directory 用户组和 `computer$` 计算机对象的 Active Directory 访问控制列表 (ACL)。
* 必须为文件共享启用 HA：
    * 用于存储文件的磁盘不能是单一故障点。
    * 服务器或 VM 停机不会导致文件共享故障。

SAP \<SID\> 群集角色不包含群集共享磁盘或通用文件共享群集资源。


![图 3：使用文件共享所需的 SAP \<SID\> 群集角色资源][sap-ha-guide-figure-8005]

**图 3：** 使用文件共享所需的 SAP &lt;SID&gt; 群集角色资源__


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>在 Azure 中用作 SAPMNT 文件共享且具有存储空间直通功能的横向扩展文件共享

可以使用横向扩展文件共享来托管和保护 SAP 全局主机文件。 横向扩展文件共享还提供高度可用的 SAPMNT 文件共享服务。

![图 4：用于保护 SAP 全局主机文件的横向扩展文件共享][sap-ha-guide-figure-8006]

**图 4：** 用于保护 SAP 全局主机文件的横向扩展文件共享__

> [!IMPORTANT]
> Microsoft Azure 云和本地环境均完全支持横向扩展文件共享。
>

横向扩展文件共享提供高度可用且可横向缩放的 SAPMNT 文件共享。

存储空间直通用作横向扩展文件共享的共享磁盘。 可以借助存储空间直通，使用具有本地存储的服务器构建高度可用且可缩放的存储。 用于横向扩展文件共享（例如 SAP 全局主机文件）的共享存储不是单一故障点。

选择存储空间直通时，请考虑以下用例：

- 需要在 Azure 可用性集中部署用于构建存储空间直通群集的虚拟机。
- 对于存储空间直通群集的灾难恢复，可以使用[Azure Site Recovery 服务](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#replicated-machines---storage)。
- 不支持将存储空间直通群集延伸到不同的 Azure 可用性区域。

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Azure 中的横向扩展文件共享的 SAP 先决条件

若要使用横向扩展文件共享，系统必须满足以下要求：

* 至少有两个群集节点用于横向扩展文件共享。
* 每个节点必须至少包含两个本地磁盘。
* 出于性能原因，必须使用** 镜像复原：
    * 双向镜像适用于两个群集节点的横向扩展文件共享。
    * 三向镜像适用于三个（或三个以上）群集节点的横向扩展文件共享。
* 建议使用三个（或三个以上）群集节点进行横向扩展文件共享，启用三向镜像。
    与使用两个群集节点和双向镜像的横向扩展文件共享设置相比，这种设置提供的可伸缩性和存储复原能力更高。
* 必须使用 Azure 高级磁盘。
* 建议使用 Azure 托管磁盘。
* 建议使用复原文件系统 (ReFS) 来格式化卷。
    * 有关详细信息，请参阅“在存储空间直通中规划卷”一文的 [SAP 说明 1869038 - SAP 支持 ReFs 文件系统][1869038]和[选择文件系统][planning-volumes-s2d-choosing-filesystem]章节。
    * 请确保安装 [Microsoft KB4025334 累积更新][kb4025334]。
* 可以使用“DS 系列”或“DSv2 系列”Azure VM 大小。
* 若要获得良好的 VM 间网络性能以顺利进行存储空间直通磁盘同步，请使用至少能够提供“高”网络带宽的 VM 类型。
    有关详细信息，请参阅 [DSv2 系列][dv2-series]和 [DS 系列][ds-series]规格。
* 建议在存储池中保留一些未分配的容量。 在存储池中留一些未分配的容量可以使卷空间能够在驱动器故障时进行“就地”修复。 这样可提高数据安全性和性能。  有关详细信息，请参阅[选择卷大小][choosing-the-size-of-volumes-s2d]。
* 不需针对横向扩展文件共享网络名称（例如 \<SAP 全局主机\>）来配置 Azure 内部负载均衡器。 此操作针对 SAP ASCS/SCS 实例的 \<ASCS/SCS 虚拟主机名\>，或者针对 DBMS。 横向扩展文件共享将负载横向扩展到所有群集节点。 \<SAP 全局主机\> 将本地 IP 地址用于所有群集节点。


> [!IMPORTANT]
> 不能重命名指向 \<SAP 全局主机\> 的 SAPMNT 文件共享。 SAP 仅支持共享名“sapmnt”。
>
> 有关详细信息，请参阅 [SAP 说明 2492395 - 是否可以更改共享名 sapmnt？][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>在两个群集中配置 SAP ASCS/SCS 实例和横向扩展文件共享

可以在一个群集中部署 SAP ASCS/SCS 实例，这些实例具有自身的 SAP \<SID\> 群集角色。 这种情况下，请在另一群集中配置横向扩展文件共享，分配另一群集角色。

> [!IMPORTANT]
>在本方案中，SAP ASCS/SCS 实例配置为使用 UNC 路径 \\\\&lt;SAP 全局主机&gt;\sapmnt\\&lt;SID&gt;\SYS\. 来访问 SAP 全局主机
>

![图 5：在两个群集中部署的 SAP ASCS/SCS 实例和横向扩展文件共享][sap-ha-guide-figure-8007]

**图 5：** 在两个群集中部署的 SAP ASCS/SCS 实例和横向扩展文件共享__

> [!IMPORTANT]
> 在 Azure 云中，用于 SAP 和横向扩展文件共享的每个群集都必须部署在其自己的 Azure 可用性集中或跨 Azure 可用性区域。 这样可确保将群集 VM 分散放置在其下的 Azure 基础结构中。 此技术支持可用性区域部署。
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>将 SIOS DataKeeper 用作群集共享磁盘的通用文件共享


通用文件共享是实现高可用性文件共享的另一个选项。

这种情况下，可将第三方 SIOS 解决方案用作群集共享磁盘。

## <a name="next-steps"></a>后续步骤

* [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP HA 的 Azure 基础结构][sap-high-availability-infrastructure-wsfc-file-share]
* [针对 SAP ASCS/SCS 实例在 Windows 故障转移群集和文件共享上安装 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]
* [在 Azure 中为 UPD 存储部署双节点存储空间直通横向扩展文件服务器][deploy-sofs-s2d-in-azure]
* [Windows Server 2016 中的存储空间直通][s2d-in-win-2016]
* [深入探讨：存储空间直通中的卷][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 多 SID 高可用性配置)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 
