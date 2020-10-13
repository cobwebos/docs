---
title: 在 Azure 中，使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA | Microsoft Docs
description: 了解如何使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5126ae94d8c751952964aaf4df0736a5e546ff36
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963629"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>在 Azure 中，使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
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

本文介绍如何通过使用 Windows Server 故障转移群集和群集共享磁盘，群集化 SAP ASCS/SCS 实例，在 Azure 中配置高可用性 SAP 系统。 如 [体系结构指南：使用群集共享磁盘在 Windows 故障转移群集上群集 SAP ASCS/SCS 实例][sap-high-availability-guide-wsfc-shared-disk]中所述，有两种备用 *群集共享磁盘*：

- [Azure 共享磁盘](../../windows/disks-shared.md)
- 使用 [SIOS DataKeeper 群集 Edition](https://us.sios.com/products/datakeeper-cluster/) 创建将模拟群集共享磁盘的镜像存储 

## <a name="prerequisites"></a>先决条件

在开始安装之前，请查看这些文档：

* [体系结构指南：使用群集共享磁盘在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例][sap-high-availability-guide-wsfc-shared-disk]

* [为 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘准备 SAP HA 的 Azure 基础结构][sap-high-availability-infrastructure-wsfc-shared-disk]

本文不会介绍 DBMS 安装，因为安装因所使用的 DBMS 系统而异。 本文假设 DBMS 在高可用性方面的疑虑已通过不同 DBMS 供应商为 Azure 提供的功能支持而获得解决。 例如，适用于 SQL Server 的 AlwaysOn 或数据库镜像，以及适用于 Oracle 数据库的 Oracle Data Guard。 本文未介绍 DBMS 的高可用性方案。

当不同的 DBMS 服务与 Azure 中的群集 SAP ASCS 或 SCS 配置交互时，不存在任何特殊注意事项。

> [!NOTE]
> SAP NetWeaver ABAP 系统、Java 系统和 ABAP+Java 系统的安装过程几乎完全相同。 最明显的差别在于，SAP ABAP 系统只有一个 ASCS 实例。 SAP Java 系统有一个 SCS 实例。 SAP ABAP+Java 系统有一个 ASCS 实例和一个在相同 Microsoft 故障转移群集组中运行的 SCS 实例。 将明确说明每个 SAP NetWeaver 安装堆栈的所有安装差异。 您可以假定步骤的其余部分是相同的。  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>使用高可用性 ASCS/SCS 实例安装 SAP

> [!IMPORTANT]
> 如果使用 SIOS 显示共享磁盘，请不要将页面文件放置在 DataKeeper 镜像卷上。 可将页面文件保留在 Azure 虚拟机的临时驱动器 D 上，这是默认值。 如果它尚未在该位置，请将 Windows 页面文件移到 Azure 虚拟机的驱动器 D。  


安装包含高可用性 ASCS/SCS 实例的 SAP 系统的过程包括以下任务：

* 为群集 SAP ASCS/SCS 实例创建虚拟主机名。
* 在第一个群集节点上安装 SAP。
* 修改 ASCS/SCS 实例的 SAP 配置文件。
* 添加探测端口。
* 打开 Windows 防火墙探测端口。

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>为群集 SAP ASCS/SCS 实例创建虚拟主机名

1. 在 Windows DNS 管理器中为 ASCS/SCS 实例的虚拟主机名创建 DNS 条目。

   > [!IMPORTANT]
   > 分配给 ASCS/SCS 实例的虚拟主机名的 IP 地址必须与分配给 Azure 负载均衡器的 IP 地址相同。  
   
   
   ![图 1：定义 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的 DNS 条目][sap-ha-guide-figure-3046]

   _定义 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的 DNS 条目_

2. 如果正在使用新的 SAP 排队复制服务器2（也是群集实例），则还需要在 DNS 中保留 ERS2 的虚拟主机名。 

   > [!IMPORTANT]
   > 分配给 ERS2 实例虚拟主机名的 IP 地址必须是分配给 Azure 负载均衡器的 IP 地址的第二个 ip 地址。    
   
   
   ![图1A：定义 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的 DNS 条目][sap-ha-guide-figure-3046-ers2]

   _为 SAP ERS2 群集虚拟名称和 TCP/IP 地址定义 DNS 条目_


3. 若要定义分配给虚拟主机名的 IP 地址，请选择 " **DNS 管理器**  >  **域**"。

   ![图 2：SAP ASCS/SCS 群集配置的新虚拟名称和 TCP/IP 地址][sap-ha-guide-figure-3047]

   _SAP ASCS/SCS 群集配置的新虚拟名称和 TCP/IP 地址_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>安装 SAP 的第一个群集节点

1. 在群集节点 A 上执行第一个群集节点选项。选择：

   * **ABAP 系统**：**ASCS** 实例编号 **00**
   * **Java 系统**：**SCS** 实例编号 **01**
   * **ABAP+Java 系统**：**ASCS** 实例编号 **00** 和 **SCS** 实例编号 **01**

   
   > [!IMPORTANT]
   > 请记住，Azure 内部负载均衡器的配置负载均衡规则 (如果使用基本 SKU) 并且所选 SAP 实例编号必须匹配。

2. 按照 SAP 说明的安装过程操作。 请确保在开始安装选项 "第一个群集节点" 中，选择 "群集共享磁盘" 作为配置选项。

> [!TIP]
> SAP 安装文档介绍如何安装第一个 ASCS/SCS 群集节点。



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>修改 ASCS/SCS 实例的 SAP 配置文件

如果已将复制服务器1排入队列，请 `enque/encni/set_so_keepalive` 按如下所述添加 SAP 配置文件参数。 配置文件参数可避免 SAP 工作进程与排队服务器之间的连接在空闲时间太长时关闭。 ERS2 不需要 SAP 参数。 

1. 如果使用 ERS1，请将此配置文件参数添加到 SAP ASCS/SCS 实例配置文件。

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   对于 ERS1 和 ERS2，请确保 `keepalive` 按 SAP 说明 [1410736](https://launchpad.support.sap.com/#/notes/1410736)中所述设置 OS 参数。   

2. 若要应用 SAP 配置文件参数更改，请重新启动 SAP ASCS/SCS 实例。

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>添加探测端口

使用内部负载均衡器探测功能，让整个群集配置使用 Azure Load Balancer。 Azure 内部负载均衡器通常在参与的虚拟机之间平均分配传入的工作负荷。

但是，这在某些群集配置中由于只有一个实例处于活动状态而不起作用。 其他实例处于被动状态，并且无法接受任何工作负荷。 当 Azure 内部负载均衡器检测到哪个实例处于活动状态，并且仅面向活动实例时，探测功能会很有帮助。  

> [!IMPORTANT]
> 在此示例配置中， **ProbePort** 设置为 620**Nr**。 对于编号为 **00** 的 SAP ASCS 实例，该实例为 620**00**。 需要调整配置，使其与 SAP 实例编号和 SAP SID 匹配。

若要添加探测端口，请在其中一个群集 Vm 上运行此 PowerShell 模块：

- 对于 SAP ASC/SCS 实例 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- 如果使用的是群集的 ERS2。 不需要为 ERS1 配置探测端口，因为它未群集。  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 函数的代码如下 `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` 所示：
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
    
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }
    
                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
                    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }   
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>打开 Windows 防火墙探测端口

