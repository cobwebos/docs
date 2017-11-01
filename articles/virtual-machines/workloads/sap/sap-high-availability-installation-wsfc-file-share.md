---
title: "Azure 上针对 SAP (A)SCS 实例使用 Windows 故障转移群集和文件共享的 SAP NetWeaver HA 安装 | Microsoft Docs"
description: "针对 SAP (A)SCS 实例使用 Windows 故障转移群集和文件共享的 SAP NetWeaver HA 安装"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 71296618-673b-4093-ab17-b7a80df6e9ac
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec7888cfb9b0d288b5b25f580c852ee32306684c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="sap-netweaver-ha-installation-on-windows-failover-cluster-and-file-share-for-sap-ascs-instance-on-azure"></a>Azure 上针对 SAP (A)SCS 实例使用 Windows 故障转移群集和文件共享的 SAP NetWeaver HA 安装

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1596496]:https://launchpad.support.sap.com/#/notes/1596496

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[sap-blog-new-sap-cluster-resource-dll]:https://blogs.sap.com/2017/08/28/new-sap-cluster-resource-dll-is-available/
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]:sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)


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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

本文档描述如何在 Azure 上安装和配置高可用 SAP 系统，使用 **Windows 故障转移群集 (WSFC)**和**横向扩展文件共享**作为一个选项来群集化 SAP (A)SCS 实例。

## <a name="prerequisites"></a>先决条件

在开始安装之前，请确保查看以下文档：

* [体系结构指南 - 基于 Windows 故障转移群集使用文件共享群集化 SAP (A)SCS 实例][sap-high-availability-guide-wsfc-file-share]

* [针对 SAP (A)SCS 实例使用 Windows 故障转移群集和文件共享完成 SAP HA 的 Azure 基础结构准备工作][sap-high-availability-infrastructure-wsfc-file-share]


你需要以下来自 SAP 的可执行文件/dll：
* SAP 软件预配管理器 (SWPM) 安装工具版本 SPS21（或更高版本）。
* 下载包含新的 SAP 群集资源 DLL 的最新 NTCLUST.SAR 档案。 新的 SAP 群集 dll 支持基于 Windows Server 故障转移群集使用文件共享的 SAP (A)SCS 高可用性。

  有关新的 SAP 群集资源 DLL 的详细信息，请查看此博客：[新的 SAP 群集资源 DLL 可用！][sap-blog-new-sap-cluster-resource-dll]

我们不会介绍 DBMS 安装，因为安装因所使用的 DBMS 系统而异。 但是，本文假设 DBMS 在高可用性方面的疑虑已通过不同 DBMS 供应商为 Azure 提供的功能支持而获得解决。 例如，适用于 SQL Server 的 Always On 或数据库镜像，以及适用于 Oracle 数据库的 Oracle Data Guard。 在本文中所使用的方案中，我们未向 DBMS 添加更多保护。

当不同的 DBMS 服务与 Azure 中这种群集 SAP ASCS/SCS 配置交互时，不存在任何特殊注意事项。

> [!NOTE]
> SAP NetWeaver ABAP 系统、Java 系统和 ABAP+Java 系统的安装过程几乎完全相同。 最明显的差别在于，SAP ABAP 系统只有一个 ASCS 实例。 SAP Java 系统有一个 SCS 实例。 SAP ABAP+Java 系统有一个 ASCS 实例和一个在相同 Microsoft 故障转移群集组中运行的 SCS 实例。 将明确说明每个 SAP NetWeaver 安装堆栈的所有安装差异。 可假定所有其他部分都相同。  
>
>

## <a name="install-ascs-instance-on-ascs-cluster"></a>在 (A)SCS 群集上安装 (A)SCS 实例

> [!IMPORTANT]
>
>目前，SAP 安装工具软件预配管理器 (SWPM) 不支持使用文件共享配置的 HA 设置。 因此，需手动安装 SAP 系统，例如，安装和群集化 SAP (A)SCS 实例并配置单独的 SAP GLOBALHOST。  
>
>安装（和群集化）DBMS 实例和 SAP 应用程序服务器的其他安装步骤没有变化。
>

