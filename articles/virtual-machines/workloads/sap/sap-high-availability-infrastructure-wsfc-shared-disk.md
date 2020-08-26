---
title: 用于 SAP ASCS/SCS 和 WSFC&共享磁盘的 Azure 基础结构 |Microsoft Docs
description: 了解如何针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/13/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6eaabac35676e9c836d07ffe6942ef9fc8c20cd8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855397"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>针对 SAP ASCS/SCS 使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows OS][Logo_Windows] Windows


本文介绍了在 Windows 故障转移群集上使用 *群集共享磁盘* 作为群集化 sap ASCS 实例的选项来准备 Azure 基础结构以在 Windows 故障转移群集上安装和配置高可用性 SAP ASCS/SCS 实例所采取的步骤。
文档中提供了两种 *群集共享磁盘* 备选方案：

- [Azure 共享磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)
- 使用 [SIOS DataKeeper 群集 Edition](https://us.sios.com/products/datakeeper-cluster/) 创建将模拟群集共享磁盘的镜像存储 

所提供的配置依赖于 [Azure 邻近组 (PPG) ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) ，以实现 SAP 工作负荷的最佳网络延迟。 文档不涵盖数据库层。  

> [!NOTE]
> Azure 近程放置组是使用 Azure 共享磁盘的先决条件。
 

## <a name="prerequisites"></a>先决条件

在开始安装之前，请查看以下文章：

* [体系结构指南：使用群集共享磁盘在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>创建 ASCS Vm

对于 SAP ASCS/SCS 群集，请在 Azure 可用性集中部署两个 Vm。 将 Vm 部署在相同的邻近位置组中。 部署 Vm 后：  
- 为 SAP ASCS/SCS 实例创建 Azure 内部负载均衡器 
- 将 Windows Vm 添加到 AD 域

提供的方案的主机名和 IP 地址是：

| 主机名角色 | 主机名 | 静态 IP 地址 | 可用性集 | 邻近位置组 |
| --- | --- | --- |---| ---|
| 第一个群集节点 ASCS/SCS 群集 |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| 第二个群集节点 ASCS/SCS 群集 |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| 群集网络名称 | pr1clust |本例为 10.0.0.42 (**仅** 适用于 Win 2016 群集)  | 不适用 | 不适用 |
| ASCS 群集网络名称 | pr1-ascscl |10.0.0.43 | 不适用 | 不适用 |
| ERS 群集网络名称 (**仅** 适用于 ERS2)  | pr1-erscl |10.0.0.44 | 不适用 | 不适用 |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> 创建 Azure 内部负载均衡器

SAP ASCS、SAP SCS 和新的 SAP ERS2 使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要使用虚拟 IP 地址的 [负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 。 强烈建议使用 [标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 


以下列表显示了 () SCS/ERS 负载均衡器的配置。 SAP ASCS 和 ERS2 在同一 Azure 负载均衡器中的配置。  

**(A)SCS**
- 前端配置
    - 静态 ASCS/SCS IP 地址 **10.0.0.43**
- 后端配置  
    添加所有虚拟机，这些虚拟机应作为 () SCS/ERS 群集的一部分。 在此示例中，Vm **pr1-ascs-10** 和 **pr1-ascs**。
- 探测端口
    - 端口 620**nr** 保留协议 (默认选项 "TCP) ，Interval (5) ，不正常阈值 (2) 
- 负载均衡规则
    - 如果使用“标准负载均衡器”，请选择“HA 端口”
    - 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
        - 32**nr** TCP
        - 36**nr** TCP
        - 39**nr** TCP
        - 81**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 请确保 "空闲超时" (分钟) 设置为 "最大值 30"，并启用 "浮动 IP (直接服务器返回) "。

**ERS2**

作为排队复制服务器 2 (ERS2) 也是群集的，除以上 SAP ASCS/SCS IP 外，还必须在 Azure ILB 上配置 ERS2 虚拟 IP 地址。 本部分仅适用于使用排队复制服务器2体系结构的情况。  
- 第二个前端配置
    - 静态 SAP ERS2 IP 地址 **10.0.0.44**

- 后端配置  
  Vm 已添加到 ILB 后端池。  

- 第二个探测端口
    - 端口 621**nr**  
    保留协议 (的默认选项 "TCP) ，Interval (5) ，不正常阈值 (2) 

- 第二级负载平衡规则
    - 如果使用“标准负载均衡器”，请选择“HA 端口”
    - 如果使用“基本负载均衡器”，请为以下端口创建负载均衡规则
        - 32**nr** TCP
        - 33**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - 请确保 "空闲超时" (分钟) 设置为 "最大值 30"，并启用 "浮动 IP (直接服务器返回) "。


> [!TIP]
> 使用 azure [ASCS/SCS 实例的适用于 WSFC FOR SAP/SCS 实例的 azure 资源管理器模板](https://github.com/robotechredmond/301-shared-disk-sap)，你可以使用 Azure 共享磁盘通过 ERS1 为一个 sap SID 自动完成基础结构准备工作。  
> Azure ARM 模板将创建两个 Windows 2019 或 2016 Vm，创建 Azure 共享磁盘并将其附加到 Vm。 将创建并配置 Azure 内部负载均衡器。 有关详细信息，请参阅 ARM 模板。 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> 在 ASCS/SCS 实例的两个群集节点上添加注册表项

如果连接空闲一段时间并且超过空闲超时，Azure 负载均衡器可能会关闭连接。 一旦需要发送第一个排队/取消排队请求，SAP 工作就会立即处理与 SAP 排队进程的连接。 若要避免中断这些连接，请在两个群集节点上更改 TCP/IP KeepAliveTime 和 KeepAliveInterval 值。 如果使用 ERS1，则还需要添加 SAP 配置文件参数，如本文后面部分所述。
必须在两个群集节点上更改以下注册表项：

- KeepAliveTime
- KeepAliveInterval

| 路径| 变量名称 | 变量类型  | 值 | 文档 |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD（十进制） |120000 |[KeepAliveTime](https://technet.microsoft.com/library/cc957549.aspx) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD（十进制） |120000 |[KeepAliveInterval](https://technet.microsoft.com/library/cc957548.aspx) |


若要应用更改，请重新启动两个群集节点。
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 将 Windows Vm 添加到域
将静态 IP 地址分配给虚拟机后，将虚拟机添加到域。 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> 安装和配置 Windows 故障转移群集 

### <a name="install-the-windows-failover-cluster-feature"></a>安装 Windows 故障转移群集功能

在其中一个群集节点上运行以下命令：

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

功能安装完成后，请重新启动两个群集节点。  

### <a name="test-and-configure-windows-failover-cluster"></a>测试和配置 Windows 故障转移群集 

在 Windows 2019 上，群集将自动识别它在 Azure 中运行，作为群集管理 IP 的默认选项，它将使用分布式网络名称。 因此，它将使用任何群集节点的本地 IP 地址。 因此，群集不需要专用 (虚拟) 网络名称，并且无需在 Azure 内部负载均衡器上配置此 IP 地址。

有关详细信息，请参阅 [Windows Server 2019 故障转移群集新功能](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) 在其中一个群集节点上运行以下命令：

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>配置群集云仲裁
使用 Windows Server 2016 或2019时，建议配置 [Azure 云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)作为群集仲裁。

在其中一个群集节点上运行以下命令：

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>优化 Windows 故障转移群集阈值
 
成功安装 Windows 故障转移群集后，需调整某些阈值，使其适用于在 Azure 中部署的群集。 [调整故障转移群集网络阈值](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834)中记录了要更改的参数。 假设构成 ASCS/SCS 的 Windows 群集配置的两个 VM 位于同一子网中，则需要将以下参数更改为这些值：
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

这些设置已经过客户测试，并且提供了很好的折衷方案。 它们具有足够的弹性，但它们还提供了足够快速的故障转移，以满足 SAP 工作负荷或 VM 故障中的实际错误条件。  

## <a name="configure-azure-shared-disk"></a>配置 Azure 共享磁盘
本部分仅适用于使用 Azure 共享磁盘的情况。 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>创建 Azure 共享磁盘并将其附加到 PowerShell
在其中一个群集节点上运行此命令。 需要调整资源组、Azure 区域、SAPSID 等的值。  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>用 PowerShell 格式化共享磁盘
1. 获取磁盘号。 在其中一个群集节点上运行以下 PowerShell 命令：

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. 格式化该磁盘。 在此示例中，它是第2个磁盘。 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. 验证磁盘现在是否作为群集磁盘显示。  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. 在群集中注册磁盘。  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> SAP ASCS/SCS 群集共享磁盘的 SIOS DataKeeper 群集版本
仅当使用第三方软件 SIOS DataKeeper Cluster Edition 创建模拟群集共享磁盘的镜像存储时，此部分才适用。  

现在，你可以在 Azure 中使用 Windows Server 故障转移群集配置。 若要安装 SAP ASCS/SCS 实例，需要一个共享磁盘资源。 其中一个选项是使用 SIOS DataKeeper Cluster Edition 是可用于创建共享磁盘资源的第三方解决方案。  

为 SAP ASCS/SCS 群集共享磁盘安装 SIOS DataKeeper Cluster Edition 的过程包括以下任务：
- 如果需要，请添加 Microsoft .NET 框架。 https://us.sios.com/products/datakeeper-cluster/)有关最新 .net framework 要求，请参阅 [SIOS 文档] ( # B1 
- 安装 SIOS DataKeeper
- 配置 SIOS DataKeeper

### <a name="install-sios-datakeeper"></a>安装 SIOS DataKeeper
在群集中的每个节点上安装 SIOS DataKeeper Cluster Edition。 要创建包含 SIOS DataKeeper 的虚拟共享存储，请创建同步的镜像，并模拟群集共享存储。

在安装 SIOS 软件之前，请创建 DataKeeperSvc 域用户。

> [!NOTE]
> 将 DataKeeperSvc 域用户添加到两个群集节点上的“本地管理员”组中。
>

1. 在两个群集节点上安装 SIOS 软件。

   ![SIOS 安装程序][sap-ha-guide-figure-3030]

   ![图 31：SIOS DataKeeper 安装程序的第一页][sap-ha-guide-figure-3031]

   _SIOS DataKeeper 安装的第一页_

2. 在对话框中，选择“是”****。

   ![图 32：DataKeeper 通知将会禁用服务][sap-ha-guide-figure-3032]

   _DataKeeper 通知你将禁用某个服务_

3. 在对话框中，建议选中“域或服务器帐户”****。

   ![图 33：用户为 SIOS DataKeeper 选中的选项][sap-ha-guide-figure-3033]

   _用户为 SIOS DataKeeper 选中的选项_

4. 输入为 SIOS DataKeeper 创建的域帐户用户名和密码。

   ![图 34：输入域用户名和密码以安装 SIOS DataKeeper][sap-ha-guide-figure-3034]

   _输入域用户名和密码以安装 SIOS DataKeeper_

5. 如图 35 所示，安装 SIOS DataKeeper 实例的许可证密钥。

   ![图 35：输入 SIOS DataKeeper 许可证密钥][sap-ha-guide-figure-3035]

   _输入 SIOS DataKeeper 许可证密钥_

6. 根据提示重新启动虚拟机。

### <a name="configure-sios-datakeeper"></a>配置 SIOS DataKeeper
在两个节点上安装 SIOS DataKeeper 之后，开始进行配置。 配置的目的是在连接到每个虚拟机的附加磁盘之间进行同步数据复制。

1. 启动 DataKeeper 管理和配置工具，并选择“连接服务器”。

   ![图 36：SIOS DataKeeper 管理和配置工具][sap-ha-guide-figure-3036]

   _SIOS DataKeeper 管理和配置工具_

2. 输入管理和配置工具应连接到的第一个节点的名称或 TCP/IP 地址，并在第二个步骤中，插入第二个节点的相关数据。

   ![图 37：插入管理和配置工具应连接到的第一个节点的名称或 TCP/IP 地址，并在第二步中插入第二个节点的相关数据][sap-ha-guide-figure-3037]

   _插入管理和配置工具应连接到的第一个节点的名称或 TCP/IP 地址，并在第二步中插入第二个节点_

3. 在两个节点之间创建复制作业。

   ![图 38：创建复制作业][sap-ha-guide-figure-3038]

   _创建复制作业_

   向导指导完成创建复制作业的过程。

4. 定义复制作业的名称。

   ![图 39：定义复制作业的名称][sap-ha-guide-figure-3039]

   _定义复制作业的名称_

   ![图 40：定义节点（应为当前源节点）的基本数据][sap-ha-guide-figure-3040]

   _定义节点的基本数据，该节点应为当前源节点_

5. 定义目标节点的名称、TCP/IP 地址和磁盘卷。

   ![图 41：定义当前目标节点的名称、TCP/IP 地址和磁盘卷][sap-ha-guide-figure-3041]

   _定义当前目标节点的名称、TCP/IP 地址和磁盘卷_

6. 定义压缩算法。 在本例中，建议压缩复制流。 尤其是在重新同步的情况下，压缩复制流可大幅缩短重新同步的时间。 压缩需要占用虚拟机的 CPU 和 RAM 资源。 随着压缩率增加，CPU 资源量也增加。 稍后可调整此设置。

7. 需要检查的另一项设置是复制以异步还是同步方式发生。 保护 SAP ASCS/SCS 配置时，必须使用同步复制。  

   ![图 42：定义复制详细信息][sap-ha-guide-figure-3042]

   _定义复制详细信息_

8. 定义是否应向 Windows Server 故障转移群集配置将复制作业所复制的卷表示为共享磁盘。 对于 SAP ASCS/SCS 配置，选择“是”，以便 Windows 群集将复制的卷视为可用作群集卷的共享磁盘。

   ![图 43：选择“是”将复制的卷设置为群集卷][sap-ha-guide-figure-3043]

   _ 选择 **"是"** 将复制的卷设置为群集 volume_

   创建卷后，DataKeeper 管理和配置工具显示复制作业处于活动状态。

   ![图 44：SAP ASCS/SCS 共享磁盘的 DataKeeper 同步镜像处于活动状态][sap-ha-guide-figure-3044]

   _SAP ASCS/SCS 共享磁盘的 DataKeeper 同步镜像处于活动状态_

   现在，故障转移群集管理器将磁盘显示为 DataKeeper 磁盘，如图 45 所示：

   ![图 45：故障转移群集管理器显示 DataKeeper 复制的磁盘][sap-ha-guide-figure-3045]

   _故障转移群集管理器显示 DataKeeper 复制的磁盘_


## <a name="next-steps"></a>后续步骤

* [使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]
