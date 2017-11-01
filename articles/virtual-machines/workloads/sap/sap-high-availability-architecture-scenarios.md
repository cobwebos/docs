---
title: "Azure 虚拟机上 SAP NetWeaver 的 高可用性体系结构和方案 | Microsoft Docs"
description: "Azure 虚拟机上 SAP NetWeaver 的高可用性 (HA) 体系结构和方案"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 867fe2835418a48e4e616d8137ba9fa4182c8fb7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>SAP NetWeaver 的高可用性体系结构和方案

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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


## <a name="definition-of-terminologies"></a>术语定义

术语“高可用性 (HA)”与一系列技术相关，这些技术通过相同数据中心内受冗余、容错或故障转移保护的组件，为 IT 服务提供业务连续性，以最大程度减少 IT 中断的情况。 在本例中，我们会在一个 Azure 区域中实现高可用性。

灾难恢复 (DR) 的目标也是最大程度地减少 IT 服务中断以及恢复服务，但此目标是通过不同的数据中心（相距数百公里）实现的。 在本例中，这通常发生同一地缘政治区域内的不同 Azure 区域或客户建立的区域之间。


## <a name="overview-of-high-availability"></a>高可用性概述
Azure 中的 SAP 高可用性的相关介绍可分为三个部分：

* Azure 基础结构高可用性，例如计算 (VM)、网络、存储等的 HA，以及提高 SAP 应用程序可用性的优点。

* 利用 Azure 基础结构 VM 重新启动来实现 SAP 应用程序的“更高可用性”

  如果确定不使用 Windows Server 故障转移群集 (WSFC) 或 Linux 上的 Pacemake 等功能，可利用 Azure VM 重启来保护 SAP 系统，以防止 Azure 物理服务器基础结构和整体基础 Azure 平台发生计划内和计划外的停机。


* SAP 应用程序高可用性

  要实现完整的 SAP 系统高可用性，我们需要保护所有关键的 SAP 系统组件，例如：
  * 冗余 SAP 应用程序服务器，和
  * 唯一的组件（例如单一故障点 (SPOF)），如
    * SAP (A)SCS 实例和
    *  DBMS。


Azure 中的 SAP 高可用性与本地物理或虚拟环境中的 SAP 高可用性在相比之下有一些差异。 以下文章 [Microsoft Windows 上使用 VMware 和 Hyper-V 的虚拟环境中的 SAP NetWeaver 高可用性和业务连续性][sap-ha-bc-virtual-env-hyperv-vmware-white-paper]描述了 Windows 上虚拟环境中的标准 SAP 高可用性配置。

不同于 Windows，Linux 没有集成 sapinst 的 SAP-HA 配置。 有关 Linux 的本地 SAP HA 的详细信息，请参阅[高可用性合作伙伴信息][sap-ha-partner-information]。

## <a name="azure-infrastructure-high-availability"></a>Azure 基础结构高可用性

### <a name="single-instance-virtual-machine-sla"></a>单实例虚拟机 SLA

目前存在一个使用高级存储的 99.9% 的单一 VM SLA。 若要大致了解单一 VM 可用性的情形，请计算可用的不同 [Azure 服务级别协议][azure-sla]的积。

计算的基数是每月 30 天，即 43,200 分钟。 因此，0.05% 停机时间就相当于 21.6 分钟。 像往常一样，不同服务的可用性按以下方式相乘：

(可用性服务 #1/100) * (可用性服务 #2/100) * (可用性服务 #3/100) \*…

例如：

(99.95/100) * (99.9/100) * (99.9/100) = 0.9975，即总体可用性为 99.75%。

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>同一个可用性集中虚拟机的多个实例
对于在同一可用性集中部署两个或更多实例的所有虚拟机，我们保证你至少在 99.95% 的时间内具有至少一个实例的虚拟机连接。

当两个或多个 VM 是同一可用性集的一部分时，基础 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域。

容错域保证 VM 部署在不共享通用电源和网络交换机的不同硬件上。 当服务器、网络交换机或电源发生计划外故障时，只有一个 VM 会受到影响。

更新域保证在计划内的 Azure 基础结构维护期间，不同的 VM 不会同时重新启动，而是一次只重新启动一个 VM。

有关详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性][azure-virtual-machines-manage-availability]。

可用性集用于实现以下项的高可用性：

* 冗余 SAP 应用程序服务器  

* 具有两个或多个节点（例如 VM）的群集，保护诸如 SAP (A)SCS 实例和 DBMS 之类的 SPOF

### <a name="virtual-machine-vm-planned-and-unplanned-maintenance"></a>虚拟机 (VM) 计划内维护和计划外维护