### <a name="install-ascs-instance-on-local-drive"></a>在本地驱动器上安装 (A)SCS 实例

在两个 (A)SCS 群集节点上安装 SAP(A)SCS 实例。 将其安装在本地驱动器中。 在本示例中，我们选择的本地驱动器为 C:\\。 也可以选择任何其他本地驱动器。  

若要安装，请在 SAP 安装工具 SWPM 中导航到：

&lt;产品&gt; -> &lt;DBMS&gt; -> 安装 -> 应用程序服务器 ABAP（或 Java） -> 分布式系统 -> (A)SCS 实例

> [!IMPORTANT]
>目前，SAP 安装工具 SWPM 尚不支持文件共享方案，因此不能使用安装路径：
>
>&lt;产品&gt; -> &lt;DBMS&gt; -> 安装 -> 应用程序服务器 ABAP（或 Java） -> 高可用性系统 -> …
>

### <a name="remove-sapmnt-and-create-saploc-file-share"></a>删除 SAPMNT 并创建 SAPLOC 文件共享

SWMP 在 C:\\usr\\sap 文件夹上创建了 SAPMNT 本地共享。

在两个 (A)SCS 群集节点上删除 SAPMNT 文件共享：

执行以下 PowerShell 脚本：

```PowerShell
Remove-SmbShare sapmnt -ScopeName * -Force
 ```

如果 SAPLOC 共享不存在，则在两个 ASCS 群集节点上创建一个。

执行以下 PowerShell 脚本：

```PowerShell
#Create SAPLOC share and set security
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"
$HostName = $env:computername
$SAPLocalAdminGroupName = "$HostName\SAP_LocalAdmin"
$SAPDisk = "C:"
$SAPusrSapPath = "$SAPDisk\usr\sap"

New-SmbShare -Name saploc -Path c:\usr\sap -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName , $SAPLocalAdminGroupName  
 ```

## <a name="prepare-sap-global-host-on-sofs-cluster"></a>在 SOFS 群集上准备 SAP GLOBAL HOST

在此步骤中，在 SOFS 群集上创建以下卷和文件共享：

* SOFS 群集共享卷 (CSV) 上的 SAP GLOBALHOST 文件 C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS\ 结构

* 创建 SAPMNT 文件共享

* 通过对以下项的完全控制，在 SAPMNT 文件共享和文件夹上设置安全性：
    * &lt;DOMAIN&gt;\SAP_&lt;SID&gt;_GlobalAdmin 用户组
    * SAP (A)SCS 群集节点计算机对象 &lt;DOMAIN&gt;\ClusterNode1$ 和 &lt;DOMAIN&gt;\ClusterNode2$

若要创建具有镜像复原的 CSV 卷，如 **Azure 中 SOFS 的 SAP 先决条件 - 添加链接**章节中所定义的那样，请在其中一个 SOFS 群集节点上执行以下 PowerShell cmdlet：


```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR1 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```
若要创建 SAPMNT 并设置文件夹和共享安全性，请在以下其中一个 SOFS 群集节点上执行 PowerShell 脚本：

```PowerShell
# Create SAPMNT on file share
$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Create SAPMNT file share and set share security
New-SmbShare -Name sapmnt -Path $UsrSAPFolder -FullAccess "BUILTIN\Administrators", $SAPSIDGlobalAdminGroupName, $ASCSClusterObjectNode1, $ASCSClusterObjectNode2 -ContinuouslyAvailable $false -CachingMode None -Verbose

# Get SAPMNT file share security settings
Get-SmbShareAccess sapmnt

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
 ```
## <a name="stop-ascs-instances-and-sap-services"></a>停止 (A)SCS 实例和 SAP 服务

执行以下步骤：
* 停止两个 (A)SCS 群集节点上的 SAP (A)SCS 实例
* 停止两个群集节点上的 SAP (A)SCS Windows 服务 SAP&lt;SID&gt;_&lt;InstanceNumber&gt;

## <a name="move-sys-folder-to-sofs-cluster"></a>将 \SYS\...文件夹移动到 SOFS 群集

