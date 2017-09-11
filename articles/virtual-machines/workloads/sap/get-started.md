---
title: "Azure VM 上的 SAP 入门 | Microsoft Docs"
description: "了解在 Microsoft Azure 中的虚拟机 (VM) 上运行的 SAP 解决方案"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: e69d96e5f18b4334a63445b08979c457d241e818
ms.contentlocale: zh-cn
ms.lasthandoff: 08/30/2017

---

# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>使用 Azure 承载和运行 SAP 工作负荷方案
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 
[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b 
[deploy-template-cli]:../../../resource-group-template-deploy.md
[deploy-template-portal]:../../../resource-group-template-deploy.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c


[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056
[ha-guide]:sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../../storage/common/storage-premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../../../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md

选择 Microsoft Azure 作为 SAP 就绪云合作伙伴，能够在可缩放、符合要求且经企业认证的平台上可靠运行任务关键型 SAP 工作负荷和方案。  可利用 Azure 的可伸缩性、灵活性和低成本特性。 由于 Microsoft 和 SAP 之间扩大了合作伙伴关系，因此，可以在 Azure 的各个开发/测试和生产方案中运行 SAP 应用程序 - 并且获得完全支持。 从 SAP NetWeaver 到 SAP S4/HANA、SAP BI，Linux 到 Windows，SAP HANA 到 SQL，我们都能满足客户的需求。 

除了在 Azure 上承载具有不同 DBMS 的 SAP NetWeaver 方案，可以在 Azure 上承载其他不同的 SAP 工作负荷方案，如 SAP BI。 可以在“Azure 虚拟机上的 SAP NetWeaver”部分中找到有关 Azure 本机虚拟机上的 SAP NetWeaver 部署的文档。 

Azure 具有本机 Azure 虚拟机产品/服务，它们的 CPU 和内存资源规模不断增大，以涵盖利用 SAP HANA 的 SAP 工作负载。 有关本主题的详细信息，请查找“Azure 虚拟机上的 SAP HANA”部分下的文档。

Azure for SAP HANA 的独特性是使 Azure 在竞争中脱颖而出的独特优势。 为了可以承载更多对内存和 CPU 资源要求苛刻的涉及 SAP HANA 的 SAP 方案，Azure 提供客户专用裸机硬件的使用，以便运行对于 S/4HANA 或其他 SAP HANA 工作负荷需要多达 20 TB（可扩大到 60 TB）内存的 SAP HANA 部署。 Azure 上的 SAP HANA（大型实例）这种独特 Azure 解决方案使你可以在专用裸机硬件上运行 SAP HANA，其 SAP 应用层或工作负荷中间软件层在本机 Azure 虚拟机中承载。 此解决方案在“Azure 上的 SAP HANA（大型实例）”部分中的几个文档中进行了记录。   

在 Azure 中承载 SAP 工作负荷方案还可能会导致需要使用 Azure Activity Directory 到不同 SAP 组件以及 SAP SaaS 或 PaaS 产品/服务的标识集成和单一登录。 在“AAD SAP 标识集成和单一登录”部分中介绍和记录了具有 Azure Active Directory (AAD) 和 SAP 实体的这类集成和单一登录方案的列表。


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>Azure 上 SAP HANA 上的 SAP HANA（大型实例）

### <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）概述和体系结构
标题：Azure 上的 SAP HANA（大型实例）概述和体系结构

摘要：此体系结构和技术部署指南提供的信息可帮助用户在 Azure 中的新型“Azure 上的 SAP HANA（大型实例）”上部署 SAP。 它并不打算成为涵盖 SAP 解决方案的具体设置的综合指南，而是旨在为初始部署和正在进行的操作提供有用信息。 它不应取代有关安装 SAP HANA 的 SAP 文档（或者包括该主题的多种 SAP 支持说明）。 它提供概述以及安装 Azure 上的 SAP HANA（大型实例）的其他详细信息。

更新时间：2017 年 7 月

[可在此处找到此指南](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）的基础结构与连接
标题：Azure 上的 SAP HANA（大型实例）的基础结构与连接

摘要：用户与 Microsoft 企业帐户团队之间完成购买 Azure 上的 SAP HANA（大型实例）后，需要进行各种网络配置以确保连接正确。  本文档概述必须共享的信息，其中以下信息是必需的。 本文档概述需要收集哪些信息以及需要运行哪些配置脚本。 

更新时间：2017 年 7 月

[可在此处找到此指南](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="install-sap-hana-in-sap-hana-on-azure-large-instances"></a>在 Azure 上的 SAP HANA中安装 SAP HANA（大型实例）
标题：在 Azure 上的 SAP HANA（大型实例）上安装 SAP HANA

摘要：本文档概述在 Azure 大型实例上安装 SAP HANA 的安装过程。 

更新时间：2017 年 7 月

[可在此处找到此指南](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复
标题：Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复

摘要：高可用性 (HA) 和灾难恢复 (DR) 是运行任务关键型 Azure 上的 SAP HANA（大型实例）服务器的非常重要方面。 必须咨询 SAP、系统集成商和/或 Microsoft，以便正确构建和实施适当的 HA/DR 策略。 必须考虑到与环境相关的一些重要因素，例如，恢复点目标 (RPO) 和恢复时间目标 (RTO)。  本文档介绍用于启用首选级别的 HA 和 DR 的选项。

更新时间：2016 年 12 月

[本文档可在此处找到](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>Azure 上的 SAP HANA（大型实例）的故障排除与监视
标题：Azure 上的 SAP HANA（大型实例）的故障排除与监视

摘要：本指南介绍对于在 Azure 上的 SAP HANA 环境中建立监视很有用的信息，以及其他故障排除信息。 

更新时间：2017 年 8 月

[本文档可在此处找到](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sap-hana-on-azure-virtual-machines"></a>Azure 虚拟机上的 SAP HANA

### <a name="getting-started-with-sap-hana-on-azure"></a>Azure 上的 SAP HANA 入门
标题：在 Azure VM 上手动安装 SAP HANA 的快速入门指南

摘要：本快速入门指南帮助通过手动安装 SAP NetWeaver 7.5 和 SAP HANA SP12，在 Azure VM 上设置单实例 SAP HANA 系统。 本指南假设读者熟悉 Azure IaaS 的基本概念，例如如何通过 Azure 门户或 Powershell/CLI 部署虚拟机或虚拟网络，包括使用 json 模板的选项。 此外还预期读者熟悉 SAP HANA、SAP NetWeaver 以及本地安装方式。

更新时间：2017 年 6 月

[可在此处找到此指南](hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="s4hana-sap-cal-deployment-on-azure"></a>Azure 上的 S/4HANA SAP CAL 部署
标题：在 Azure 上部署 SAP S/4HANA 或 BW/4HANA

摘要：本指南帮助演示如何使用 SAP Cloud Appliance Library 在 Azure 上部署 SAP S/4HANA。 SAP Cloud Appliance Library 是 SAP 提供的一项服务，可用于在 Azure 上部署 SAP 应用程序。 本指南提供分步部署说明。

更新时间：2017 年 6 月

[可在此处找到此指南](cal-s4h.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="high-availability-of-sap-hana-in-azure-virtual-machines"></a>Azure 虚拟机中 SAP HANA 的高可用性
标题：Azure 虚拟机上 SAP HANA 的高可用性

摘要：本指南引导完成 SUSE 12 OS 和 SAP HANA 的高可用性配置，以便 HANA 系统复制实现自动故障转移。 本指南专用于 SUSE 和 Azure 虚拟机。 本指南尚不适用于 Red Hat、裸机、私有云或其他非 Azure 公有云部署。

更新时间：2017 年 6 月

[可在此处找到此指南](sap-hana-high-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-backup-overview-on-azure-vms"></a>Azure VM 上的 SAP HANA 备份概述
标题：Azure 虚拟机上的 SAP HANA 备份指南

摘要：本指南介绍有关在 Azure 虚拟机上运行 SAP HANA 时的备份可能性的基本信息。

更新时间：2017 年 3 月

[可在此处找到此指南](sap-hana-backup-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="sap-hana-file-level-backup-on-azure-vms"></a>Azure VM 上的 SAP HANA 文件级备份
标题：基于存储快照的 SAP HANA 备份

摘要：本指南介绍在 Azure 虚拟机上运行 SAP HANA 时如何在 Azure VM 上使用基于快照的备份。

更新时间：2017 年 3 月

[可在此处找到此指南](sap-hana-backup-file-level.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="sap-hana-snapshot-based-backups-on-azure-vms"></a>Azure VM 上基于快照的 SAP HANA 备份
标题：文件级别的 SAP HANA Azure 备份

摘要：本指南介绍在 Azure 虚拟机上运行 SAP HANA 时如何使用 SAP HANA 文件级备份

更新时间：2017 年 3 月

[可在此处找到此指南](sap-hana-backup-storage-snapshots.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Azure 虚拟机上部署的 SAP NetWeaver

### <a name="deploy-sap-ides-system-on-windows-and-sql-server-through-sap-cal-on-azure"></a>通过 Azure 上的 SAP CAL 在 Windows 和 SQL Server 上部署 SAP IDES 系统
标题：测试 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver 

摘要：本文档介绍如何使用 SAP Cloud Appliance Library 在 Azure 上部署基于 Windows 和 SQL Server 的 SAP IDES 系统。 SAP Cloud Appliance Library 是一项 SAP 服务，可用于在 Azure 上部署 SAP 产品。 本文档分步介绍如何部署 SAP IDES 系统。 IDE 系统只是众多应用程序（数十个）中的一个例子，这些应用程序均可通过 Microsoft Azure 上的 SAP Cloud 设备进行部署。

更新时间：2017 年 6 月

[可在此处找到此指南](cal-ides-erp6-erp7-sp3-sql.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


### <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Azure 上 SUSE Linux 中 NetWeaver 的快速入门指南
标题：测试 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver 

摘要：此文介绍在 Microsoft Azure SUSE Linux 虚拟机 (VM) 上运行 SAP NetWeaver 时应注意的各个事项。 Azure 上的 SUSE Linux VM 已正式支持 SAP NetWeaver。 有关 Linux 版本、SAP 内核版本和其他版本的所有详细信息，请参阅 SAP 说明 1928533“Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型”。

更新时间：2016 年 9 月

[可在此处找到此指南](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>规划和实现
标题：SAP NetWeaver 的 Azure 虚拟机规划和实现

摘要：如果正考虑在 Azure 虚拟机中运行 SAP NetWeaver，可从本文档着手。 此规划和实现指南有助于评估现有或计划的基于 SAP NetWeaver 的系统是否可以部署到 Azure 虚拟机环境。 它介绍了多个 SAP NetWeaver 部署方案，并包括特定于 Azure 的 SAP 配置。 该文列出并说明了在 SAP/Azure 端运行混合 SAP 布局产品时所需的所有必要配置信息。 此外，还介绍了为确保 IaaS 上基于 SAP NetWeaver 的系统实现高可用性可以采取的措施。

更新时间：2017 年 6 月

[可在此处找到此指南][planning-guide]

### <a name="high-availability-configurations-of-sap-netweaver-in-azure-vms"></a>Azure VM 中 SAP NetWeaver 的高可用性配置
标题：SAP NetWeaver 的 Azure 虚拟机高可用性

摘要：本文档介绍使用 Azure 资源管理器部署模型在 Azure 中部署高可用性 SAP 系统可采用的步骤。 我们演练以下主要任务。 本文档介绍单一故障点组件（如高级业务应用程序编程 (ABAP) SAP 中心服务 (ASCS)/SAP 中心服务 (SCS) 和数据库管理系统 (DBMS)）和冗余组件（如 SAP 应用程序服务器）在 Azure VM 中运行时，如何保护这些组件。 本文档介绍并显示在 Azure 的 Windows Server 故障转移群集中安装和配置高可用性 SAP 系统的分步示例。

更新时间：2017 年 6 月

[可在此处找到此指南](high-availability-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="realizing-multi-sid-deployments-of-sap-netweaver-in-azure-vms"></a>在 Azure VM 中实现 SAP NetWeaver 的多 SID 部署
标题：创建 SAP NetWeaver 多 SID 配置 

摘要：本文档是“Azure VM 上 SAP NetWeaver 的高可用性”文档的补充。 借助 2016 年 9 月 Azure 中引入的新功能，现可在两台 Azure VM 中部署多个 SAP NetWeaver ASCS/SCS 实例。 借助此类配置，可以减少需部署的 VM 数量以实现高度可用的 SAP NetWeaver 配置。 本指南介绍如何设置此类多 SID 配置。

更新时间：2016 年 12 月

[可在此处找到此指南](high-availability-multi-sid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>在 Azure VM 中部署 SAP NetWeaver
标题：SAP NetWeaver 的 Azure 虚拟机部署

摘要：本文档提供将 SAP NetWeaver 软件部署到 Azure 中虚拟机的过程指导。 此文重点介绍三种特定部署方案，主要侧重于启用 SAP 的 Azure 监视扩展，包括针对 SAP 的 Azure 监视扩展的故障排除建议。 此文假定已阅读规划和实现指南。

更新时间：2017 年 6 月

[可在此处找到此指南][deployment-guide]

### <a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS 部署指南
标题：SAP NetWeaver 的 Azure 虚拟机 DBMS 部署

摘要：此文介绍了应与 SAP 一起运行的 DBMS 系统的规划和实现注意事项。 在第一部分中，列出并提供了一般注意事项。 此文的以下部分与在 Azure 中部署 SAP 所支持的不同 DBMS 相关。 提供的不同 DBMS 为 SQL Server、SAP ASE 和 Oracle。 这些特定部分讨论了在 Azure 上将 SAP 系统与这些 DBMS 一起运行时必须考虑的注意事项。 提供了 Azure 上的不同 DBMS 支持的备份和高可用性方法等主题，以便用于 SAP 应用程序。

更新时间：2017 年 6 月

[可在此处找到此指南][dbms-guide]

### <a name="using-azure-site-recovery-for-sap-workload"></a>将 Azure Site Recovery 用于 SAP 工作负载
标题：SAP NetWeaver：使用 Azure Site Recovery 构建灾难恢复解决方案 

摘要：本文档介绍了将 Azure Site Recovery 用于处理灾难恢复应用场景的方法。 使用 Azure Site Recovery 服务将 Azure 用作本地 SAP 环境的灾难恢复位置的场景。 本文档介绍的另一个应用场景是 Azure 到 Azure (A2A) 灾难恢复场景，以及如何使用 Azure Site Recovery 对其进行管理。  

更新时间：2017 年 8 月

[可在此处找到此指南](http://aka.ms/asr-sap)