有两种类型的 Azure 平台事件可能影响到虚拟机的可用性：计划内维护和计划外维护。

* 计划内维护事件是指由 Microsoft 对底层 Azure平台进行定期更新，以改进虚拟机运行时所在的平台基础结构的总体可靠性、性能和安全性。

* 计划外维护事件见于虚拟机所在硬件或物理基础结构出现某类故障的情况。 此类故障可能包括：本地网络故障、本地磁盘故障，或者其他机架级别的故障。 检测到此类故障时，Azure 平台会自动将虚拟机从所在的异常物理服务器迁移到正常运行的物理服务器。 此类事件很少见，但也会导致虚拟机重启。

有关详细信息，请参阅[在 Azure 中管理 Windows 虚拟机的可用性][azure-virtual-machines-manage-availability]。

### <a name="azure-storage-redundancy"></a>Azure 存储冗余
始终复制 Microsoft Azure 存储帐户中的数据以确保持久性和高可用性，并且即使在遇到临时硬件故障时也符合 Azure 存储 SLA 要求。

由于 Azure 存储默认保留 3 个数据映像，因此不需要部署跨多个 Azure 磁盘的 RAID5 或 RAID1。

有关详细信息，请参阅 [Azure 存储复制][azure-storage-redundancy]。

### <a name="azure-managed-disks"></a>Azure 托管磁盘
托管磁盘是 Azure 资源管理器中的一种新资源类型，可用于替代 Azure 存储帐户中存储的 VHD。 托管磁盘与其所附加到的虚拟机可用性集自动对齐，因而增加了虚拟机和虚拟机上所运行服务的可用性。
有关详细信息，请参阅 [Azure 托管磁盘概述][azure-storage-managed-disks-overview]。

建议使用托管磁盘，因为托管磁盘可简化虚拟机的部署和管理。
**SAP 当前仅支持高级托管磁盘**。 有关详细信息，请参阅 SAP 说明 [1928533]。

## <a name="utilizing-azure-infrastructure-ha-to-achieve-sap-application-higher-availability"></a>利用 Azure 基础结构 HA 来实现 SAP 应用程序的“更高”可用性

如果确定不使用 Windows Server 故障转移群集 (WSFC) 或 Linux 上的 Pacemaker（当前仅支持 SLES 12 及更高版本）等功能，可利用 Azure VM 重启来保护 SAP 系统，以防止 Azure 物理服务器基础结构和整体基础 Azure 平台发生计划内和计划外的停机。

以下文档[利用 Azure 基础结构 VM 重新启动来实现 SAP 系统的“更高可用性”][sap-higher-availability]对此方法进行了详述。

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Azure IaaS 上的 SAP 应用程序高可用性

要实现完整的 SAP 系统高可用性，我们需要保护所有关键的 SAP 系统组件，例如：

* 冗余 SAP 应用程序服务器，和

* 唯一的组件（例如单一故障点 (SPOF)），如
  * SAP (A)SCS 实例和
  *  DBMS。

我们将在下面详细讨论如何实现这三个关键 SAP 系统组件的高可用性。

### <a name="high-availability-for-sap-application-servers"></a>SAP 应用程序服务器的高可用性

> 此章节适用于以下两个系统：
>
> ![Windows][Logo_Windows] Windows 和 ![Linux][Logo_Linux] Linux
>

对于 SAP 应用程序服务器和对话实例，通常不需要特定的高可用性解决方案。 通过冗余实现高可用性，并且可在 Azure 虚拟机的不同实例中配置多个对话实例。 应该至少有两个 SAP 应用程序实例安装在两个 Azure 虚拟机实例中。

![图 1：高可用性 SAP 应用程序服务器][sap-ha-guide-figure-2000]

图 1：高可用性 SAP 应用程序服务器

必须将所有托管 SAP 应用程序服务器实例的虚拟机放置在同一个 Azure 可用性集中。 Azure 可用性集确保：

* 所有虚拟机都是同一个升级域的一部分。 例如，升级域确保虚拟机不在计划的维护停机时间期间同时更新。
[升级域][planning-guide-3.2.2]一章已介绍了在 Azure 缩放单位内不同升级域和容错域上构建的基本功能。

* 所有虚拟机都是同一个容错域的一部分。 例如，容错域确保将虚拟机部署为任何单一故障点都不会影响所有虚拟机的可用性。

Azure 缩放单位内的 Azure 可用性集可使用不限数目的容错和升级域。 这意味着，如果将一些 VM 放在一个可用性集中，迟早会有多个 VM 出现在同一个容错或升级域中。

将一些 SAP 应用程序服务器实例部署在其专用 VM 中，并假设有 5 个升级域，最终将出现下图所示的情况。 可用性集内的容错域和更新域的实际数目上限将来可能会变化：