执行以下步骤：
* 将 SYS 文件夹（例如 C:\usr\sap\\&lt;SID&gt;\SYS）从一个 (A)SCS 群集节点移动到 SOFS 群集，例如 C:\ClusterStorage\Volume1\usr\sap\\&lt;SID&gt;\SYS
* 从这两个 (A)SCS 群集节点删除 C:\usr\sap\\&lt;SID&gt;\SYS 文件夹

## <a name="update-cluster-security-setting-on-sap-ascs-cluster"></a>更新 SAP (A)SCS 群集上的群集安全设置

在其中一个 SAP(A)SCS 群集节点上执行以下 PowerShell 脚本：

```PowerShell
# Grant <DOMAIN>\SAP_<SID>_GlobalAdmin group access to cluster

$SAPSID = "PR1"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# Set full access for <DOMAIn>\SAP_<SID>_GlobalAdmin group
Grant-ClusterAccess -User $SAPSIDGlobalAdminGroupName -Full

#Check security settings
Get-ClusterAccess
```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascs-instance"></a>为群集 SAP (A)SCS 实例创建虚拟主机名

如章节[为群集 SAP ASCS/SCS 实例创建虚拟主机名][sap-high-availability-installation-wsfc-shared-disk-create-ascs-virt-host]中所述，创建 SAP (A)SCS 群集网络名称，例如 pr1-ascs [10.0.6.7]

## <a name="update-default-and-sap-ascs-instance-profile"></a>更新默认和 SAP (A)SCS 实例配置文件

必须更新默认值和 SAP (A)SCS 实例配置文件 &lt;SID&gt;_(A)SCS<Nr>_<Host>才能使用以下内容：

* 新的 SAP (A)SCS 虚拟主机名

* 新的 SAP 全局主机名


| 旧值 |  |
| --- | --- |
| SAP (A)SCS 主机名 = SAP 全局主机 | ascs-1 |
| SAP (A)SCS 实例配置文件名称 | PR1_ASCS00_ascs-1 |

| 新值 |  |
| --- | --- |
| SAP (A)SCS 主机名 | pr1-ascs |
| SAP 全局主机 | sapglobal |
| SAP (A)SCS 实例配置文件名称 | PR1\_ASCS00\_pr1-ascs |

### <a name="update-sap-default-profile"></a>更新 SAP 默认配置文件


| 参数名称 | 参数值 |
| --- | --- |
| SAPGLOBALHOST | sapglobal |
| rdisp/mshost | pr1-ascs |
| enque/serverhost | pr1-ascs |

### <a name="update-sap-ascs-instance-profile"></a>更新 SAP (A)SCS 实例配置文件

| 参数名称 | 参数值 |
| --- | --- |
| SAPGLOBALHOST | sapglobal |
| DIR_PROFILE | \\\\sapglobal\sapmnt\PR1\SYS\profile |
| _PF | $(DIR_PROFILE)\PR1\_ASCS00_ pr1-ascs |
| Restart_Program_02 = local$(_MS) pf=$(_PF) | Start_Program_02 = local$(_MS) pf=$(_PF) |
| SAPLOCALHOST | pr1-ascs |
| Restart_Program_03 = local$(_EN) pf=$(_PF) | Start_Program_03 = local$(_EN) pf=$(_PF) |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | true |
| service/ha_check_node | **1** |

> [!IMPORTANT]
>可以使用 Update-SAPASCSSCSProfile PowerShell cmdlet 自动更新配置文件
>
>PowerShell cmdlet 支持 SAP ABAP ASCS 和 SAP Java SCS 实例。
>

将 SAPScripts.ps1 复制到本地驱动器 C:\tmp 并运行以下 PowerShell cmdlet：

```PowerShell
Import-Module C:\tmp\SAPScripts.ps1

Update-SAPASCSSCSProfile -PathToAscsScsInstanceProfile \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_ascs-1 -NewASCSHostName pr1-ascs -NewSAPGlobalHostName sapglobal -Verbose  
```

![图 1：SAPScripts.ps1 输出][sap-ha-guide-figure-8012]

图 1：SAPScripts.ps1 输出

