---
title: 在 Azure 中，使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA | Microsoft Docs
description: 了解如何使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA。
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5d52653d68c6ebfca7e35a134da263eee99fd3e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657071"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>在 Azure 中，使用 Windows 故障转移群集和共享磁盘为 SAP ASCS/SCS 实例安装 SAP NetWeaver HA

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

本文介绍如何通过使用 Windows Server 故障转移群集和群集共享磁盘，群集化 SAP ASCS/SCS 实例，在 Azure 中配置高可用性 SAP 系统。

## <a name="prerequisites"></a>先决条件

在开始安装之前，请查看这些文档：

* [体系结构指南：使用群集共享磁盘在 Windows 故障转移群集上群集化 SAP ASCS/SCS 实例][sap-high-availability-guide-wsfc-shared-disk]

* [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和共享磁盘，准备 SAP HA 的 Azure 基础结构][sap-high-availability-infrastructure-wsfc-shared-disk]

本文不会介绍 DBMS 安装，因为安装因所使用的 DBMS 系统而异。 本文假设 DBMS 在高可用性方面的疑虑已通过不同 DBMS 供应商为 Azure 提供的功能支持而获得解决。 例如，适用于 SQL Server 的 AlwaysOn 或数据库镜像，以及适用于 Oracle 数据库的 Oracle Data Guard。 在本文中所使用的方案中，我们未向 DBMS 添加更多保护。

当不同的 DBMS 服务与 Azure 中的群集 SAP ASCS 或 SCS 配置交互时，不存在任何特殊注意事项。

> [!NOTE]
> SAP NetWeaver ABAP 系统、Java 系统和 ABAP+Java 系统的安装过程几乎完全相同。 最明显的差别在于，SAP ABAP 系统只有一个 ASCS 实例。 SAP Java 系统有一个 SCS 实例。 SAP ABAP+Java 系统有一个 ASCS 实例和一个在相同 Microsoft 故障转移群集组中运行的 SCS 实例。 将明确说明每个 SAP NetWeaver 安装堆栈的所有安装差异。 可假定所有其他部分都相同。  
>
>

## <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>使用高可用性 ASCS/SCS 实例安装 SAP

> [!IMPORTANT]
> 请确保不要将页面文件放置在 SIOS DataKeeper 镜像卷上。 DataKeeper 不支持镜像卷。 可将页面文件保留在 Azure 虚拟机的临时驱动器 D 上，这是默认值。 如果它尚未在该位置，请将 Windows 页面文件移到 Azure 虚拟机的驱动器 D。
>
>

安装包含高可用性 ASCS/SCS 实例的 SAP 系统的过程包括以下任务：

* 为群集 SAP ASCS/SCS 实例创建虚拟主机名。
* 安装 SAP 的第一个群集节点。
* 修改 ASCS/SCS 实例的 SAP 配置文件。
* 添加探测端口。
* 打开 Windows 防火墙探测端口。

### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>为群集 SAP ASCS/SCS 实例创建虚拟主机名

1.  在 Windows DNS 管理器中为 ASCS/SCS 实例的虚拟主机名创建 DNS 条目。

  > [!IMPORTANT]
  > 分配给 ASCS/SCS 实例虚拟主机名的 IP 地址必须与分配给 Azure 负载均衡器 (\<SID\>-lb-ascs) 的 IP 地址相同。  
  >
  >

  虚拟 SAP ASCS/SCS 的主机名 (pr1-ascs-sap) 的 IP 地址与 Azure 负载均衡器(pr1-lb-ascs) 的 IP 地址相同。

  ![图 1：定义 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的 DNS 条目][sap-ha-guide-figure-3046]

  图 1：定义 SAP ASCS/SCS 群集虚拟名称和 TCP/IP 地址的 DNS 条目

2.  若要定义分配给虚拟主机名的 IP 地址，请选择“DNS 管理器” > “域”。

  ![图 2：SAP ASCS/SCS 群集配置的新虚拟名称和 TCP/IP 地址][sap-ha-guide-figure-3047]

  图 2：SAP ASCS/SCS 群集配置的新虚拟名称和 TCP/IP 地址

### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>安装 SAP 的第一个群集节点

1.  在群集节点 A 上执行第一个群集节点选项。例如，在 pr1-ascs-0 主机上。
2.  若要保留 Azure 内部负载均衡器的默认端口，请选择：

  * **ABAP 系统**：**ASCS** 实例编号 **00**
  * **Java 系统**：**SCS** 实例编号 **01**
  * **ABAP+Java 系统**：**ASCS** 实例编号 **00** 和 **SCS** 实例编号 **01**

  若要对 ABAP ASCS 实例使用 00 以外的实例编号并且对 Java SCS 实例使用 01 以外的实例编号，请先更改 Azure 内部负载均衡器的默认负载均衡规则。 有关详细信息，请参阅[更改 Azure 内部负载均衡器的 ASCS/SCS 默认负载均衡规则][sap-ha-guide-8.9]。

