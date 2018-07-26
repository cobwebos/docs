---
title: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 243aecbe3627a6cc72de1bc98c301e8fa632ec36
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075220"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>适用于 SAP 工作负荷的 SAP ASE Azure 虚拟机 DBMS 部署

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
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
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
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
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

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
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
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



本文档介绍在 Azure IaaS 中部署 SAP 时要考虑的多个不同领域。 在阅读本文档之前，应已经阅读了[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)文档以及 [Azure 文档上的 SAP 工作负荷](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 

## <a name="specifics-to-sap-ase-on-windows"></a>有关 Windows 上的 SAP ASE 的具体信息
自从有了 Microsoft Azure，可以将现有 SAP ASE 应用程序迁移到 Azure 虚拟机。 借助 Azure 虚拟机中的 SAP ASE，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SAP ASE，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。

Azure 虚拟机的 SLA 可在此处找到：<https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure 提供了许多不同的虚拟机类型，可运行最小的 SAP 系统和布局，也可运行包含成千上万用户的大型 SAP 系统和布局。 经过 SAP 认证的不同 VM SKU 的 SAP 大小调整 SAPS 数目会在 SAP 说明 [1928533] 中提供。

[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中关于 Azure 存储使用情况、SAP VM 部署或 SAP 监视的表述和建议也适用于 SAP ASE 部署。

### <a name="sap-ase-version-support"></a>SAP ASE 版本支持
SAP 目前支持 SAP ASE 版本 16.0，可与 SAP Business Suite 产品搭配使用。 适用于 SAP ASE 服务器的所有更新或者适用于可与 SAP Business Suite 产品搭配使用的 JDBC 和 ODBC 驱动程序的所有更新，仅通过 SAP Service Marketplace (<https://support.sap.com/swdc>) 提供。

请勿直接从 Sybase 网站下载适用于 SAP ASE 服务器或适用于 JDBC 和 ODBC 驱动程序的更新。 有关支持在本地和 Azure 虚拟机中与 SAP 产品搭配使用的修补程序的详细信息，请参阅下列 SAP 说明：

* [1590719]
* [1973241]

有关在 SAP ASE 上运行 SAP Business Suite 的常规信息可在 [SCN](https://www.sap.com/community/topic/ase.html) 中找到

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 相关 SAP ASE 的 SAP ASE 配置准则
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE 部署的结构
SAP ASE 可执行文件应位于或安装在 VM 的 OS 磁盘的系统驱动器（驱动器 c:\)）中。 一般情况下，大部分 SAP ASE 系统和工具数据库的工作负荷并不很高。 因此，系统和工具数据库（master、model、saptools、sybmgmtdb、sybsystemdb）可以保留在驱动器 C:\ 上。 

唯一的例外就是临时数据库，在某些 SAP ERP 和所有 BW 工作负荷中，它可能需要较高的数据卷或 I/O 操作卷。 VM 的 OS 磁盘（驱动器 c:\)）不能提供的卷或 IOPS。

根据用于安装的 SAPInst/SWPM 版本，SAP ASE 实例配置可能如下：

* 安装 SAP ASE 时创建的单个 SAP ASE tempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及 SAP 安装例程所创建的附加 saptempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及为满足 ERP/BW 特定 tempdb 需求而手动创建的附加 tempdb（例如，遵循 SAP 说明 [1752266]）

从特定的 ERP 或所有 BW 工作负荷的性能考虑，可以采用以下做法：将额外创建的 tempdb 的 tempdb 设备存储在 C:\ 以外的驱动器上。 如果没有附加 tempdb，建议创建一个（SAP 说明 [1752266]）。

在这类系统中，应针对额外创建的 tempdb 执行下列步骤：

* 将第一个 tempdb 设备移至 SAP 数据库的第一个设备
* 将 tempdb 设备添加到每个包含 SAP 数据库设备的 VHD

此配置使 tempdb 使用的空间比系统驱动器可以提供的还多。 作为参考，可以在运行于本地的现有系统上检查 tempdb 设备的大小。 或者，这类配置针对 tempdb 启用系统驱动器无法提供的 IOPS 数目。 本地运行的系统可以用来监视 tempdb 的 I/O 工作负荷。

永远不要将任何 SAP ASE 设备放入 VM 的 D:\ 驱动器。 对于 SAP ASE，此建议也适用于 tempdb，即使 tempdb 中保留的对象只是暂时性的也适用。

有关数据和事务日志文件部署的信息，请参阅[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的表述和建议。 对于基于 Windows 的部署，建议使用 Windows 存储空间来构建具有足够 IOPS、吞吐量和卷的带区集。  

#### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先应用库压缩，再上传到 Azure，原因有以下几点：

* 减少要上传到 Azure 的数据量
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短
* 较小的数据库大小可以降低磁盘分配的成本

数据和 LOB 压缩可以在 Azure 虚拟机托管的 VM 中运行，如同在本地运行一样。 有关如何检查是否已在现有 SAP ASE 数据库中使用压缩的更多详细信息，请查看 SAP 说明 [1750510]。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 来监视数据库实例
对于使用 SAP ASE 作为数据库平台的 SAP 系统，可以将 DBACockpit 作为事务 DBACockpit 中的嵌入式浏览器窗口或作为 Webdynpro 来访问。 但是，完整的数据库监视和管理功能仅在 DBACockpit 的 Webdynpro 实现中提供。

与本地系统一样，需执行数个步骤，才能启用 DBACockpit 的 Webdynpro 实现所使用的所有 SAP NetWeaver 功能。 遵循 SAP 说明 [1245200]，启用 Webdynpro 并生成必要的功能。 遵循上述说明中的指示操作时，还将配置 Internet Communication Manager (icm) 以及用于 http 和 https 连接的端口。 http 的默认设置如下所示：

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

事务 DBACockpit 中生成的链接如下所示：

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

需要确保 ICM 使用完全限定的主机名，并且此名称可在打开 DBACockpit 的计算机上解析，具体取决于如何将托管 SAP 系统的 Azure 虚拟机连接到 AD 和 DNS。 请参阅 SAP 说明 [773830]，了解 ICM 如何基于配置文件参数来确定完全限定的主机名，并在必要时显式设置 icm/host_name_full 参数。

如果在仅限云方案中部署了 VM，但未在本地与 Azure 之间建立跨界连接，则需要定义一个公共 IP 地址和一个 domainlabel。 VM 的公共 DNS 名称格式如下所示：

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

可以在[此处][virtual-machines-azurerm-versus-azuresm]找到有关 DNS 名称的更多详细信息。

将 SAP 配置文件参数 icm/host_name_full 设置为 Azure VM 的 DNS 名称，其链接可能如下所示：

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

在此情况下，需要确保：

* 在 Azure 门户中，针对用来与 ICM 通信的 TCP/IP 端口，向网络安全组添加入站规则
* 针对用来与 ICM 通信的 TCP/IP 端口，向 Windows 防火墙配置添加入站规则

针对自动导入的所有可用修正，建议定期应用适用于 SAP 版本的修正集合 SAP 说明：

* [1558958]
* [1619967]
* [1882376]

可以在以下 SAP 说明中找到有关 SAP ASE 的 DBA Cockpit 的更多详细信息：

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE 的备份/恢复注意事项
将 SAP ASE 部署到 Azure 时，必须检查备份方法。 即使对于非生产系统，也必须定期备份 SAP 数据库。 由于 Azure 存储会保留三个映像，在补救存储崩溃方面，备份可能变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 

在 Azure 中备份和还原数据库的方式与在本地一样。 请参阅 SAP 说明：

* [1588316]
* [1585981]

，以获取有关创建转储配置和计划备份的详细信息。 根据策略和需求，可以在现有的某个磁盘上配置磁盘的数据库和日志转储，也可以添加一个附加磁盘以供备份使用。 为了减少在发生错误时丢失数据的风险，建议使用不带任何数据库文件的磁盘。

除了数据和 LOB 压缩，SAP ASE 还提供备份压缩。 若要让数据库和日志转储耗用较少的空间，建议使用备份压缩。 有关详细信息，请参阅 SAP 说明 [1588316]。 如果打算从 Azure 虚拟机将备份或包含备份转储的 VHD 下载至本地，则压缩备份对于减少要传输的数据量也很重要。

请勿使用驱动器 D:\ 作为数据库或日志转储目标。

#### <a name="performance-considerations-for-backupsrestores"></a>备份/还原的性能注意事项
与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 请记住，备份压缩会耗用 CPU 资源。 备份压缩的 CPU 消耗可在较少数目的 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

若要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 在 SAP ASE 级别创建转储配置，该配置将使用多个目标目录来写入转储

基于已装载的多个磁盘对磁盘卷划分带区，这已在[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中进行了讨论。 有关在 SAP ASE 转储配置中使用多个目录的详细信息，请参阅存储过程 sp_config_dump 的相关文档，此存储过程用于在 [Sybase 信息中心](http://infocenter.sybase.com/help/index.jsp)创建转储配置。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 的灾难恢复
#### <a name="data-replication-with-sap-sybase-replication-server"></a>使用 SAP Sybase 复制服务器复制数据
利用 SAP Sybase 复制服务器 (SRS)，SAP ASE 可提供热备用解决方案，以异步方式将数据库事务传输到远程位置。 

与在本地一样，也可以在 Azure 虚拟机托管的 VM 中正常安装和运行 SRS。

SAP ASE HADR 不需要 Azure 内部负载均衡器，并且不依赖于 OS 级别的群集。 它在 Azure Windows 和 Linux VM 上工作。 有关 SAP ASE HADR 的详细信息，请参阅 [SAP ASE HADR 用户指南](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)。

## <a name="specifics-to-sap-ase-on-linux"></a>有关 Linux 上的 SAP ASE 的具体信息
自从有了 Microsoft Azure，可以轻松地将现有 SAP ASE 应用程序迁移到 Azure 虚拟机。 借助虚拟机中的 SAP ASE，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SAP ASE，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。

若要部署 Azure VM，请务必熟悉可在此处找到的官方 SLA：<https://azure.microsoft.com/support/legal/sla>

SAP 说明 [1928533] 将提供 SAP 大小调整信息以及经过 SAP 认证的 VM SKU 列表。 其他适用于 Azure 虚拟机的 SAP 大小调整文档可以在 <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> 和 <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx> 中找到

有关 Azure 存储使用情况、SAP VM 部署或 SAP 监视的表述与建议适用于 SAP ASE 与 SAP 应用程序的联合部署，如本文档前四章所述。

下面这两个 SAP 说明提供有关 Linux 版 ASE 和云端版 ASE 的常规信息：

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>SAP ASE 版本支持
SAP 目前支持 SAP ASE 版本 16.0，可与 SAP Business Suite 产品搭配使用。 适用于 SAP ASE 服务器的所有更新或者适用于可与 SAP Business Suite 产品搭配使用的 JDBC 和 ODBC 驱动程序的所有更新，仅通过 SAP Service Marketplace (<https://support.sap.com/swdc>) 提供。

与本地安装一样，不要直接从 Sybase 网站下载适用于 SAP ASE 服务器或适用于 JDBC 和 ODBC 驱动程序的更新。 有关支持在本地和 Azure 虚拟机中与 SAP Business Suite 产品搭配使用的补丁的详细信息，请参阅下列 SAP 说明：

* [1590719]
* [1973241]

有关在 SAP ASE 上运行 SAP Business Suite 的常规信息可在 [SCN](https://www.sap.com/community/topic/ase.html) 中找到

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 相关 SAP ASE 的 SAP ASE 配置准则
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE 部署的结构
SAP ASE 可执行文件应位于或安装在 VM 的根文件系统 ( /sybase ) 中。 一般情况下，大部分 SAP ASE 系统和工具数据库的工作负荷并不很高。 因此，系统和工具数据库（master、model、saptools、sybmgmtdb、sybsystemdb）可以存储在根文件系统上。 

唯一的例外就是临时数据库，在某些 SAP ERP 和所有 BW 工作负荷中，它可能需要较高的数据卷或 I/O 操作卷。 VM 的 OS 磁盘不能提供的卷或 IOPS 

根据用于安装系统的 SAPInst/SWPM 版本，数据库可能包含：

* 安装 SAP ASE 时创建的单个 SAP ASE tempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及 SAP 安装例程所创建的附加 saptempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及为满足 ERP/BW 特定 tempdb 需求而手动创建的附加 tempdb（例如，遵循 SAP 说明 [1752266]）

从特定的 ERP 或所有 BW 工作负荷的性能考虑，可以采用以下做法：将额外创建的 tempdb（通过 SWPM 或手动）的 tempdb 设备存储在单独的文件系统上，该文件系统可以用单个 Azure 数据磁盘或者跨多个 Azure 数据磁盘的 Linux RAID 表示。 如果没有附加 tempdb，建议创建一个（SAP 说明 [1752266]）。

在这类系统中，应针对额外创建的 tempdb 执行下列步骤：

* 将第一个 tempdb 目录移至 SAP 数据库的第一个文件系统
* 将 tempdb 目录添加到每个包含 SAP 数据库文件系统的磁盘

此配置使 tempdb 使用的空间比系统驱动器可以提供的还多。 作为参考，可以在运行于本地的现有系统上检查 tempdb 设备的大小。 或者，这类配置针对 tempdb 启用系统驱动器无法提供的 IOPS 数目。 本地运行的系统可以用来监视 tempdb 的 I/O 工作负荷。

永远不要将任何 SAP ASE 目录放入 VM 的 /mnt 或 /mnt/resource。 对于 SAP ASE，此建议也适用于 tempdb，即使 tempdb 中保留的对象只是暂时性的也适用。 因为 /mnt 或 /mnt/resource 是默认的 Azure VM 临时空间，这不是永久的。 可以在[此文][virtual-machines-linux-how-to-attach-disk]中找到有关 Azure VM 临时空间的更多详细信息

有关数据和事务日志文件部署的信息，请参阅[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中的表述和建议。 对于基于 Linux 的部署，建议使用 LVM 或 MDADM 来构建具有足够 IOPS、吞吐量和卷的带区集。 

#### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先应用库压缩，再上传到 Azure，原因有以下几点：

* 减少要上传到 Azure 的数据量
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短
* 较小的数据库大小可以降低磁盘分配的成本

数据和 LOB 压缩可以在 Azure 虚拟机托管的 VM 中运行，如同在本地运行一样。 有关如何检查是否已在现有 SAP ASE 数据库中使用压缩的更多详细信息，请查看 SAP 说明 [1750510]。 有关数据库压缩的详细信息，请参阅 SAP 说明 [2121797]。

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>使用 DBACockpit 来监视数据库实例
对于使用 SAP ASE 作为数据库平台的 SAP 系统，可以将 DBACockpit 作为事务 DBACockpit 中的嵌入式浏览器窗口或作为 Webdynpro 来访问。 但是，完整的数据库监视和管理功能仅在 DBACockpit 的 Webdynpro 实现中提供。

与本地系统一样，需执行数个步骤，才能启用 DBACockpit 的 Webdynpro 实现所使用的所有 SAP NetWeaver 功能。 遵循 SAP 说明 [1245200]，启用 Webdynpro 并生成必要的功能。 遵循上述说明中的指示操作时，还将配置 Internet Communication Manager (icm) 以及用于 http 和 https 连接的端口。 http 的默认设置如下所示：

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

事务 DBACockpit 中生成的链接如下所示：

> https://`<fullyqualifiedhostname`>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://`<fullyqualifiedhostname`>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

需要确保 ICM 使用完全限定的主机名，并且此名称可在打开 DBACockpit 的计算机上解析，具体取决于如何将托管 SAP 系统的 Azure 虚拟机连接到 AD 和 DNS。 请参阅 SAP 说明 [773830]，了解 ICM 如何根据配置文件参数来确定完全限定的主机名，并在必要时显式设置 icm/host_name_full 参数。

如果在仅限云方案中部署了 VM，但未在本地与 Azure 之间建立跨界连接，则需要定义一个公共 IP 地址和一个 domainlabel。 VM 的公共 DNS 名称格式如下所示：

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com
> 
> 

可以在[此处][virtual-machines-azurerm-versus-azuresm]找到有关 DNS 名称的更多详细信息。

将 SAP 配置文件参数 icm/host_name_full 设置为 Azure VM 的 DNS 名称，其链接可能如下所示：

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

在此情况下，需要确保：

* 在 Azure 门户中，针对用来与 ICM 通信的 TCP/IP 端口，向网络安全组添加入站规则
* 针对用来与 ICM 通信的 TCP/IP 端口，向 Windows 防火墙配置添加入站规则

针对自动导入的所有可用修正，建议定期应用适用于 SAP 版本的修正集合 SAP 说明：

* [1558958]
* [1619967]
* [1882376]

可以在以下 SAP 说明中找到有关 SAP ASE 的 DBA Cockpit 的更多详细信息：

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>SAP ASE 的备份/恢复注意事项
将 SAP ASE 部署到 Azure 时，必须检查备份方法。 即使对于非生产系统，也必须定期备份 SAP 数据库。 由于 Azure 存储会保留三个映像，在补救存储崩溃方面，备份可能变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 

在 Azure 中备份和还原数据库的方式与在本地一样。 请参阅 SAP 说明：

* [1588316]
* [1585981]

，以获取有关创建转储配置和计划备份的详细信息。 根据策略和需求，可以在现有的某个磁盘上配置磁盘的数据库和日志转储，也可以添加一个附加磁盘以供备份使用。 为了减少在发生错误时丢失数据的风险，建议使用不带任何数据库目录/文件的磁盘。

除了数据和 LOB 压缩，SAP ASE 还提供备份压缩。 若要让数据库和日志转储耗用较少的空间，建议使用备份压缩。 有关详细信息，请参阅 SAP 说明 [1588316]。 如果打算从 Azure 虚拟机将备份或包含备份转储的 VHD 下载至本地，则压缩备份对于减少要传输的数据量也很重要。

请勿将 Azure VM 临时空间 /mnt 或 /mnt/resource 用作数据库或日志转储目标。

#### <a name="performance-considerations-for-backupsrestores"></a>备份/还原的性能注意事项
与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 请记住，备份压缩会耗用 CPU 资源。 备份压缩的 CPU 消耗可在较少数目的 CPU 线程的 VM 上扮演重要角色。  因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（Linux 软件 RAID、磁盘）越少，吞吐量就越低

若要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 在 SAP ASE 级别创建转储配置，该配置将使用多个目标目录来写入转储

基于已装载的多个磁盘对磁盘卷划分带区，这已在[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中进行了讨论。 有关在 SAP ASE 转储配置中使用多个目录的详细信息，请参阅存储过程 sp_config_dump 的相关文档，此存储过程用于在 [Sybase 信息中心](http://infocenter.sybase.com/help/index.jsp)创建转储配置。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 的灾难恢复
#### <a name="data-replication-with-sap-sybase-replication-server"></a>使用 SAP Sybase 复制服务器复制数据
利用 SAP Sybase 复制服务器 (SRS)，SAP ASE 可提供热备用解决方案，以异步方式将数据库事务传输到远程位置。 

与在本地一样，也可以在 Azure 虚拟机托管的 VM 中正常安装和运行 SRS。

支持通过 SAP 复制服务器的 ASE HADR。 强烈建议使用 SAP ASE 16.03 尝试此类配置。 有关安装此类配置的详细信息，请参阅此[博客](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/)。