## <a name="update-ltsidgtadm-user-environment-variable"></a>更新 &lt;sid&gt;adm 用户环境变量

更新两个 (A)SCS 群集节点上的 &lt;sid&gt;adm 用户环境新 GLOBALHOST UNC 路径。
以 &lt;sid&gt;adm 用户身份登录并启动 Regedit.exe 工具。
转到“HKEY_CURRENT_USER” -> “环境”，并将变量更新为新值：

| 变量 | 值 |
| --- | --- |
| RSEC_SSFS_DATAPATH | \\\\sapglobal\sapmnt\PR1\SYS\global\security\rsecssfs\data |
| RSEC_SSFS_KEYPATH | \\\\sapglobal\sapmnt\PR1\SYS\global\security\rsecssfs\key |
| SAPEXE | \\\\sapglobal\sapmnt\PR1\SYS\exe\uc\NTAMD64 |
| SAPLOCALHOST  | pr1-ascs |


## <a name="install-new-saprcdll"></a>安装新的 SAPRC.DLL

你需要安装支持文件共享方案的新版 SAP 群集资源。

从 SAP 服务商城下载最新的 NTCLUST.SAR包。

在其中一个 (A)SCS 群集节点上解压缩 NTCLUS.SAR，并从命令提示符运行以下命令来安装新的 saprc.dll：

```
.\NTCLUST\insaprct.exe -yes -install
```

将在两个 (A)SCS 群集节点上安装新的 saprc.dll。

有关详细信息，请参阅 [SAP 说明 1596496 - 如何更新群集资源监视器的 SAP 资源类型 DLL][1596496]。

## <a name="create-sap-sid-cluster-group-network-name-and-ip"></a>创建 SAP <SID> 群集组、网络名称和 IP

你必须创建：

* SAP &lt;SID&gt; 群集组
* <(A)SCSNetworkName>
* 和对应的 IP 地址

运行以下 PowerShell cmdlet：

```PowerShell
# Create SAP Cluster Group
$SAPSID = "PR1"
$SAPClusterGroupName = "SAP $SAPSID"
$SAPIPClusterResourceName = "SAP $SAPSID IP"
$SAPASCSNetworkName = "pr1-ascs"
$SAPASCSIPAddress = "10.0.6.7"
$SAPASCSSubnetMask = "255.255.255.0"

# Create SAP ASCS instance Virtual IP cluster resource
Add-ClusterGroup -Name $SAPClusterGroupName -Verbose

#Create SAP ASCS Virtual IP Address
$SAPIPClusterResource = Add-ClusterResource -Name $SAPIPClusterResourceName -ResourceType "IP Address" -Group $SAPClusterGroupName -Verbose

# Set static IP Address
$param1 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,Address,$SAPASCSIPAddress
$param2 = New-Object Microsoft.FailoverClusters.PowerShell.ClusterParameter $SAPIPClusterResource,SubnetMask,$SAPASCSSubnetMask
$params = $param1,$param2
$params | Set-ClusterParameter

# Create corresponding network name
$SAPNetworkNameClusterResourceName = $SAPASCSNetworkName
Add-ClusterResource -Name $SAPNetworkNameClusterResourceName -ResourceType "Network Name" -Group $SAPClusterGroupName -Verbose

# Set Network DNS Name
$SAPNetworkNameClusterResource = Get-ClusterResource $SAPNetworkNameClusterResourceName
$SAPNetworkNameClusterResource | Set-ClusterParameter -Name Name -Value $SAPASCSNetworkName

#Check the updated values
$SAPNetworkNameClusterResource | Get-ClusterParameter

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPNetworkNameClusterResourceName -Dependency "[$SAPIPClusterResourceName]" -Verbose

#Start SAP <SID> Cluster Group
Start-ClusterGroup -Name $SAPClusterGroupName -Verbose
```

## <a name="register-sap-start-service-on-both-nodes"></a>在两个节点上注册 SAP START 服务

你需要重新注册 SAP (A)SCS sapstart 服务以指向新的配置文件和配置文件路径。

必须在两个 (A)SCS 群集节点上执行此操作。