![图 2：Azure 可用性集中的 SAP 应用程序服务器的高可用性][planning-guide-figure-3000]
_****图 2：Azure 可用性集中的 SAP 应用程序的 HA_可在本文档中找到更多详细信息：[管理虚拟机的可用性][virtual-machines-manage-availability]。


SAP NetWeaver 文档的 Azure 虚拟机计划和实现的 [Azure 可用性集][planning-guide-3.2.3]章节中介绍了 Azure 可用性集。


仅限非托管磁盘：由于 Azure 存储帐户是潜在的单一故障点，因此必须至少有两个 Azure 存储帐户，并在其中至少分配两个虚拟机。 在理想的设置中，运行 SAP 对话实例的每个虚拟机的磁盘都应部署在不同的存储帐户中。

> [!IMPORTANT]
>
> 我们强烈建议你使用 Azure 托管磁盘进行 SAP HA 安装，因为这些托管磁盘与其所附加到的虚拟机可用性集自动对齐，因而增加了虚拟机和虚拟机上所运行服务的可用性。  
>


### <a name="high-availability-architecture-for-the-sap-ascs-instance"></a>SAP (A)SCS 实例的高可用性体系结构

### <a name="high-availability-architecture-for-the-sap-ascs-instance-on-windows"></a>Windows 上 SAP (A)SCS 实例的高可用性体系结构


> ![Windows][Logo_Windows] Windows
>

可以使用 Windows Server 故障转移群集 (WSFC) 解决方案来保护 SAP (A)SCS 实例。 有两种变体的解决方案：

* 使用群集共享磁盘群集化 SAP (A)SCS 实例

   可以在此文档中找到与使用群集共享磁盘的 HA 体系结构相关的详细信息：[使用群集共享磁盘群集化 Windows 故障转移群集上的 SAP (A)SCS 实例][sap-high-availability-guide-wsfc-shared-disk]。   

* 使用文件共享群集化 SAP (A)SCS 实例

  可以在此文档中找到与使用文件共享的 HA 体系结构相关的详细信息：[使用文件共享群集化 Windows 故障转移群集上的 SAP (A)SCS 实例][sap-high-availability-guide-wsfc-file-share]。

### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Linux 上 SAP (A)SCS 实例的高可用性


> ![Linux][Logo_Linux] Linux
>

你可以在本文档中找到有关使用 SUSE Linux Enterprise Server 群集框架群集化 SAP (A)SCS 实例的详细信息：[SUSE Linux Enterprise Server 的 Azure VM 上针对 SAP 应用程序的 SAP NetWeaver 的高可用性][sap-suse-ascs-ha]。

### <a name="sap-netweaver-multi-sid-configuration-for-clustered-sap-ascs-instance"></a>群集 SAP (A)SCS 实例的 SAP NetWeaver 多 SID 配置

> ![Windows][Logo_Windows] Windows
>
>目前，多 SID 仅支持 Windows Server 故障转移群集 (WSFC)。 支持多 SID 使用文件共享和共享磁盘。
>

你可以在下面的体系结构文档中找到有关多 SID HA 体系结构的详细信息：

* [使用 Windows Server 故障转移群集和文件共享实现 SAP (A)SCS 实例多 SID 高可用性][sap-ascs-ha-multi-sid-wsfc-file-share]

* [使用 Windows Server 故障转移群集和共享磁盘实现 SAP (A)SCS 实例多 SID 高可用性][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>高可用性 DBMS 实例

DBMS 也是 SAP 系统中的单一接触点。 需要使用高可用性解决方案保护它。 下图显示了 Azure 中使用 Windows Server 故障转移群集和 Azure 内部负载均衡器的 SQL Server Always On 高可用性解决方案。 SQL Server AlwaysOn 使用其自身的 DBMS 复制功能复制 DBMS 数据和日志文件。 在此情况下，无需用于简化整个设置的群集共享磁盘。

![图 3：使用 SQL Server Always On 的高可用性 SAP DBMS 示例][sap-ha-guide-figure-2003]

图 3：使用 SQL Server Always On 的高可用性 SAP DBMS 示例

有关使用 Azure 资源管理器部署模型在 Azure 中群集化 SQL Server DBMS 的详细信息，请参阅以下文章：

* [使用 Resource Manager 在 Azure 虚拟机中手动配置 Always On 可用性组][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [在 Azure 中为 Always On 可用性组配置 Azure 内部负载均衡器][virtual-machines-windows-portal-sql-alwayson-int-listener]

有关使用 Azure 资源管理器部署模型在 Azure 中群集化 SAP HANA DBMS 的详细信息，请参阅以下文章：

* [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
