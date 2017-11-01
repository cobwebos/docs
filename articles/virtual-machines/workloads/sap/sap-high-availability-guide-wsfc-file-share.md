---
title: "使用 Azure 中的文件共享群集化 Windows 故障转移群集上的 SAP (A)SCS 实例 | Microsoft Docs"
description: "使用文件共享群集化 Windows 故障转移群集上的 SAP (A)SCS 实例"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 472c3f35e2ae32550be62826407689f93101041f
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

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
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>使用 Azure 中的文件共享群集化 Windows 故障转移群集上的 SAP (A)SCS 实例

> ![Windows][Logo_Windows] Windows
>

Windows Server 故障转移群集是 Windows 中高可用性 SAP ASCS/SCS 安装和 DBMS 的基础。

故障转移群集是由 1+n 个独立服务器（节点）构成的组，这些服务器配合工作以提高应用程序和服务的可用性。 如果发生节点故障，Windows Server 故障转移群集将计算发生的故障次数并维护状况良好的群集，提供应用程序和服务。 可从不同的仲裁模式中选择，以实现故障转移群集。

## <a name="prerequisite"></a>先决条件
在开始学习本文档之前，请确保查看以下文档：

* [SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>装有 **SAP 内核 7.49（和更高版本）**的 **SAP NetWeaver 7.40（和更高版本）**产品支持使用文件共享群集化 SAP (A)SCS 实例。
>


## <a name="windows-server-failover-clustering-in-azure"></a>Azure 中的 Windows Server 故障转移群集

相比于裸机或私有云部署，Azure 虚拟机要求执行额外的步骤来配置 Windows Server 故障转移群集。 构建群集时，需要为 SAP ASCS/SCS 实例设置多个 IP 地址和虚拟主机名。

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Azure 中的名称解析和群集虚拟主机名

Azure 云平台不提供配置虚拟 IP 地址（如浮动 IP 地址）的选项。 需要一个替代解决方案来设置虚拟 IP，以便连接到云中的群集资源。 Azure 在 Azure 负载均衡器服务中具有**内部负载均衡器**。 借助内部负载均衡器，客户端通过群集虚拟 IP 地址访问群集。 需要在包含群集节点的资源组中部署内部负载均衡器。 然后，使用内部负载均衡器的探测端口配置所有必要的端口转发规则。 客户端可以通过虚拟主机名连接。 DNS 服务器解析群集 IP 地址，内部负载均衡器处理向活动群集节点的端口转发。

![图 1：Azure 中没有共享磁盘的 Windows Server 故障转移群集配置][sap-ha-guide-figure-1001]

_**图 1：**Azure 中没有共享磁盘的 Windows Server 故障转移群集配置_

## <a name="sap-ascs-ha-with-file-share"></a>采用文件共享的 SAP (A)SCS HA

SAP 开发了可以取代群集共享磁盘的新方法和新方案，用于将 Windows 故障转移群集上的 SAP (A)SCS 实例群集化。

本文使用的 **SMB 文件共享**就是一个可用于部署 **SAP GLOBAL HOST 文件**的选项。

> [!NOTE]
>SMB 文件共享是除群集共享磁盘以外的一个附加选项，可将 SAP (A)SCS 实例群集化。  
>

此体系结构的具体特征如下：

* **SAP 中心服务（具有自身的文件结构以及消息和排队进程）与 SAP GLOBAL Host 文件相互独立**
* **SAP 中心服务在 SAP (A)SCS 实例下运行**
* SAP (A)SCS 实例已群集化，可使用虚拟主机名 **<(A)SCSVirtualHostName>** 进行访问
* SAP GLOBAL 文件放置在 SMB 文件共享中，可使用 <SAPGLOBALHost> 主机名 \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\. 进行访问。
* SAP (A)SCS 实例安装在本地磁盘和两个群集节点上
* **<(A)SCSVirtualHostName>** 网络名称不同于 **<SAPGLOBALHost>**

![图 2：采用 SMB 文件共享的新 SAP (A)SCS HA 体系结构][sap-ha-guide-figure-8004]

_**图 2：**采用 SMB 文件共享的新 SAP (A)SCS HA 体系结构_

SMB 文件共享的先决条件：

* SMB 3.0（或更高版本）协议
* 能够设置 **AD 用户组**和**计算机对象 computer$** 的 Active Directory (AD) 访问控制列表 (ACL)
* 必须为文件共享启用 HA：
    * 用于存储文件的磁盘不能是单一故障点
    * 必须确保服务器/VM 停机不会导致文件共享关闭

现在，**SAP &lt;SID&gt;** 群集角色不会包含共享群集磁盘或通用文件共享群集资源。


![图 3：使用文件共享时的 SAP <SID> 群集角色资源][sap-ha-guide-figure-8005]

_**图 3：**使用文件共享时的 **SAP &lt;SID&gt;** 群集角色资源_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Azure 上用作 SAPMNT 文件共享的、具有存储空间直通 (S2D) 功能的横向扩展文件共享 (SOFS)

可以使用 SOFS 来托管和保护 SAP GLOBAL Host 文件，以及提供高度可用的 SAPMNT 文件共享服务。

![图 4：用于保护 SAP GLOBAL Host 文件的 SOFS 文件共享][sap-ha-guide-figure-8006]

_**图 4：**用于保护 SAP GLOBAL Host 文件的 SOFS 文件共享_

> [!IMPORTANT]
>Microsoft Azure 云以及本地环境完全支持 SOFS 文件共享。
>

**SOFS** 提供高度可用且可横向缩放的 SAPMNT 文件共享。

**存储空间直通 (S2D)** 用作 SOFS 的**共享磁盘**。借助 S2D 可以使用具有本地存储的服务器构建高度可用且可缩放的存储。 因此，用于 SOFS（例如用于 SAP GLOBALHOST 文件）的共享存储不是单一故障点 (SPOF)。

> [!IMPORTANT]
>如果打算设置灾难恢复，SOFS 是在 Azure 中构建高度可用的文件共享的建议解决方案。
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Azure 中 SOFS 的 SAP 先决条件

对于 SOFS，需要：

* 至少创建两个群集节点用于 SOFS

* 每个节点必须至少包含两个本地磁盘

* 出于性能原因，必须使用**镜像复原**：
    * 对包含两个群集节点的 SOFS 使用**双向**镜像
    * 对包含三个（或更多）群集节点的 SOFS 使用**三向**镜像


* **建议对使用三向镜像的 SOFS 提供 3 个（或更多）群集节点**。
与使用 2 个群集节点和双向镜像设置的 SOFS 相比，这种设置提供的可伸缩性和存储复原能力更高。

* 必须使用 **Azure 高级磁盘**

* **建议**使用 **Azure 托管磁盘**

* **建议**使用新的**复原文件系统 (ReFS)** 将卷格式化
    * [SAP 说明 1869038 - SAP 对 ReFs 文件系统的 支持][1869038]
    * 请参阅“在存储空间直通中规划卷”的[选择文件系统][planning-volumes-s2d-choosing-filesystem]一章。
    * 确保安装此 [MS **KB4025334** 累积更新][kb4025334]。


* 可以使用 **DS 系列**或 **DSv2 系列** Azure VM 大小

* 若要获得良好的 VM 间网络性能以顺利进行存储空间直通磁盘同步，应使用至少能够提供**“高”网络带宽**的 VM 类型。
有关更多详细信息，请参阅 [DSv2 系列][dv2-series]和 [DS 系列][ds-series]规范。

* **建议****在存储池中将一些容量保留为未分配状态，或预留一些容量**。 这样，在驱动器发生故障后，便可以提供所需的空间来让卷“就地”修复，从而提高数据安全和性能。

 有关更多详细信息，请参阅[选择卷的大小][choosing-the-size-of-volumes-s2d]


* 必须在**自有的 Azure 可用性集**中部署 SOFS Azure VM

* 无需为 SOFS 文件共享网络名称（例如 <SAPGlobalHostName>）配置 Azure 内部负载均衡器，因为已经为 SAP (A)SCS 实例的 <(A)SCSVirtualHostname> 或者为 DBMS 进行此项配置。 SOFS 跨所有群集节点横向扩展负载，因此，<SAPGlobalHostName> 使用所有群集节点的本地 IP。


> [!IMPORTANT]
>无法更改指向 SAPGLOBALHOST 的 SAPMNT 文件共享。 SAP 不支持使用不同的共享名作为 sapmnt。
>[SAP 说明 2492395 - 是否可以更改共享名 sapmnt？][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>在两个群集中配置 SAP (A)SCS 实例和 SOFS

我们可能会在一个群集中部署 SAP (A)SCS 实例，这些实例具有自身的 SAP <SID> 群集角色。 SOFS 文件共享在具有另一群集角色的另一个群集中配置。

> [!IMPORTANT]
>在这种情况下，SAP (A)SCS 实例配置为使用 UNC 路径 \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\. 访问 SAP GLOBALHost。
>

![图 5：在两个群集中部署的 SAP (A)SCS 实例和 SOFS][sap-ha-guide-figure-8007]

_**图 5：**在两个群集中部署的 SAP (A)SCS 实例和 SOFS_

> [!IMPORTANT]
>在 Azure 云中，用于 SAP 和 SOFS 文件共享的每个群集必须部署在其自身的 Azure 可用性集中，确保在整个底层 Azure 基础结构中分散放置这些群集 VM。
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>将 SIOS 用作群集共享磁盘的通用文件共享


> [!IMPORTANT]
>SOFS 是建议用于构建高可用性文件共享的解决方案。
>
>但是，如果还想要为高可用性文件共享设置**灾难恢复**，则必须使用通用文件共享和 SISO 作为群集共享磁盘的技术。
>

通用文件共享是实现高可用性文件共享的另一个选项。

在此处，可将第三方 SIOS 解决方案用作群集共享磁盘。

## <a name="next-steps"></a>后续步骤

* [使用 SAP (A)SCS 实例的 Windows 故障转移群集和文件共享准备 Azure 基础结构以实现 SAP 高可用性][sap-high-availability-infrastructure-wsfc-file-share]

* [在 SAP (A)SCS 实例的 Windows 故障转移群集和文件共享中安装 SAP NetWeaver HA][sap-high-availability-installation-wsfc-shared-disk]

* [在 Azure 中为 UPD 存储部署双节点存储空间直通横向扩展文件服务器][deploy-sofs-s2d-in-azure]

* [Windows Server 2016 中的存储空间直通][s2d-in-win-2016]

* [深入探讨：存储空间直通中的卷][deep-dive-volumes-in-s2d]