下面几个任务未在标准的 SAP 安装文档中做介绍。

> [!NOTE]
> SAP 安装文档介绍如何安装第一个 ASCS/SCS 群集节点。
>
>

### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>修改 ASCS/SCS 实例的 SAP 配置文件

首先，需要添加新的配置文件参数。 配置文件参数可避免 SAP 工作进程与排队服务器之间的连接在空闲时间太长时关闭。 [在 SAP ASCS/SCS 实例的两个群集节点上添加注册表项][sap-ha-guide-8.11]部分中已提到了出现该问题的情景。 在该部分中，我们还介绍了对一些基本 TCP/IP 连接参数所做的两项更改。 在第二个步骤中，需将排队服务器设置为发送 `keep_alive` 信号，以便连接不会达到 Azure 内部负载均衡器的空闲阈值。

修改 ASCS/SCS 实例的 SAP 配置文件：

1.  将此配置文件参数添加到 SAP ASCS/SCS 实例配置文件：

  ```
  enque/encni/set_so_keepalive = true
  ```
  在本例中，路径为：

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

  例如，添加到 SAP SCS 实例配置文件和相应的路径：

  `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2.  若要应用更改，请重启 SAP ASCS/SCS 实例。

### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>添加探测端口

使用内部负载均衡器探测功能，让整个群集配置使用 Azure Load Balancer。 Azure 内部负载均衡器通常在参与的虚拟机之间平均分配传入的工作负荷。

 但是，这在某些群集配置中由于只有一个实例处于活动状态而不起作用。 其他实例处于被动状态，并且无法接受任何工作负荷。 当 Azure 内部负载均衡器将工作仅分配给活动实例时，探测功能很有帮助。 通过探测功能，内部负载均衡器可检测到哪些实例处于活动状态，仅定位带有工作负荷的实例。

添加探测端口：

1.  运行以下 PowerShell 命令，检查当前的“ProbePort”值：

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
  ```

   请在群集配置中的某个虚拟机上执行命令。

2.  定义探测端口。 默认探测端口号为 0。 在本示例中，我们使用探测端口 62000。

  ![图 3：群集配置探测端口默认为 0][sap-ha-guide-figure-3048]

  图 3：群集配置探测端口默认为 0

  端口号在 SAP Azure 资源管理器模板中定义。 可在 PowerShell 中分配端口号。

  若要为 SAP \<SID\> IP 群集资源设置新的 ProbePort 值，请运行以下 PowerShell 脚本，更新环境的 PowerShell 变量：

  ```PowerShell
  $SAPSID = "PR1"      # SAP <SID>
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  Clear-Host
  $SAPClusterRoleName = "SAP $SAPSID"
  $SAPIPresourceName = "SAP $SAPSID IP"
  $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
  $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
  $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
  $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
  $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
  $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
  $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

  $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

  Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

  Write-Host
  Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
  Write-Host
  Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
  Write-Host

  $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

  Write-Host

  $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

  if($ActivateChanges -eq "yes"){
  Write-Host
  Write-Host "Activating changes..." -ForegroundColor Cyan

  Write-Host
  write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
  Stop-ClusterResource -Name $SAPIPresourceName
  sleep 5

  Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
  Start-ClusterGroup -Name $SAPClusterRoleName

  Write-Host "New ProbePort parameter is active." -ForegroundColor Green
  Write-Host

  Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
  Write-Host
  Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
  }else
  {
  Write-Host "Changes are not activated."
  }
  ```

  将 SAP \<SID\> 群集角色联机之后，验证“ProbePort”是否已设置为新值。

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPNetworkIPClusterName = "SAP $SAPSID IP"
  Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

  ```
  运行该脚本后，系统会提示重启 SAP 群集组以激活更改。

  ![图 4：设置新值后探测群集端口][sap-ha-guide-figure-3049]

  图 4：设置新值后探测群集端口

### <a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a>打开 Windows 防火墙探测端口

在两个群集节点上打开 Windows 防火墙探测端口。 使用以下脚本打开 Windows 防火墙探测端口。 更新环境的 PowerShell 变量。

  ```PowerShell
  $ProbePort = 62000   # ProbePort of the Azure internal load balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