从提升的命令提示符运行以下命令：

```
C:\usr\sap\PR1\ASCS00\exe\sapstartsrv.exe -r -p \\sapglobal\sapmnt\PR1\SYS\profile\PR1_ASCS00_pr1-ascs -s PR1 -n 00 -U SAPCLUSTER\SAPServicePR1 -P mypasswd12 -e SAPCLUSTER\pr1adm
```

![图 2：重新安装 SAP 服务][sap-ha-guide-figure-8013]

图 2：重新安装 SAP 服务

确保参数正确，并选择“手动”作为启动类型。

## <a name="stop-ascs-service"></a>停止 (A)SCS 服务

停止两个 (A)SCS 群集节点上的 SAP (A)SCS 服务 SAP&lt;SID&gt;_ &lt;InstanceNumber&gt;。

## <a name="create-new-sap-service-and-sap-instance-resources"></a>创建新的 SAP 服务和 SAP 实例资源

现在，你必须完成 SAP SAP&lt;SID&gt; 群集组的资源创建，例如，你需要创建以下资源：

* SAP &lt;SID&gt; &lt;InstanceNumber&gt; 服务 和
* SAP &lt;SID&gt; &lt;InstanceNumber&gt; 实例

运行以下 Azure Powershell cmdlet：

```PowerShell
$SAPSID = "PR1"
$SAPInstanceNumber = "00"
$SAPNetworkNameClusterResourceName = "pr1-ascs"

$SAPServiceName = "SAP$SAPSID"+ "_" + $SAPInstanceNumber

$SAPClusterGroupName = "SAP $SAPSID"
$SAPServiceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Service"

$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPServiceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Service" -SeparateMonitor -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name ServiceName -Value $SAPServiceName

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPNetworkNameClusterResourceName]" -Verbose

$SAPInstanceClusterResourceName = "SAP $SAPSID $SAPInstanceNumber Instance"

# Create SAP Instance cluster resource
$SAPASCSServiceClusterResource = Add-ClusterResource -Name $SAPInstanceClusterResourceName -Group $SAPClusterGroupName -ResourceType "SAP Resource" -SeparateMonitor -Verbose

#Set SAP Instance cluster resource parameters
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystemName -Value $SAPSID -Verbose
$SAPASCSServiceClusterResource  | Set-ClusterParameter  -Name SAPSystem -Value $SAPInstanceNumber -Verbose

#Set resource dependencies
Set-ClusterResourceDependency -Resource $SAPASCSServiceClusterResource  -Dependency "[$SAPServiceClusterResourceName]" -Verbose
```

## <a name="add-a-probe-port"></a>添加探测端口

此步骤使用 PowerShell 配置 SAP 群集资源 SAP-SID-IP 探测端口。 应在其中一个 SAP ASCS/SCS 群集节点上执行此配置，如[此处][sap-high-availability-installation-wsfc-shared-disk-add-probe-port]所述。

## <a name="install-ers-instance-on-both-cluster-nodes"></a>在两个群集节点上安装 ERS 实例

在下一步中，必须在两个 (A)SCS 群集节点上安装 ERS（排入队列复制服务器）群集。
可以在 SWPM 菜单中找到安装选项：

&lt;产品&gt; -> &lt;DBMS&gt; -> 安装-> 其他 SAP 系统实例 -> 排入队列复制服务器实例

## <a name="install-dbms-instance-and-sap-application-servers"></a>安装 DBMS 实例和 SAP 应用程序服务器

通过安装以下项完成 SAP 系统安装：
* DBMS 实例
* 主 SAP 应用程序服务器
* 其他 SAP 应用程序服务器

## <a name="next-steps"></a>后续步骤

* [在没有共享磁盘的故障转移群集上安装一个 (A)SCS 实例 - HA 文件共享的官方 SAP 准则][sap-official-ha-file-share-document]：

* [Windows Server 2016 中的存储空间直通][s2d-in-win-2016]

* [应用程序数据的横向扩展文件服务器概述][sofs-overview]

* [Windows Server 2016 的存储中的新增功能][new-in-win-2016-storage]