在两个群集节点上打开 Windows 防火墙探测端口。 使用以下脚本打开 Windows 防火墙探测端口。 更新环境的 PowerShell 变量。  
如果使用的是 ERS2，则还需要为 ERS2 探测端口打开防火墙端口。  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>安装数据库实例

若要安装数据库实例，请按照 SAP 安装文档中所述的过程操作。

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>安装第二个群集节点

若要安装第二个群集，请按照 SAP 安装指南中所述的步骤操作。

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>安装 SAP 主应用程序服务器

在 \<SID\> 已指定为托管 PAS 的虚拟机上安装主应用程序服务器 (PAS) 实例-0。 Azure 上没有依赖项。 如果使用 SIOS，则没有特定于 DataKeeper 的设置。

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>安装 SAP 附加应用程序服务器

在指定用于托管 SAP 应用程序服务器实例的所有虚拟机上安装 SAP 附加应用程序服务器 (AAS)。 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> 测试 SAP ASCS/SCS 实例故障转移

对于概括的故障转移测试，假设 SAP ASCS 在节点 A 上处于活动状态。  

1. 验证 SAP 系统是否可以成功地从节点 A 故障转移到节点 B 选择以下选项之一，以启动 SAP \<SID\> 群集组从群集节点 a 到群集节点 b 的故障转移：
    - 故障转移群集管理器  
    - 故障转移群集 PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. 重启 Windows 来宾操作系统中的群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。  
3. 重启 Azure 门户中的群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。  
4. 使用 Azure PowerShell 重启群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。

5. 验证
   - 故障转移后，验证 SAP \<SID\> 群集组是否正在群集节点 B 上运行。 

      ![图 8：在故障转移群集管理器中，SAP \<SID\> 群集组在群集节点 B 上运行][sap-ha-guide-figure-5002]

      _在故障转移群集管理器中，SAP \<SID\> 群集组在群集节点 B 上运行_

   - 故障转移后，请验证共享磁盘现在已装载到群集节点 B 上。 
   - 故障转移后，如果使用 SIOS，请验证 DataKeeper 是否正在将群集节点 B 上的源卷驱动器 S 中的数据复制到群集节点 A 上的目标卷 S。 

      ![图 9：SIOS DataKeeper 将本地卷从群集节点 B 复制到群集节点 A][sap-ha-guide-figure-5003]

      _SIOS DataKeeper 将本地卷从群集节点 B 复制到群集节点 A_