ProbePort 设置为 62000。 现在，可从其他主机（例如 ascsha-dbas）访问文件共享 \\\ascsha-clsap\sapmnt。

## <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>安装数据库实例

若要安装数据库实例，请按照 SAP 安装文档中所述的过程操作。

## <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>安装第二个群集节点

若要安装第二个群集，请按照 SAP 安装指南中所述的步骤操作。

## <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a>更改 SAP ERS Windows 服务实例的启动类型

将两个群集节点上的 SAP 排队复制服务器 (ERS) Windows 服务的启动类型更改为“自动(延迟启动)”。

![图 5：将 SAP ERS 实例的服务类型更改为自动延迟][sap-ha-guide-figure-3050]

图 5：将 SAP ERS 实例的服务类型更改为自动延迟

## <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>安装 SAP 主应用程序服务器

在已指定为托管 PAS 的虚拟机上安装主应用程序服务器 (PAS) 实例 \<SID\>-di-0。 Azure 上没有依赖项。 没有特定于 DataKeeper 的设置。

## <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>安装 SAP 附加应用程序服务器

在指定用于托管 SAP 应用程序服务器实例的所有虚拟机上安装 SAP 附加应用程序服务器 (AAS)。 例如，在 \<SID\>-di-1 到 \<SID\>-di-&lt;n&gt; 上。

> [!NOTE]
> 现已完成高可用性 SAP NetWeaver 系统的安装。 接下来，请执行故障转移测试。
>


## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>测试 SAP ASCS/SCS 实例故障转移和 SIOS 复制
可以使用故障转移群集管理器和 SIOS DataKeeper 管理和配置工具，轻松测试及监视 SAP ASCS/SCS 实例故障转移与 SIOS 磁盘复制。

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>SAP ASCS/SCS 实例在群集节点 A 上运行

SAP PR1 群集组在群集节点 A（例如在 pr1-ascs-0）上运行。 将属于 SAP PR1 群集组的共享磁盘驱动器 S 分配到群集节点 A。ASCS/SCS 实例也使用磁盘驱动器 S。 

![图 6：故障转移群集管理器：SAP \<SID\> 群集组在群集节点 A 上运行][sap-ha-guide-figure-5000]

_图 6：故障转移群集管理器：SAP \<SID\> 群集组在群集节点 A 上运行_

在 SIOS DataKeeper 管理和配置工具中，可以看到共享磁盘数据以同步方式从群集节点 A 上的源卷驱动器 S 复制到群集节点 B 上的目标卷驱动器 S。例如，从 pr1-ascs-0 [10.0.0.40] 复制到 pr1-ascs-1 [10.0.0.41]。

![图 7：在 SIOS DataKeeper 中，将本地卷从群集节点 A 复制到群集节点 B][sap-ha-guide-figure-5001]

图 7：在 SIOS DataKeeper 中，将本地卷从群集节点 A 复制到群集节点 B

### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>从节点 A 到节点 B 的故障转移

1.  选择以下选项之一，开始将 SAP\<SID\> 群集组从群集节点 A 故障转转到群集节点 B 的故障转移：
  - 故障转移群集管理器  
  - 故障转移群集 PowerShell

  ```PowerShell
  $SAPSID = "PR1"     # SAP <SID>

  $SAPClusterGroup = "SAP $SAPSID"
  Move-ClusterGroup -Name $SAPClusterGroup

  ```
2.  重启 Windows 来宾操作系统中的群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。  
3.  重启 Azure 门户中的群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。  
4.  使用 Azure PowerShell 重启群集节点 A。 这会启动将 SAP \<SID\> 群集组从节点 A 故障转移到节点 B 的自动故障转移。

  故障转移后，SAP \<SID\> 群集组在群集节点 B 上运行。例如，在 pr1-ascs-1 上运行。

  ![图 8：在故障转移群集管理器中，SAP \<SID\> 群集组在群集节点 B 上运行][sap-ha-guide-figure-5002]

  _图 8：在故障转移群集管理器中，SAP \<SID\> 群集组在群集节点 B 上运行_

  共享磁盘现在已装载到群集节点 B。SIOS DataKeeper 正在将数据从群集节点 B 上的源卷 S 复制到群集节点 A 上的目标卷 S。例如，从 pr1-ascs-1 [10.0.0.41] 复制到 pr1-ascs-0 [10.0.0.40]。

  ![图 9：SIOS DataKeeper 将本地卷从群集节点 B 复制到群集节点 A][sap-ha-guide-figure-5003]

  图 9：SIOS DataKeeper 将本地卷从群集节点 B 复制到群集节点 A
