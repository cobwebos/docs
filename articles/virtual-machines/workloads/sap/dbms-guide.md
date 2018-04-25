---
title: 适用于 SAP NetWeaver 的 Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: SAP NetWeaver 的 Azure 虚拟机 DBMS 部署
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5654dac7-4204-4387-b312-3d8b2898eb3a
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/26/2018
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 596b88d8bfeaa42caaa234b945973b1404a5f1d3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>SAP NetWeaver 的 Azure 虚拟机 DBMS 部署
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
[resource-groups-networking]:../../../virtual-network/resource-groups-networking.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
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

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

本指南是介绍如何在 Microsoft Azure 上实施和部署 SAP 软件的文档的一部分。 阅读本指南之前，请先阅读[规划和实施指南][planning-guide]。 本文档介绍如何在 Microsoft Azure 虚拟机 (VM) 上使用 Azure 服务架构 (IaaS) 功能，将各种关系数据库管理系统 (RDBMS) 及相关产品与 SAP 组合进行部署。

本文是对 SAP 安装文档和 SAP 说明的补充，这些文档代表在给定平台上安装和部署 SAP 软件的主要资源。

## <a name="general-considerations"></a>一般注意事项
本章介绍在 Azure VM 中运行 SAP 相关 DBMS 系统的注意事项。 其中很少涉及有关特定 DBMS 系统的参考信息。 在本白皮书内，改在本章之后讨论特定的 DBMS 系统。

### <a name="definitions-upfront"></a>定义预释
整个文档使用以下术语：

* IaaS：服务架构。
* PaaS：平台即服务。
* SaaS：软件即服务。
* SAP 组件：单个 SAP 应用程序，例如 ECC、BW、Solution Manager 或 EP。  SAP 组件可以基于传统的 ABAP 或 Java 技术，也可以是不基于 NetWeaver 的应用程序，例如业务对象。
* SAP 环境：以逻辑方式组合在一起，用于执行开发、QAS、培训、DR 或生产等业务功能的一个或多个 SAP 组件。
* SAP 布局：表示客户 IT 布局中所有 SAP 资产的整个格局。 SAP 布局包括所有生产和非生产环境。
* SAP 系统：诸如 SAP ERP 开发系统、SAP BW 测试系统、SAP CRM 生产系统等的 DBMS 层与应用程序层的组合。在 Azure 部署中，不支持在本地和 Azure 之间分割这两个层。 这意味着，某个 SAP 系统要么部署在本地，要么部署在 Azure 中。 但是，可以将 SAP 布局中的不同系统部署到 Azure 或本地。 例如，可以在 Azure 中部署 SAP CRM 开发系统和测试系统，但在本地部署 SAP CRM 生产系统。
* 仅限云的部署：不通过站点到站点或 ExpressRoute 连接将 Azure 订阅连接到本地网络基础结构的一种部署。 在一般的 Azure 文档中，此类部署也称为“仅限云”部署。 使用此方法部署的虚拟机可通过 Internet 和分配给 Azure VM 的公共 Internet 终结点来访问。 在此类部署中，本地 Active Directory (AD) 和 DNS 不会扩展到 Azure。 因此，VM 不是本地 Active Directory 的一部分。 注意：本文档中的仅限云部署定义为在 Azure 中以独占方式运行，而不会将 Active Directory 或名称解析从本地扩展到公有云的完整 SAP 布局。 SAP 生产系统或配置不支持仅限云的配置，在此配置中，托管于 Azure 的 SAP 系统与位于本地的资源之间需要使用 SAP STMS 或其他本地资源。
* 跨界：描述这样一种方案：将 VM 部署到在本地数据中心与 Azure 之间建立了站点到站点、多站点或 ExpressRoute 连接的 Azure 订阅。 在一般的 Azure 文档中，此类部署也称为跨界方案。 建立连接是为了将本地域、本地 Active Directory 和本地 DNS 扩展到 Azure。 本地布局会扩展到订阅的 Azure 资产。 经过这种扩展后，VM 可以成为本地域的一部分。 本地域的域用户可以访问服务器，并可在这些 VM 上运行服务（例如 DBMS 服务）。 可以在部署于本地的 VM 与部署于 Azure 的 VM 之间进行通信和名称解析。 我们预计这是在 Azure 上部署 SAP 资产最常见的方案。 有关详细信息，请参阅[此文][vpn-gateway-cross-premises-options]和[此文][vpn-gateway-site-to-site-create]。

> [!NOTE]
> SAP 生产系统支持对 SAP 系统进行这种跨界部署：运行 SAP 系统的 Azure 虚拟机是本地域的成员。 跨界配置可将部分或完整 SAP 布局部署到 Azure。 即使在 Azure 中运行完整 SAP 布局，也需要这些 VM 成为本地域和 ADS 的一部分。 在本文档的旧版本中，我们曾谈到混合 IT 方案，其中“混合”一词基本上是指本地与 Azure 之间有跨界连接。 在此方案中，“混合”还表示 Azure 中的 VM 是本地 Active Directory 的一部分。
> 
> 

有些 Microsoft 文档在描述跨界方案时稍有不同，特别是针对 DBMS HA 配置。 在 SAP 相关的文档中，跨界方案单纯是指具有站点到站点或专用 (ExpressRoute) 连接，以及将 SAP 布局分布到本地与 Azure 的情况。

### <a name="resources"></a>资源
我们针对有关 Azure 上的 SAP 部署提供以下指南：

* [SAP NetWeaver 的 Azure 虚拟机规划和实施指南][planning-guide]
* [SAP NetWeaver 的 Azure 虚拟机部署][deployment-guide]
* [SAP NetWeaver 的 Azure 虚拟机 DBMS 部署（本文档）][dbms-guide]

以下 SAP 说明与 Azure 上的 SAP 相关：

| 说明文档编号 | 标题 |
| --- | --- |
| [1928533] |Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型 |
| [2015553] |Microsoft Azure 上的 SAP：支持先决条件 |
| [1999351] |适用于 SAP 的增强型 Azure 监视故障排除 |
| [2178632] |Microsoft Azure 上的 SAP 关键监视度量值 |
| [1409604] |Windows 上的虚拟化：增强型监视 |
| [2191498] |使用 Azure 的 Linux 上的 SAP：增强型监视 |
| [2039619] |Microsoft Azure 上使用 Oracle 数据库的 SAP 应用程序：支持的产品和版本 |
| [2233094] |DB6：Azure 上使用 IBM DB2 for Linux、UNIX 和 Windows 的 SAP 应用程序 — 附加信息 |
| [2243692] |Microsoft Azure (IaaS) VM 上的 Linux：SAP 许可证问题 |
| [1984787] |SUSE LINUX Enterprise Server 12：安装说明 |
| [2002167] |Red Hat Enterprise Linux 7.x：安装和升级 |
| [2069760] |Oracle Linux 7.x SAP 安装和升级 |
| [1597355] |适用于 Linux 的交换空间建议 |
| [2171857] |Oracle Database 12c - Linux 上的文件系统支持 |
| [1114181] |Oracle Database 11g - Linux 上的文件系统支持 |


另请阅读 [SCN Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)，其中包含适用于 Linux 的所有 SAP 说明。

应当使用过 Microsoft Azure 体系结构，并知道如何部署和操作 Microsoft Azure 虚拟机。 可在 <https://azure.microsoft.com/documentation/> 中查看详细信息

> [!NOTE]
> 我们**不**讨论 Microsoft Azure Platform 的 Microsoft Azure 平台即服务 (PaaS) 产品。 本文讨论如何在 Microsoft Azure 虚拟机 (IaaS) 中运行数据库管理系统 (DBMS)，就像在本地环境中运行 DBMS 一样。 这两种产品的数据库性能与功能差异极大，不应混用。 另请参阅：<https://azure.microsoft.com/services/sql-database/>
> 
> 

由于我们讨论的是 IaaS，因此，一般而言，Windows、Linux 和 DBMS 的安装和配置基本上与你在本地安装的任何虚拟机或裸机计算机相同。 不过，使用 IaaS 时的一些体系结构和系统管理实施决策有所不同。 本文档旨在说明使用 IaaS 时必须准备好应对的特定体系结构和系统管理差异。

一般而言，本文讨论的总体差异范围如下：

* 为 SAP 系统规划适当的 VM/磁盘布局，以确保有适当的数据文件布局，而且可达到足够的 IOPS 供工作负荷使用。
* 使用 IaaS 时的网络注意事项。
* 用于优化数据库布局的特定数据库功能。
* IaaS 中的备份和还原注意事项。
* 使用不同类型的映像进行部署。
* Azure IaaS 中的高可用性。

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>RDBMS 部署的结构
为了完成本章的学习，必须先了解[部署指南][deployment-guide]的[此][deployment-guide-3]章所提供的内容。 在阅读本章之前，必须先了解并熟悉不同的 VM 系列及其差异，以及 Azure 标准存储和高级存储的差异。

在 2015 年 3 月之前，包含操作系统的磁盘的大小限制为 127 GB。 此限制已在 2015 年 3 月提高（有关详细信息，请查看 <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>）。 从那时起，包含操作系统的磁盘的大小可以与任何其他磁盘一样。 不过，我们仍然推荐这种结构的部署：操作系统、DBMS 和最终的 SAP 二进制文件与数据库文件分开。 因此，我们预计 Azure 虚拟机中运行的 SAP 系统将安装包含操作系统、数据库管理系统可执行文件和 SAP 可执行文件的基础 VM（或磁盘）。 DBMS 数据和日志文件将存储在 Azure 存储（标准或高级存储）的独立磁盘中，并以逻辑磁盘形式附加到原始的 Azure 操作系统映像 VM。 

视具体使用 Azure 标准存储还是高级存储（例如，使用 DS 系列还是 GS 系列的 VM）而定，Azure 中会有其他配额（[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 有介绍）。 规划磁盘布局时，必须找到下列各项的配额最佳均衡：

* 数据文件数目。
* 包含文件的磁盘数目。
* 单个磁盘的 IOPS 配额。
* 每个磁盘的数据吞吐量。
* 每个 VM 大小可能的附加数据磁盘数目。
* VM 可提供的总体存储吞吐量。

Azure 强制实施每个数据磁盘的 IOPS 配额。 对于 Azure 标准存储上托管的磁盘与高级存储上托管的磁盘，这些配额是不同的。 这两种存储类型的 I/O 延迟也很不一样：高级存储提供更好的 I/O 延迟。 每种不同的 VM 类型可附加的数据磁盘数目有限。 另一个限制是，只有特定的 VM 类型可以使用 Azure 高级存储。 这意味着，针对特定 VM 类型的决策可能不只受到 CPU 和内存需求影响，还受到 IOPS、延迟和磁盘吞吐量需求影响，这些需求通常通过磁盘数目或高级存储磁盘的类型来调整。 特别是在高级存储中，磁盘的大小可能还取决于每个磁盘需要达到的 IOPS 数目和吞吐量。

实际上，总体 IOPS 速率、装载的磁盘数目以及 VM 大小全都绑在一起，这可能导致 SAP 系统的 Azure 配置与其本地部署不同。 每个 LUN 的 IOPS 限制通常可在本地部署中配置。 然而，使用 Azure 存储时，这些限制可能是固定的，也可能与高级存储相同，具体视磁盘类型而定。 因此，在本地部署中，我们看到数据库服务器的客户配置会针对特殊的可执行文件（例如 SAP 和 DBMS）使用许多不同的卷，或者针对临时数据库或表空间使用特殊的卷。 将这类本地系统移至 Azure 时，可能会为未执行任何 IOPS 或 IOPS 较少的可执行文件或数据库浪费一个磁盘，从而可能浪费潜在的 IOPS 带宽。 因此，在 Azure VM 中，建议尽可能将 DBMS 和 SAP 可执行文件安装在 OS 磁盘上。

数据库文件和日志文件的放置以及所使用的 Azure 存储类型应该根据 IOPS、延迟和吞吐量需求来定义。 为了有足够的 IOPS 可供事务日志使用，可能不得不针对事务日志文件使用多个磁盘或使用更大的高级存储磁盘。 在这种情况下，只需使用包含事务日志的磁盘构建一个软件 RAID（例如，适用于 Windows 的 Windows 存储池或适用于 Linux 的 MDADM 和 LVM（逻辑卷管理器））。

- - -
> ![Windows][Logo_Windows] Windows
> 
> Azure VM 中的驱动器 D:\ 是一个非持久性驱动器，由 Azure 计算节点上的部分本地磁盘提供支持。 非持久性意味着，当 VM 重新启动时，将丢失对 D:\ 驱动器上的内容所做的任何更改。 “任何更改”是指已保存的文件、已创建的目录、已安装的应用程序等等。
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux Azure VM 会在 /mnt/resource 上自动装载一个非持久性驱动器，该驱动器由 Azure 计算节点上的本地磁盘提供支持。 非持久性意味着，当 VM 重新启动时，将丢失对 /mnt/resource 中的内容所做的任何更改。 “任何更改”是指已保存的文件、已创建的目录、已安装的应用程序等等。
> 
> 

- - -
视 Azure VM 系列而定，计算节点上的本地磁盘会表现出不同的性能，具体可按以下方式分类：

* A0-A7：非常有限的性能。 不能用于 Windows 页面文件以外的项目
* A8-A11：性能特征极佳，具有高达数万的 IOPS 和 1 GB/秒以上的吞吐量
* D 系列：性能特征极佳，具有高达数万的 IOPS 和 1 GB/秒以上的吞吐量
* DS 系列：性能特征极佳，具有高达数万的 IOPS 和 1 GB/秒以上的吞吐量
* G 系列：性能特征极佳，具有高达数万的 IOPS 和 1 GB/秒以上的吞吐量
* GS 系列：性能特征极佳，具有高达数万的 IOPS 和 1 GB/秒以上的吞吐量

以上表述适用于已通过 SAP 认证的 VM 类型。 具有绝佳 IOPS 和吞吐量的 VM 系列可供某些 DBMS 功能使用，例如，tempdb 或临时表空间。

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>VM 和数据磁盘的缓存
在通过门户创建数据磁盘，或者将上传的磁盘装载到 VM 时，可以选择是否缓存 VM 与位于 Azure 存储中的磁盘之间的 I/O 流量。 Azure 标准存储和高级存储针对这种缓存类型采用两种不同的技术。 在这两种方案中，缓存本身位于 VM 的临时磁盘（Windows 上的 D:\ 或 Linux 上的 /mnt/resource）所使用的相同驱动器上，并由磁盘提供支持。

对于 Azure 标准存储，可能的缓存类型如下：

* 不缓存
* 读取缓存
* 读取和写入缓存

为了获得一致且稳定的性能，应在 Azure 标准存储上，针对包含 **DBMS 相关数据文件、日志文件和表空间**的所有磁盘，将缓存设置为“无”。 VM 的缓存可以保留默认值。

对于 Azure 高级存储，提供下列缓存选项：

* 不缓存
* 读取缓存

在 Azure 高级存储中，建议对 SAP 数据库的**数据文件使用“读取缓存”**，**对磁盘的日志文件选择“不缓存”**。

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>软件 RAID
如上所述，需要在可配置数目的磁盘中的数据库文件所需的 IOPS 数目，与 Azure VM 将针对每个磁盘或高级存储磁盘类型提供的最大 IOPS 数目之间取得平衡。 处理磁盘上 IOPS 负载的最简单方式是基于不同的磁盘构建一个软件 RAID。 然后在从软件 RAID 划分出的 LUN 上放置多个 SAP DBMS 数据文件。 因为三个不同高级存储磁盘的其中两个提供比基于标准存储的磁盘更高的 IOPS 配额，所以你可能也要根据需求，考虑使用高级存储。 此外，Azure 高级存储还提供明显更好的 I/O 延迟。 

上述情况也适用于各种 DBMS 系统的事务日志。 在具有大量事务日志的情况下，仅仅添加更多 Tlog 文件毫无用处，因为 DBMS 系统一次只会写入其中一个文件。 如果需要的 IOPS 速率比基于标准存储的单个磁盘可提供的速率更高，可以对多个标准存储磁盘划分带区，也可以使用提供更高 IOPS 速率的更大型高级存储磁盘类型，这种磁盘类型同时对事务日志的写入 I/O 提供更低的延迟。

在 Azure 部署中遇到以下情况时，应优先使用软件 RAID：

* 事务日志/重做日志需要的 IOPS 数目超过 Azure 为单个磁盘提供的 IOPS 数目。 如上所述，若要解决此问题，可以使用软件 RAID 基于多个磁盘来构建一个 LUN。
* SAP 数据库的不同数据文件中的 I/O 工作负荷分布不均。 在这种情况下，会发现，某个数据文件经常达到配额， 而其他数据文件甚至还未接近单个磁盘的 IOPS 配额。 在这种情况下，最简单的解决方案是使用软件 RAID 基于多个磁盘来构建一个 LUN。 
* 不知道每个数据文件确切的 I/O 工作负荷，只粗略知道 DBMS 的总体 IOPS 工作负荷。 最简单的解决方案是使用软件 RAID 构建一个 LUN。 如此一来，此 LUN 后面的多个磁盘的配额总和应该可以满足已知的 IOPS 速率。

- - -
> ![Windows][Logo_Windows] Windows
> 
> 如果在 Windows Server 2012 或更高版本上运行，建议使用 Windows 存储空间。 它比早期 Windows 版的 Windows Striping 更有效。 使用 Windows Server 2012 作为操作系统时，可能需要通过 PowerShell 命令来创建 Windows 存储池和存储空间。 可在 <https://technet.microsoft.com/library/jj851254.aspx> 中找到 PowerShell 命令
> 
> ![Linux][Logo_Linux] Linux
> 
> 仅支持使用 MDADM 和 LVM（逻辑卷管理器）在 Linux 上构建软件 RAID。 有关详细信息，请阅读以下文章：
> 
> * [在 Linux 上配置软件 RAID][virtual-machines-linux-configure-raid]（适用于 MDADM）
> * [在 Azure 中的 Linux VM 上配置 LVM][virtual-machines-linux-configure-lvm]
> 
> 

- - -
使用能够与 Azure 高级存储搭配使用的 VM 系列时，通常需要注意以下事项：

* 接近 SAN/NAS 设备能力的 I/O 延迟需求。
* 超出 Azure 标准存储能力的 I/O 延迟需求。
* 针对特定 VM 类型，所需的 IOPS/VM 超出使用多个标准存储磁盘可达到的 IOPS。

由于基础 Azure 存储会将每个磁盘复制到至少三个存储节点，因此可以使用简单的 RAID 0 带区。 不需要实施 RAID5 或 RAID1。

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure 存储
Microsoft Azure 存储将基础 VM（含 OS）以及磁盘或 BLOB 存储到至少三个不同的存储节点。 创建存储帐户或托管磁盘时，有一个保护选项，如下所示：

![为 Azure 存储帐户启用异地复制][dbms-guide-figure-100]

Azure 存储本地复制（本地冗余）可提供多层保护，避免在部署过程中出现因基础结构故障而导致数据丢失的情况，而这是大多数客户都难以承受的。 如上所示，一共有四个不同的选项，第五个是前三个选项之一的变体。 请仔细查看它们的区分方式：

* **高级本地冗余存储 (LRS)**：Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机提供高性能、低延迟的磁盘支持。 在 Azure 区域的同一个 Azure 数据中心内有三个数据副本。 这些副本位于不同的容错域和升级域中（相关概念请参阅[规划指南][planning-guide]中的[此][planning-guide-3.2]章）。 如果数据副本因为存储节点故障或磁盘故障而停止服务，系统会自动生成新的副本。
* 本地冗余存储 (LRS)：在此方案中，Azure 区域的同一个 Azure 数据中心内有三个数据副本。 这些副本位于不同的容错域和升级域中（相关概念请参阅[规划指南][planning-guide]中的[此][planning-guide-3.2]章）。 如果数据副本因为存储节点故障或磁盘故障而停止服务，系统会自动生成新的副本。 
* 异地冗余存储 (GRS)：此方案中有一项异步复制操作，它会在另一个 Azure 区域提供额外的三个数据副本，而在大多数情况下，此区域会在同一个地理区域内（例如，北欧和西欧）。 这样就有三个额外的副本，因此总共有六个副本。 此方案的变体添加了一项功能，即，能够读取异地复制的 Azure 区域中的数据（读取访问异地冗余）。
* 区域冗余存储 (ZRS)：在此方案中，三个数据副本保留在同一个 Azure 区域中。 如[规划指南][planning-guide]中的[此][planning-guide-3.1]章所述，一个 Azure 区域可以是数个相邻的数据中心。 在 LRS 方案中，副本分布在组成一个 Azure 区域的不同数据中心。

可在[此处][storage-redundancy]找到更多信息。

> [!NOTE]
> 对于 DBMS 部署，不建议使用异地冗余存储
> 
> Azure 存储异地复制是异步的。 对装载到单个 VM 的各个磁盘的复制不会完全一致地进行同步。 因此，不适合复制分布在不同磁盘上的 DBMS 文件，或者使用软件 RAID 基于多个磁盘来部署的 DBMS 文件。 DBMS 软件要求持久性磁盘存储在不同的 LUN 和基础磁盘/主轴上精确同步。 DBMS 软件会使用各种机制对 IO 写入活动排序，即使有几毫秒的变化，DBMS 都将报告复制的目标磁盘存储已损坏。 因此，如果想要让数据库配置中的某个数据库延伸到多个异地复制的磁盘，则需要使用数据库方法和功能来执行这类复制。 不应该依赖 Azure 存储异地复制来执行此作业。 
> 
> 下面以一个示例系统最简单地说明此问题。 假设将 SAP 系统上传到 Azure，Azure 中有八个包含 DBMS 数据文件的磁盘，以及一个包含事务日志文件的磁盘。 无论是要将数据写入数据文件还是事务日志文件，这九个磁盘中的每一个都会根据 DBMS，以一致的方式将数据写入其中。
> 
> 为了对数据进行正确的异地复制并维护一致的数据库映像，这九个磁盘的内容必须完全根据对这九个不同磁盘执行 I/O 操作的顺序进行异地复制。 但是，Azure 存储异地复制不允许声明磁盘之间的依赖关系。 这意味着，Microsoft Azure 存储异地复制不知道这九个不同磁盘中的内容是彼此相关的，而且只有以这九个磁盘上发生 I/O 操作的顺序进行复制时，数据更改才会一致。
> 
> 该方案中的异地复制映像很有可能提供不一致的数据库映像，除此之外，异地冗余存储还会严重地影响性能。 总之，请勿对 DBMS 类型的工作负荷使用这种类型的存储冗余。
> 
> 

#### <a name="mapping-vhds-into-azure-virtual-machine-service-storage-accounts"></a>将 VHD 映射到 Azure 虚拟机服务存储帐户
本章仅适用于 Azure 存储帐户。 如果计划使用托管磁盘，则本章所述限制不适用。 有关托管磁盘的详细信息，请阅读本指南中的[托管磁盘][dbms-guide-managed-disks]一章。

Azure 存储帐户不只是一种管理构造，还是一个具有各种限制的主体。 然而，根据我们讨论的是 Azure 标准存储帐户还是 Azure 高级存储帐户，限制会有所不同。 确切的功能和限制列于[此处][storage-scalability-targets]

因此，对于 Azure 标准存储，要特别注意每个存储帐户都有 IOPS 限制（[本文][storage-scalability-targets]中包含“总请求速率”的行）。 此外，每个 Azure 订阅有 100 个存储帐户的初始限制（截至 2015 年 7 月）。 因此，建议在使用 Azure 标准存储时，平衡多个存储帐户之间 VM 的 IOPS。 不过，单个 VM 最好尽可能使用一个存储帐户。 因此，在托管于 Azure 标准存储的每个 VHD 都会达到其配额限制的 DBMS 部署中，每个使用 Azure 标准存储的 Azure 存储帐户只能部署 30-40 个 VHD。 相反，如果使用 Azure 高级存储，并且想要存储大型数据库卷，在 IOPS 方面可能不会遇到问题。 但是，Azure 高级存储帐户在数据卷方面比 Azure 标准存储帐户更严格。 因此，在达到数据卷限制之前，只能在 Azure 高级存储帐户内部署数目有限的 VHD。 最后，将 Azure 存储帐户想象成在 IOPS 和/或容量方面能力有限的“虚拟 SAN”。 因此，还需执行一项任务（如同在本地部署中一样），即，在不同的“虚构 SAN 设备”或 Azure 存储帐户上定义不同 SAP 系统的 VHD 布局。

对于 Azure 标准存储，建议尽可能不要向单个 VM 提供来自不同存储帐户的存储。

在使用 DS 或 GS 系列的 Azure VM 时，可以在 Azure 标准存储帐户和高级存储帐户以外的地方装载 VHD。 当脑海中浮现出将备份写入由标准存储支持的 VHD，而将 DBMS 数据和日志文件放置在高级存储上这样的用例时，就可以使用这种异类存储。 

根据客户部署和测试，可在单个 Azure 标准存储帐户上预配大约 30 到 40 个包含数据库数据文件和日志文件的 VHD，并且性能仍在可接受的范围内。 如先前所述，Azure 高级存储帐户的限制很可能是它可持有的数据容量，而不是 IOPS。

与本地 SAN 设备一样，共享需要一些监视，以便最终能够检测到 Azure 存储帐户上的瓶颈。 适用于 SAP 的 Azure 监视扩展和 Azure 门户等工具可用于检测可能提供次优 IO 性能的忙碌 Azure 存储帐户。  如果检测到这种情况，建议将忙碌的 VM 移到另一个 Azure 存储帐户。 有关如何激活 SAP 主机监视功能的详细信息，请参阅[部署指南][deployment-guide]。

可在 <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx> 中找到另一篇概述 Azure 标准存储和 Azure 标准存储帐户最佳做法的文章

#### <a name="f42c6cb5-d563-484d-9667-b07ae51bce29"></a>托管磁盘
托管磁盘是 Azure 资源管理器中的一种新资源类型，可用于替代 Azure 存储帐户中存储的 VHD。 托管磁盘与其所附加到的虚拟机可用性集自动对齐，因而增加了虚拟机和虚拟机上所运行服务的可用性。 若要了解详细信息，请阅读[概述文章](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview)。

SAP 当前仅支持高级托管磁盘。 有关更多详细信息，请阅读 SAP 说明 [1928533]。

#### <a name="moving-deployed-dbms-vms-from-azure-standard-storage-to-azure-premium-storage"></a>将部署的 DBMS VM 从 Azure 标准存储移到 Azure 高级存储
我们遇到过很多这样的案例：客户想要将部署的 VM 从 Azure 标准存储移到 Azure 高级存储。 如果磁盘存储在 Azure 存储帐户中，在不实际移动数据的情况下，根本无法完成此操作。 但可以使用多种方法实现此目的：

* 将所有 VHD、基础 VHD 以及数据 VHD 复制到新的 Azure 高级存储帐户中。 通常情况下，在 Azure 标准存储中选择大量 VHD 不是因为需要数据卷， 而是因为 IOPS。 已经移至 Azure 高级存储，现在可以使用较少的 VHD 来达到一定的 IOPS 吞吐量。 事实上，在 Azure 标准存储中，是为已用数据而不是名义上的磁盘大小付费，因此，VHD 的数目与成本无关。 然而，在 Azure 高级存储中，就要为名义上的磁盘大小付费。 因此，大多数客户会尝试将高级存储中 Azure VHD 的数目控制在达到必要 IOPS 吞吐量所需的数目内。 所以，大多数客户不会选择简单的 1:1 复制方式。
* 装载可包含 SAP 数据库的数据库备份的单个 VHD（如果尚未装载）。 备份之后，卸载所有 VHD（包括含有备份的 VHD），并将基础 VHD 和含有备份的 VHD 复制到 Azure 高级存储帐户。 然后根据基础 VHD 部署 VM，并装载含有备份的 VHD。 现在可以为 VM 创建额外的空白高级存储磁盘，用作数据库的目标还原位置。 这会假设 DBMS 允许在还原过程中更改数据和日志文件的路径。
* 另一种可行方法是先前过程的变体，即，将备份 VHD 复制到 Azure 高级存储，然后将其附加到新部署和安装的 VM。
* 当需要更改数据库的数据文件数目时，会选择第四种可行方法。 在这种情况下，会使用导出/导入来执行 SAP 同源系统复制。 将这些导出文件放入 VHD（已复制到 Azure 高级存储帐户），然后将它附加到用于运行导入过程的 VM。 客户通常在想要减少数据文件数目时，才会使用这种可行方法。

如果使用托管磁盘，则可通过以下方式迁移到高级存储：

1. 解除分配虚拟机
2. 必要时重设虚拟机的大小，使之支持高级存储（例如重设为 DS 或 GS）
3. 将托管磁盘帐户类型更改为“高级”(SSD)
4. 启动虚拟机

### <a name="deployment-of-vms-for-sap-in-azure"></a>在 Azure 中为 SAP 部署 VM
Microsoft Azure 提供多种用于部署 VM 和相关磁盘的方法。 因此，请务必了解这些方法之间的差异，因为 VM 的准备工作可能会因部署方法的不同而异。 一般情况下，我们会考虑以下章节中所述的方案。

#### <a name="deploying-a-vm-from-the-azure-marketplace"></a>从 Azure Marketplace 部署 VM
想要从 Azure Marketplace 中获取某个 Microsoft 或第三方映像来部署 VM。 将 VM 部署到 Azure 之后，需要像在本地环境中一样，遵照相同的准则并使用相同的工具在 VM 内部安装 SAP 软件。 若要在 Azure VM 内部安装 SAP 软件，SAP 和 Microsoft 建议将 SAP 安装媒体上传并存储到磁盘中，或创建一个充当“文件服务器”并包含所有必要 SAP 安装媒体的 Azure VM。

#### <a name="deploying-a-vm-with-a-customer-specific-generalized-image"></a>使用特定于客户的通用化映像部署 VM
由于 OS 或 DBMS 版本存在特定的修补程序要求，Azure Marketplace 中提供的映像可能并不符合需要。 因此，可能需要使用自己的、以后可以多次部署的“专用”OS/DBMS VM 映像创建一个 VM。 若要准备这样一个可供复制的“专用”映像，必须在本地 VM 上将 OS 通用化。 有关如何将 VM 通用化的详细信息，请参阅[部署指南][deployment-guide]。

如果已将 SAP 内容安装在本地 VM 中（尤其是对于双层系统），则可以在部署 Azure VM 之后，通过 SAP Software Provisioning Manager 支持的实例重命名过程来修改 SAP 系统设置（SAP 说明 [1619720]）。 否则，可以稍后在部署 Azure VM 之后安装 SAP 软件。

对于 SAP 应用程序所使用的数据库内容，可以通过 SAP 安装生成全新的内容，也可以通过将 VHD 与 DBMS 数据库备份搭配使用，或利用 DBMS 的功能直接备份到 Microsoft Azure 存储，将内容导入 Azure。 在此情况下，也可以在本地准备好包含 DBMS 数据和日志文件的 VHD，然后将它们作为磁盘导入 Azure。 但是，将从本地加载的 DBMS 数据传输到 Azure 的操作会在需要在本地准备的 VHD 磁盘上执行。

#### <a name="moving-a-vm-from-on-premises-to-azure-with-a-non-generalized-disk"></a>使用非通用化磁盘将 VM 从本地移至 Azure
打算将某个特定 SAP 系统从本地移至 Azure（提升与移动）。 通过将包含 OS、SAP 二进制文件和最终 DBMS 二进制文件的磁盘以及包含 DBMS 数据和日志文件的磁盘上传到 Azure，可以实现此目的。 与上面的方案 2 相反，需要将 Azure VM 中的主机名、SAP SID 和 SAP 用户帐户保留为与本地环境中的配置相同。 因此，不需要通用化映像。 此情况主要适用于跨界方案，在这类方案中，SAP 布局的一部分在本地运行，其余部分则在 Azure 上运行。

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Azure VM 的高可用性和灾难恢复
Azure 提供以下高可用性 (HA) 和灾难恢复 (DR) 功能，这些功能适用于进行 SAP 和 DBMS 部署时所用的各种组件

### <a name="vms-deployed-on-azure-nodes"></a>部署于 Azure 节点上的 VM
Azure 平台不会为部署的 VM 提供实时迁移等功能。 这意味着，如果部署 VM 的服务器群集需要进行维护，该 VM 就必须停止并重新启动。 Azure 中的维护操作通过使用服务器群集内所谓的升级域来执行。 一次只维护一个升级域。 在这类重启操作期间，当 VM 关闭、执行维护以及 VM 重启时，服务将中断。 不过，大部分 DBMS 供应商都提供高可用性和灾难恢复功能，此功能会在主节点不可用时，在另一个节点上快速重启 DBMS 服务。 Azure 平台提供跨升级域分布 VM、存储及其他 Azure 服务的功能，以确保计划的维护或基础结构故障只影响一小部分的 VM 或服务。  通过仔细规划，可以达到相当于本地基础结构的可用性级别。

Microsoft Azure 可用性集是 VM 或服务的逻辑分组，可确保 VM 和其他服务分布到群集内不同的容错域和升级域，这样一来，在任何时间点都只会有一个节点关闭（有关更多详细信息，请阅读[此文 (Linux)][virtual-machines-manage-availability-linux] 或[此文 (Windows)][virtual-machines-manage-availability-windows]）。

推出 VM 时，需根据用途来配置可用性集，如下所示：

![DBMS HA 配置的可用性集定义][dbms-guide-figure-200]

如果想要创建 DBMS 部署的高可用配置（不依赖于所使用的单项 DBMS HA 功能），DBMS VM 需要：

* 将新 VM 添加到同一 Azure 虚拟网络 (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* HA 配置的 VM 也应该位于相同的子网中。 在仅限云的部署中，无法在不同子网之间进行名称解析，只能进行 IP 解析。 在跨界部署中使用站点到站点或 ExpressRoute 连接，就已经建立了包含至少一个子网的网络。 名称解析会根据本地 AD 策略和网络基础结构来完成。 



#### <a name="ip-addresses"></a>IP 地址
强烈建议以弹性方式来设置 HA 配置的 VM。 除非使用静态 IP 地址，否则，在 Azure 中依赖 IP 地址来处理 HA 配置内的 HA 伙伴并不可靠。 Azure 中有两种“关闭”概念：

* 通过 Azure 门户或 Azure PowerShell cmdlet Stop-AzureRmVM 来关闭：在这种情况下，会关闭虚拟机并解除分配。 将不再针对此 VM 向 Azure 帐户收费，因此，只有在使用存储时才会收费。 不过，如果网络接口的专用 IP 地址不是静态的，则会释放该 IP 地址，并且不保证在重新启动 VM 之后，会将旧的 IP 地址重新分配给网络接口。 如果通过 Azure 门户或通过调用 Stop-AzureRmVM 来执行关闭，会自动解除分配。 如果不希望解除计算机分配，请使用 Stop-AzureRmVM -StayProvisioned 
* 如果从 OS 层关闭 VM，VM 将关闭并且不解除分配。 不过，在此情况下，仍会针对 VM 向 Azure 帐户收费，即使关闭也一样。 在这种情况下，仍会将 IP 地址分配给已停止的 VM。 从中关闭 VM 并不会自动强制解除分配。

实际上，在跨界方案中，关闭和解除分配都默认表示从 VM 中解除 IP 地址的分配，即使 DHCP 设置中的本地策略不同也一样。 

* 有一种例外情况就是，将静态 IP 地址分配给网络接口，如[此处][virtual-networks-reserved-private-ip]所述。
* 在这种情况下，只要网络接口未被删除，IP 地址就保持固定。

> [!IMPORTANT]
> 为了让整个部署简单且易于管理，明确建议通过在所涉及的不同 VM 之间提供正常运行的名称解析，在 Azure 的 DBMS HA 或 DR 配置中设置 VM 合作。
> 
> 

## <a name="deployment-of-host-monitoring"></a>部署主机监视
若要有效使用 Azure 虚拟机中的 SAP 应用程序，SAP 需要能够从运行 Azure 虚拟机的物理主机获取主机监视数据。 需要有特定的 SAP 主机代理补丁级别，才能在 SAPOSCOL 和 SAP 主机代理中启用此功能。 SAP 说明 [1409604] 中介绍了确切的补丁级别。

有关向 SAPOSCOL 和 SAP 主机代理提供主机数据的组件部署以及这些组件的生命周期管理的详细信息，请参阅[部署指南][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>有关 Microsoft SQL Server 的具体信息
### <a name="sql-server-iaas"></a>SQL Server IaaS
自从有了 Microsoft Azure，可以轻松地将构建于 Windows Server 平台的现有 SQL Server 应用程序迁移到 Azure 虚拟机。 借助虚拟机中的 SQL Server，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SQL Server，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。 

> [!IMPORTANT]
> 我们不讨论 Microsoft Azure Platform 的“平台即服务”产品 - Microsoft Azure SQL 数据库。 本文讨论的是如何运行 SQL Server 产品（已知适用于 Azure 虚拟机中的本地部署），以及如何运用 Azure 的“服务架构”功能。 这两种产品的数据库性能与功能差异很大，不应混用。 另请参阅：<https://azure.microsoft.com/services/sql-database/>
> 
> 

在继续操作之前，我们建议查看[此文档][virtual-machines-sql-server-infrastructure-services]。

在下列章节中，将汇总并提及上述链接下的文档的某些部分。 另外，还将提及有关 SAP 的具体信息，并且更深入地说明一些概念。 不过，强烈建议先完整阅读上述文档，再阅读 SQL Server 特定文档。

在继续操作之前，应该先了解 IaaS 中 SQL Server 的一些特定信息：

* **虚拟机 SLA**：可以在此处找到适用于 Azure 中运行的虚拟机的 SLA：<https://azure.microsoft.com/support/legal/sla/>  
* **SQL 版本支持**：对于 SAP 客户，我们在 Microsoft Azure 虚拟机上支持 SQL Server 2008 R2 和更高版本。 不支持更早版本。 有关更多详细信息，请查看此通用[支持声明](https://support.microsoft.com/kb/956893)。 注意，Microsoft 通常也支持 SQL Server 2008。 不过，由于适用于 SAP 的重要功能是通过 SQL Server 2008 R2 引进的，因此，SQL Server 2008 R2 是适用于 SAP 的最低版本。 请记住，SQL Server 2012 和 2014 已扩展，可与 IaaS 方案更深入地集成（例如，直接对 Azure 存储进行备份）。 因此，我们将本文的范围限制为 SQL Server 2012 和 2014 及其适用于 Azure 的最新补丁级别。
* **SQL 功能支持**：Microsoft Azure 虚拟机支持大部分 SQL Server 功能，但有一些例外。 **不支持使用共享磁盘的 SQL Server 故障转移群集**。  单个 Azure 区域内支持数据库镜像、AlwaysOn 可用性组、复制、日志传送和 Service Broker 等分布式技术。 不同的 Azure 区域之间也支持 SQL Server AlwaysOn，如此处所述：<https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>。  有关更多详细信息，请查看[支持声明](https://support.microsoft.com/kb/956893)。 [此文][virtual-machines-workload-template-sql-alwayson]提供了有关如何部署 AlwaysOn 配置的示例。 此外，请查看[此处][virtual-machines-sql-server-infrastructure-services]所述的最佳做法 
* SQL 性能：相比其他公有云虚拟化产品，我们确信 Microsoft Azure 托管的虚拟机将运行得非常顺利，但个别结果可能不同。 请查看[此文][virtual-machines-sql-server-performance-best-practices]。
* **使用来自 Azure Marketplace 的映像**：部署新 Azure VM 的最快方式是使用来自 Microsoft Azure Marketplace 的映像。 Azure Marketplace 提供包含 SQL Server 的映像。 已经安装 SQL Server 的映像不能立即用于 SAP NetWeaver 应用程序。 原因是这些映像安装了默认的 SQL Server 排序规则，而不是 SAP NetWeaver 系统所需的排序规则。 若要使用此类映像，请查看[使用来自 Microsoft Azure Marketplace 的 SQL Server 映像][dbms-guide-5.6]一章中所述的步骤。 
* 有关详细信息，请查看[定价详细信息](https://azure.microsoft.com/pricing/)。 [SQL Server 2012 Licensing Guide](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf)（SQL Server 2012 许可指南）和 [SQL Server 2014 Licensing Guide](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf)（SQL Server 2014 许可指南）也是相当重要的资源。

### <a name="sql-server-configuration-guidelines-for-sap-related-sql-server-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 相关 SQL Server 的 SQL Server 配置准则
#### <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>适用于 SAP 相关 SQL Server 部署的 VM/VHD 结构建议
按照一般的说明，SQL Server 可执行文件应该位于或安装在 VM 的 OS 磁盘的系统驱动器（驱动器 C:\)）中。  通常情况下，SAP NetWeaver 工作负荷对大部分 SQL Server 系统数据库的使用率都不会很高。 因此，SQL Server 的系统数据库（master、msdb 和 model）也可以保留在驱动器 C:\ 上。 唯一的例外就是 tempdb，在某些 SAP ERP 和所有 BW 工作负荷中，它可能需要较高的数据量或 I/O 操作量，以至于不适合放在原始 VM 中。 对于这类系统，应执行以下步骤：

* 将主要 tempdb 数据文件移到与 SAP 数据库的主要数据文件相同的逻辑驱动器中。
* 将任何其他 tempdb 数据文件添加到其他包含 SAP 用户数据库数据文件的逻辑驱动器中。
* 将 tempdb 日志文件添加到包含用户数据库日志文件的逻辑驱动器中。
* 计算节点上**专供使用本地 SSD 的 VM 类型使用**的 tempdb 数据文件和日志文件可以放在 D:\ 驱动器上。 不过，可能会建议使用多个 tempdb 数据文件。 请注意，D:\ 驱动器卷因 VM 类型而异。

这些配置让 tempdb 耗用的空间比系统驱动器能够提供的还多。 若要确定正确的 tempdb 大小，可以在运行于本地的现有系统上检查 tempdb 大小。 此外，这类配置会针对 tempdb 启用系统驱动器无法提供的 IOPS 数目。 再次提醒，可以使用本地运行的系统来监视 tempdb 的 I/O 工作负荷，以此推断 tempdb 上预期显示的 IOPS 数目。

运行包含 SAP 数据库的 SQL Server 且 tempdb 数据和 tempdb 日志文件放置于 D:\ 驱动器的 VM 配置应如下所示：

![适用于 SAP 的 Azure IaaS VM 的参考配置][dbms-guide-figure-300]

请注意，D:\ 驱动器的大小因 VM 类型而异。 视 tempdb 的大小需求而定，一旦 D:\ 驱动器太小，可能就不得不将 tempdb 数据和日志文件与 SAP 数据库数据和日志文件进行配对。

#### <a name="formatting-the-disks"></a>格式化磁盘
对于 SQL Server，包含 SQL Server 数据和日志文件的磁盘的 NTFS 块大小应该为 64K。 不需要将 D:\ 驱动器格式化。 此驱动器已预先格式化。

要确保还原或创建数据库时不会通过清空文件内容来初始化数据文件，应该确保 SQL Server 服务运行所在的用户上下文具有特定的权限。 通常，Windows 管理员组中的用户拥有这些权限。 如果 SQL Server 服务在非 Windows 管理员用户的用户上下文中运行，需要为该用户分配“执行卷维护任务”用户权限。  请参阅这篇 Microsoft 知识库文章中的详细信息：<https://support.microsoft.com/kb/2574695>

#### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，SAP 和 Microsoft 建议先应用 SQL Server 页面压缩，然后再将现有 SAP 数据库上传到 Azure（如果尚未这么做）。

之所以建议先执行数据库压缩，再上载到 Azure，原因有两点：

* 减少要上传的数据量。
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短。
* 较小的数据库大小可以降低磁盘分配的成本

数据库压缩也可以在 Azure 虚拟机中运行，如同在本地运行一样。 有关如何压缩现有 SAP SQL Server 数据库的更多详细信息，请查看以下网页：<https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>

### <a name="sql-server-2014---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 - 将数据库文件直接存储在 Azure Blob 存储上
SQL Server 2014 开启了一种可能性：将数据库文件直接存储在 Azure Blob 存储上，而不需要用 VHD 来“包装”。 特别是在使用标准 Azure 存储或较小的 VM 类型时，这样可以克服 IOPS 的限制，这些限制是通过可以装载到某些较小 VM 类型的有限磁盘数目来强制实施的。 这适用于 SQL Server 的用户数据库，但不适用于系统数据库。 这也适用于 SQL Server 的数据和日志文件。 如果想使用此方式部署 SAP SQL Server 数据库，而不是将它“包装”到 VHD，请记住以下几点：

* 所用存储帐户所在的 Azure 区域必须与部署运行 SQL Server 的 VM 时所用的存储帐户相同。
* 之前列出的有关将 VHD 分布到不同 Azure 存储帐户的注意事项也适用于这种部署方法。 意味着 I/O 操作计数会以 Azure 存储帐户的限制为依据。

[comment]: <> (MSSedusch TODO：但这会使用网络带宽，而不是存储带宽，不是吗？)

此处列出了有关此类部署的详细信息：<https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure>

要将 SQL Server 数据文件直接存储在 Azure 高级存储上，必须拥有 SQL Server 2014 补丁版本（最低要求），此处进行了说明：<https://support.microsoft.com/kb/3063054>。 要将 SQL Server 数据文件存储在 Azure 标准存储上，需使用 SQL Server 2014 的发行版本。 不过，相同的补丁包含另一个系列的修补程序，利用这些修补程序，可以更加可靠地直接使用 Azure Blob 存储来存储 SQL Server 数据文件和备份。 因此，我们通常建议使用这些补丁。

### <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 缓冲池扩展
SQL Server 2014 引入了一项称为缓冲池扩展的新功能。 此功能使用由服务器或 VM 的本地 SSD 支持的第二层缓存，来扩展保留于内存中的 SQL Server 缓冲池。 扩展后，可在“内存中”保留更大型的数据工作集。 相较于访问 Azure 标准存储，访问存储在 Azure VM 的本地 SSD 上的缓冲池扩展的速度要快得多。  因此，使用具有出色 IOPS 和吞吐量的 VM 类型的本地 D:\ 驱动器可能是一个非常合理的方法，它可以减少 Azure 存储的 IOPS 负载，并大幅提升查询的响应时间。 这特别适用于未使用高级存储的场合。 使用高级存储时，如果按推荐的那样使用计算节点上的高级 Azure 读取缓存来存储数据文件，则没有太大差别。 原因在于这两个缓存（SQL Server 缓冲池扩展和高级存储读取缓存）都使用计算节点的本地磁盘。
有关此功能的更多详细信息，请查看此文档：<https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension> 

### <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server 的备份/恢复注意事项
将 SQL Server 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SQL Server 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在补救存储崩溃方面，备份现在已变得不太重要。 维护适当备份和恢复计划的首要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 例如，可以通过还原备份，从 2 层 SAP 配置转移到同一个系统的 3 层系统设置。

有三种不同的方法可以将 SQL Server 备份到 Azure 存储：

1. SQL Server 2012 CU4 和更高版本可以通过本机方式将数据库备份到 URL。 博客 [SQL Server 2014 的新功能 - 第 5 部分 - 备份/还原增强功能](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx)中有详细说明。 请参阅 [SQL Server 2012 SP1 CU4 和更高版本][dbms-guide-5.5.1]一章。
2. SQL 2012 CU4 之前的 SQL Server 版本可以使用重定向功能备份到 VHD，而且写入流基本上会流向已配置的 Azure 存储位置。 请参阅 [SQL Server 2012 SP1 CU3 和更低版本][dbms-guide-5.5.2]一章。
3. 最后一种方法是在磁盘设备上执行传统的“从 SQL Server 备份到磁盘”命令。 这与本地部署模式完全相同，本文档不详加讨论。

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 及更高版本
此功能可让你直接备份到 Azure BLOB 存储。 如果没有此方法，则必须备份到其他磁盘，从而耗用磁盘和 IOPS 容量。 其基本理念如下：

 ![使用 SQL Server 2012 备份到 Microsoft Azure 存储 BLOB][dbms-guide-figure-400]

此方案的优点是无需耗用磁盘来存储 SQL Server 备份。 因此，所分配的磁盘较少，而且可以将磁盘 IOPS 的全部带宽都用于数据和日志文件。 请注意，备份的大小上限是 1 TB，如以下文章的“Limitations”（限制）部分中所述：<https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#limitations>。 在备份大小方面，尽管使用 SQL Server 备份压缩的大小会超过 1 TB，但仍需使用本文档的 [SQL Server 2012 SP1 CU3 和更低版本][dbms-guide-5.5.2]一章中所述的功能。

描述根据 Azure Blob 存储从备份还原数据库的[相关文档](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure)建议：如果备份超过 25GB，就不要直接从 Azure BLOB 存储还原。 本文中的建议以性能注意事项为依据，并未考虑功能限制。 因此，随着方案的不同，可能会发生不同的状况。

可以在[此教程](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)中找到有关如何设置和使用此类备份的文档

可以在[此处](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)阅读一系列步骤的示例。

自动执行备份，最重要的就是确保每个备份的 BLOB 都以不同方式命名。 否则，它们会被覆盖，并且还原链会中断。

为了避免混用这三种不同的备份类型，建议在用于备份的存储帐户下面创建不同的容器。 容器可以仅按 VM 创建，也可以按 VM 和备份类型创建。 其架构可能如下：

 ![使用 SQL Server 2012 备份到 Microsoft Azure 存储 BLOB - 不同存储帐户下的不同容器][dbms-guide-figure-500]

在上述示例中，不会对部署 VM 的相同存储帐户执行备份。 将提供一个专用于备份的新存储帐户。 在存储帐户内，会有使用备份类型和 VM 名称的组合创建的不同容器。 这种细分方式可让你更加轻松地管理不同 VM 的备份。

直接写入备份的 BLOB 不会纳入 VM 的数据磁盘计数。 因此，可以将适用于数据和事务日志文件的特定 VM SKU 所装载的数据磁盘数目上限最大化，并且仍能对存储容器执行备份。 

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 及更低版本
若要直接对 Azure 存储进行备份，所需执行的第一个步骤是下载链接到[此](https://www.microsoft.com/download/details.aspx?id=40740) KBA 文章的 msi。

下载 x64 安装文件和文档。 此文件安装名为“Microsoft SQL Server Backup to Microsoft Azure Tool”的程序。 请仔细阅读产品文档。  此工具基本上会以下列方式运行：

* 从 SQL Server 端定义 SQL Server 备份的磁盘位置（请勿使用 D:\ 驱动器作为位置）。
* 利用此工具，可以定义用于将不同备份类型定向到不同 Azure 存储容器的规则。
* 定义了规则之后，此工具就会将备份的写入流重定向到之前定义的 Azure 存储位置的其中一个 VHD/磁盘。
* 此工具会在为 SQL Server 备份定义的 VHD/磁盘上，保留数 KB 大小的小型存根文件。 **此文件应保留在存储位置上，因为它需要再次从 Azure 存储还原。**
  * 如果丢失了存根文件（例如，因为丢失了包含存根文件的存储媒体），但选择了备份到 Microsoft Azure 存储帐户这一选项，则可以通过 Microsoft Azure 存储，从放置存根文件的存储容器下载来恢复该文件。 将存根文件放置在本地计算机上的文件夹中，在这里，工具将配置为检测并上传到具有相同加密密码的相同容器（如果加密与原始规则搭配使用）。 

这意味着，上述适用于较新版 SQL Server 的架构也适用于不允许直接寻址 Azure 存储位置的 SQL Server 版本。

不应将此方法用于支持以本机方式备份 Azure 存储的较新版 SQL Server。 Azure 本机备份限制阻止对 Azure 执行本机备份的情况例外。

#### <a name="other-possibilities-to-back-up-sql-server-databases"></a>其他 SQL Server 数据库备份可行方法
其他数据库备份可行方法之一是将额外的数据磁盘附加到用于存储备份的 VM。 在这种情况下，必须确保磁盘未完全运行。 如果是这样，则必须卸载磁盘，也就是所谓的将其“存档”，并用新的空磁盘替代。 如果执行此操作，就需要将这些 VHD 保存在独立的 Azure 存储帐户中（不同于包含数据库文件的 VHD 所在的帐户）。

第二种可行方法是使用可以附加许多磁盘的大型 VM，例如使用 32VHD 的 D14。 使用存储空间来构建灵活的环境，可以在其中构建共享，之后将共享作为不同 DBMS 服务器的备份目标。

[此处](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx)也提供了一些最佳做法。 

#### <a name="performance-considerations-for-backupsrestores"></a>备份/还原的性能注意事项
与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据文件所用的磁盘数目越少，读取的总体吞吐量就越小。
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大。
* 要写入备份的目标（BLOB、VHD 或磁盘）越少，吞吐量就越低。
* VM 大小越小，从 Azure 存储写入和读取的存储吞吐量配额就越小。 与以下因素无关：是将备份直接存储在 Azure Blob 上，还是先将它们存储在 VHD 中，再存储在 Azure Blob 上。

在较新版本中使用 Microsoft Azure 存储 BLOB 作为备份目标时，只能为每个特定备份指定一个 URL 目标。

但是，在较旧版本中使用“Microsoft SQL Server Backup to Microsoft Azure Tool”时，可以定义多个文件目标。 通过定义多个目标，可以缩放备份，并且备份的吞吐量会更高。 进而也会在 Azure 存储帐户中生成多个文件。 在我们的测试中，通过使用多个文件目标，完全可以达到通过在 SQL Server 2012 SP1 CU4 和更高版本中实施备份扩展可达到的吞吐量。 也不会受到 1 TB 限制的阻碍，就像在 Azure 本机备份中一样。

不过，请记住，吞吐量还取决于用于备份的 Azure 存储帐户位置。 最好是将存储帐户放置在 VM 运行区域以外的区域。 例如，在西欧运行 VM 配置，但用于备份的存储帐户放在北欧。 当然，这会对备份吞吐量产生影响，而且不大可能生成每秒 150MB 的吞吐量，因为似乎只有在目标存储和 VM 运行于同一个区域数据中心时才会生成这么高的吞吐量。

#### <a name="managing-backup-blobs"></a>管理备份 BLOB
需要自行管理备份。 经常执行事务日志备份预期会创建多个 Blob，因此，管理这些 Blob 很容易使 Azure 门户过载。 因此，建议使用 Azure 存储资源管理器。 有几个不错的工具可帮助管理 Azure 存储帐户

* 装有 Azure SDK 的 Microsoft Visual Studio (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure 存储资源管理器 (<https://azure.microsoft.com/downloads/>)
* 第三方工具

有关 Azure 上备份和 SAP 的更完整的讨论，请参阅 [SAP 备份指南](sap-hana-backup-guide.md)以了解详细信息。

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>使用来自 Microsoft Azure Marketplace 的 SQL Server 映像
Microsoft 在 Azure Marketplace 中提供已经包含 SQL Server 版本的 VM。 对于需要 SQL Server 和 Windows 许可证的 SAP 客户，可以通过运行已安装 SQL Server 的 VM，基本满足对许可证的需求。 若要针对 SAP 使用此类映像，必须注意以下事项：

* SQL Server 非评估版的购置成本高于从 Azure Marketplace 部署的“仅限 Windows”VM。 请参阅以下文章来比较价格：<https://azure.microsoft.com/pricing/details/virtual-machines/windows/> 和 <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>。 
* 只能使用 SAP 支持的 SQL Server 版本，例如 SQL Server 2012。
* 安装于 VM（来自 Azure Marketplace）中的 SQL Server 实例的排序规则并不是 SAP NetWeaver 要求 SQL Server 实例运行的排序规则。 不过，可以遵循下一节的指示来更改排序规则。

#### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>更改 Microsoft Windows/SQL Server VM 的 SQL Server 排序规则
因为 Azure Marketplace 中的 SQL Server 映像未设置为使用 SAP NetWeaver 应用程序所需的排序规则，所以需要在部署之后立即更改。 对于 SQL Server 2012，一旦部署了 VM 且管理员能够登录已部署的 VM，就可以使用下列步骤来完成此操作：

* 以管理员身份打开 Windows 命令窗口。
* 将目录更改为 C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012。
* 执行命令：Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`> 是第一次通过库部署 VM 时定义为管理员帐户的帐户。

此过程应该只需要几分钟的时间。 若要确保此步骤最终会有正确的结果，请执行下列步骤：

* 打开 SQL Server Management Studio。
* 打开查询窗口。
* 在 SQL Server master 数据库中执行 sp_helpsort 命令。

所需的结果应如下所示：

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

如果不是这个结果，请停止部署 SAP，并调查为什么安装命令未按预期运行。 **不**支持将 SAP NetWeaver 应用程序部署到 SQL Server 代码页与上述代码页不同的 SQL Server 实例。

### <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure 中适用于 SAP 的 SQL Server 高可用性
如本文前面所述，无法创建使用最早 SQL Server 高可用性功能时所需的共享存储。 此功能会在 Windows Server 故障转移群集 (WSFC) 中使用共享磁盘，为用户数据库（以及最终的 tempdb）安装两个或更多个 SQL Server 实例。 这是 SAP 也支持的长期标准高可用性方法。 由于 Azure 不支持共享存储，因此，无法实现具有共享磁盘群集配置的 SQL Server 高可用性配置。 不过，仍有许多其他高可用性方法，如下列各节所述。

#### <a name="sql-server-log-shipping"></a>SQL Server 日志传送
高可用性 (HA) 的方法之一是 SQL Server 日志传送。 如果参与 HA 配置的 VM 具有有效的名称解析，就不会出现问题，而且 Azure 中的设置与任何本地设置无任何差别。 建议不要完全依赖于 IP 解析。 有关设置日志传送的信息和日志传送的原理，请查看此文档：

<https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server>

若要实现任何高可用性，需要将此类日志传送配置内的 VM 部署到同一个 Azure 可用性集内。

#### <a name="database-mirroring"></a>数据库镜像
SAP 支持的数据库镜像（请参阅 SAP 说明 [965908]）依赖于在 SAP 连接字符串中定义故障转移伙伴。 在跨界方案中，我们假设这两个 VM 位于相同的域中，而且这两个 SQL Server 实例运行所在的用户上下文也是域用户，并在所涉及的这两个 SQL Server 实例中具备足够的特权。 因此，Azure 中的数据库镜像设置与典型的本地设置/配置并无差别。

至于仅限云的部署，最简单的方法是在 Azure 中设置另一个域，以便让这些 DBMS VM（最好是专用的 SAP VM）位于一个域中。

如果域不可行，也可以对数据库镜像终结点使用证书，如此处所述：<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

有关如何在 Azure 中设置数据库镜像的教程，请参阅：<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

#### <a name="sql-server-always-on"></a>SQL Server Always On
由于 AlwaysOn 受本地 SAP 支持（请参阅 SAP 说明 [1772688]），因此，支持在 Azure 中将其与 SAP 搭配使用。 事实上，无法在 Azure 中创建共享磁盘并不表示无法在不同 VM 之间创建 AlwaysOn Windows Server 故障转移群集 (WSFC) 配置。 这只表示不能在群集配置中使用共享磁盘作为仲裁。 因此，可以在 Azure 中构建 Always On WSFC 配置，而且无需选择使用共享磁盘的仲裁类型。 部署这些 VM 的 Azure 环境应该按名称解析 VM，而 VM 应该位于同一个域中。 这适用于仅限 Azure 的部署和跨界部署。 部署 SQL Server 可用性组侦听器（请勿与 Azure 可用性集混淆）时有一些特殊注意事项，因为 Azure 目前不允许创建 AD/DNS 对象，而在本地是允许的。 因此，需要执行一些不同的安装步骤来克服 Azure 的特定行为。

以下是使用可用性组侦听器的一些注意事项：

* 只有将 Windows Server 2012 或更高版本作为 VM 的来宾 OS 时，才支持使用可用性组侦听器。 对于 Windows Server 2012，必须确保应用此补丁：<https://support.microsoft.com/kb/2854082> 
* 对于 Windows Server 2008 R2，此补丁并不存在，而 Always On 需要通过与数据库镜像相同的方式来使用，即，在连接字符串中指定故障转移伙伴（通过 SAP default.pfl 参数 dbs/mss/server 完成 - 请参阅 SAP 说明 [965908]）。
* 使用可用性组侦听器时，数据库 VM 需要连接到专用的负载均衡器。 仅限云部署中的名称解析可能会要求 SAP 系统的所有 VM（应用程序服务器、DBMS 服务器及 (A)SCS 服务器）位于同一个虚拟网络中，或者要求从 SAP 应用程序层维护 etc\host 文件，以解析 SQL Server VM 的 VM 名称。 为了避免 Azure 在这两个 VM 都意外关闭的情况下分配新的 IP 地址，应该在 AlwaysOn 配置中为这些 VM 的网络接口分配静态 IP 地址（有关如何定义静态 IP 地址的信息，请参阅[此文][virtual-networks-reserved-private-ip]）

[comment]: <> (旧博客)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* 构建群集需要分配特殊 IP 地址的 WSFC 群集配置时，需执行一些特殊的步骤，因为 Azure 及其当前功能会为群集名称分配与创建群集所在的节点相同的 IP 地址。 这表示必须执行手动步骤，为群集分配不同的 IP 地址。
* 可用性组侦听器将创建于具有 TCP/IP 终结点的 Azure 中，这些终结点会分配给运行可用性组主要和次要副本的 VM。
* 可能需要使用 ACL 来保护这些终结点。

[comment]: <> (TODO 旧博客)
[comment]: <> (学完[此处][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]提供的教程后，将全面了解在 Azure 上安装 AlwaysOn 配置的详细步骤和必要条件)
[comment]: <> (Azure 库中的预配置 AlwaysOn 设置 <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> ([此教程][virtual-machines-windows-classic-ps-sql-int-listener]详细介绍了如何创建可用性组侦听器)
[comment]: <> (此处详细介绍了如何使用 ACL 来保护网络终结点：)
[comment]: <> (*    <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx>)
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)  
[comment]: <> (*    <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>) 

也可以在不同的 Azure 区域部署 SQL Server AlwaysOn 可用性组。 此功能使用 Azure VNet 到 Vnet 连接（[更多详细信息][virtual-networks-configure-vnet-to-vnet-connection]）。

[comment]: <> (TODO 旧博客)
[comment]: <> (有关此类方案中 SQL Server AlwaysOn 可用性组的设置信息，请参阅此处：<https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>。) 

#### <a name="summary-on-sql-server-high-availability-in-azure"></a>Azure 中的 SQL Server 高可用性汇总
Azure 存储会保护内容，因此，更加没有理由坚持使用热备用映像。 这意味着，高可用性方案只需针对下列情况提供保护：

* 由于在 Azure 的服务器群集上进行维护或其他原因而导致 VM 完全不可用
* SQL Server 实例中的软件问题
* 防止因人为删除数据而需要进行时间点恢复

寻找相应的技术，你可能认为前两种情况可以使用数据库镜像或 AlwaysOn 来解决，而第三种情况只能使用日志传送来解决。

需要在 AlwaysOn 更为复杂的设置（相比数据库镜像）与它的优势之间取得平衡。 下面列出了它的优势，例如：

* 可读取的次要副本。
* 来自次要副本的备份。
* 更好的可伸缩性。
* 多个次要副本。

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>适用于 Azure 上的 SAP 的 SQL Server 总体摘要
本指南提供了许多建议，因此，建议在规划 Azure 部署之前，反复阅读本指南。 但是，一般而言，请务必遵循有关 Azure 上的 DBMS 的前十大要点：

[comment]: <> (2.3 吞吐量比什么更高？比一个 VHD 更高？)
1. 使用最新的 DBMS 版本（例如 SQL Server 2014），其在 Azure 中最具优势。 单论 SQL Server，SQL Server 2012 SP1 CU4 便已足够，该版本包含对 Azure 存储进行备份的功能。 但是，如果与 SAP 搭配使用，则我们建议至少使用 SQL Server 2014 SP1 CU1 或 SQL Server 2012 SP2 以及最新的 CU。
2. 在 Azure 中仔细规划 SAP 系统布局，以平衡数据文件布局和 Azure 限制：
   * 不要有太多磁盘，但必须足以确保可以达到所需的 IOPS。
   * 如果不使用托管磁盘，请记住，不仅每个 Azure 存储帐户的 IOPS 受到限制，每个 Azure 订阅的存储帐户数目也受到限制（[更多详细信息][azure-subscription-service-limits]）。 
   * 只有在需要达到更高的吞吐量时，才在磁盘上划分带区。
3. 永远不要在 D:\ 驱动器上安装软件或放置任何需要永久保留的文件，因为它不是永久性的，此驱动器上的所有内容都会在 Windows 重新启动时丢失。
4. 不要对 Azure 标准存储使用磁盘缓存。
5. 不要使用 Azure 异地复制的存储帐户。  对 DBMS 工作负荷使用本地冗余。
6. 使用 DBMS 供应商的 HA/DR 解决方案来复制数据库数据。
7. 始终使用名称解析，不要依赖 IP 地址。
8. 尽可能使用最高强度的数据库压缩。 对 SQL Server 而言，即页面压缩。
9. 谨慎使用来自 Azure Marketplace 的 SQL Server 映像。 如果使用 SQL Server 映像，就必须更改实例排序规则，才能在其上安装任何 SAP NetWeaver 系统。
10. 按照[部署指南][deployment-guide]中的说明，安装和配置适用于 Azure 的 SAP 主机监视。

## <a name="specifics-to-sap-ase-on-windows"></a>有关 Windows 上的 SAP ASE 的具体信息
自从有了 Microsoft Azure，可以轻松地将现有 SAP ASE 应用程序迁移到 Azure 虚拟机。 借助虚拟机中的 SAP ASE，可以轻松地将这些应用程序迁移到 Microsoft Azure，从而减少部署、管理和维护企业级应用程序的总拥有成本。 借助 Azure 虚拟机中的 SAP ASE，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。

可以在此处找到适用于 Azure 虚拟机的 SLA：<https://azure.microsoft.com/support/legal/sla/virtual-machines>

相比其他公有云虚拟化产品，我们确信 Microsoft Azure 托管的虚拟机将运行得非常顺利，但个别结果可能不同。 经过 SAP 认证的不同 VM SKU 的 SAP 大小调整 SAPS 数目会在单独的 SAP 说明 [1928533] 中提供。

有关 Azure 存储使用情况、SAP VM 部署或 SAP 监视的表述与建议适用于 SAP ASE 与 SAP 应用程序的联合部署，如本文档前四章所述。

### <a name="sap-ase-version-support"></a>SAP ASE 版本支持
SAP 目前支持 SAP ASE 版本 16.0，可与 SAP Business Suite 产品搭配使用。 适用于 SAP ASE 服务器的所有更新或者适用于可与 SAP Business Suite 产品搭配使用的 JDBC 和 ODBC 驱动程序的所有更新，仅通过 SAP Service Marketplace (<https://support.sap.com/swdc>) 提供。

与本地安装一样，不要直接从 Sybase 网站下载适用于 SAP ASE 服务器或适用于 JDBC 和 ODBC 驱动程序的更新。 有关支持在本地和 Azure 虚拟机中与 SAP Business Suite 产品搭配使用的补丁的详细信息，请参阅下列 SAP 说明：

* [1590719]
* [1973241]

有关在 SAP ASE 上运行 SAP Business Suite 的常规信息可在 [SCN](https://www.sap.com/community/topic/ase.html) 中找到

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 相关 SAP ASE 的 SAP ASE 配置准则
#### <a name="structure-of-the-sap-ase-deployment"></a>SAP ASE 部署的结构
按照一般的说明，SAP ASE 可执行文件应该位于或安装在 VM 的 OS 磁盘的系统驱动器（驱动器 c:\)）中。 一般情况下，SAP NetWeaver 工作负荷对大部分 SAP ASE 系统和工具数据库的使用率并不很高。 因此，系统和工具数据库（master、model、saptools、sybmgmtdb、sybsystemdb）也可以保留在驱动器 C:\ 上。 

唯一的例外就是包含 SAP ASE 创建的所有工作表和临时表的临时数据库，在某些 SAP ERP 和所有 BW 工作负荷中，它可能需要较高的数据量或 I/O 操作量，以至于不适合放在原始 VM 的 OS 磁盘（驱动器 c:\)中。

根据用于安装系统的 SAPInst/SWPM 版本，数据库可能包含：

* 安装 SAP ASE 时创建的单个 SAP ASE tempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及 SAP 安装例程所创建的附加 saptempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及为满足 ERP/BW 特定 tempdb 需求而手动创建的附加 tempdb（例如，遵循 SAP 说明 [1752266]）

在特定的 ERP 或所有 BW 工作负荷中，从性能角度考虑，以下做法很有意义：将额外创建的 tempdb（通过 SWPM 或手动）的 tempdb 设备保存在 C:\ 以外的驱动器上。 如果没有附加 tempdb，建议创建一个（SAP 说明 [1752266]）。

在这类系统中，应针对额外创建的 tempdb 执行下列步骤：

* 将第一个 tempdb 设备移至 SAP 数据库的第一个设备
* 将 tempdb 设备添加到每个包含 SAP 数据库设备的 VHD

此配置让 tempdb 耗用的空间比系统驱动器能够提供的还多。 作为参考，可以在运行于本地的现有系统上检查 tempdb 设备的大小。 或者，这类配置会针对 tempdb 启用系统驱动器无法提供的 IOPS 数目。 再次提醒，本地运行的系统可以用来监视 tempdb 的 I/O 工作负荷。

永远不要将任何 SAP ASE 设备放入 VM 的 D:\ 驱动器。 这也适用于 tempdb，即使 tempdb 中保留的对象只是暂时性的。

#### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先执行压缩，再上载到 Azure（如果尚未这样做），原因有以下几点：

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

需要确保 ICM 使用完全限定的主机名，并且此名称可在尝试打开 DBACockpit 的计算机上解析，具体取决于是否以及如何通过站点到站点、多站点或 ExpressRoute（跨界部署）连接托管 SAP 系统的 Azure 虚拟机。 请参阅 SAP 说明 [773830]，了解 ICM 如何根据配置文件参数来确定完全限定的主机名，并在必要时显式设置 icm/host_name_full 参数。

如果在仅限云方案中部署了 VM，但未在本地与 Azure 之间建立跨界连接，则需要定义一个公共 IP 地址和一个 domainlabel。 VM 的公共 DNS 名称格式将如下所示：

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
将 SAP ASE 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SAP ASE 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在补救存储崩溃方面，备份现在已变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 例如，可以通过还原备份，从 2 层 SAP 配置转移到同一个系统的 3 层系统设置。

在 Azure 中备份和还原数据库的方式与在本地一样。 请参阅 SAP 说明：

* [1588316]
* [1585981]

，以获取有关创建转储配置和计划备份的详细信息。 根据策略和需求，可以在现有的某个磁盘上配置磁盘的数据库和日志转储，也可以添加一个附加磁盘以供备份使用。 为了减少在发生错误时丢失数据的风险，建议使用不带任何数据库设备的磁盘。

除了数据和 LOB 压缩，SAP ASE 还提供备份压缩。 若要让数据库和日志转储占用较少的空间，建议使用备份压缩。 有关详细信息，请参阅 SAP 说明 [1588316]。 如果打算从 Azure 虚拟机将备份或包含备份转储的 VHD 下载至本地，则压缩备份对于减少要传输的数据量也很重要。

请勿使用驱动器 D:\ 作为数据库或日志转储目标。

#### <a name="performance-considerations-for-backupsrestores"></a>备份/还原的性能注意事项
与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

若要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 在 SAP ASE 级别创建转储配置，该配置将使用多个目标目录来写入转储

本指南的前面部分已讨论过如何基于已装载的多个磁盘对卷划分带区。 有关在 SAP ASE 转储配置中使用多个目录的详细信息，请参阅存储过程 sp_config_dump 的相关文档，此存储过程用于在 [Sybase 信息中心](http://infocenter.sybase.com/help/index.jsp)创建转储配置。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 的灾难恢复
#### <a name="data-replication-with-sap-sybase-replication-server"></a>使用 SAP Sybase 复制服务器复制数据
利用 SAP Sybase 复制服务器 (SRS)，SAP ASE 可提供热备用解决方案，以异步方式将数据库事务传输到远程位置。 

与在本地一样，也可以在 Azure 虚拟机托管的 VM 中正常安装和运行 SRS。

SAP ASE HADR 不需要 Azure 内部负载均衡器，并且不依赖于 OS 级别的群集，可在 Azure Windows 和 Linux VM 上工作。 有关 SAP ASE HADR 的详细信息，请参阅 [SAP ASE HADR 用户指南](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html)。

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
按照一般的说明，SAP ASE 可执行文件应该位于或安装在 VM 的根文件系统 (/sybase ) 中。 一般情况下，SAP NetWeaver 工作负荷对大部分 SAP ASE 系统和工具数据库的使用率并不很高。 因此，系统和工具数据库（master、model、saptools、sybmgmtdb、sybsystemdb）也可以保留在根文件系统上。 

唯一的例外就是包含 SAP ASE 创建的所有工作表和临时表的临时数据库，在某些 SAP ERP 和所有 BW 工作负荷中，它可能需要较高的数据量或 I/O 操作量，以至于不适合放在原始 VM 的 OS 磁盘中。

根据用于安装系统的 SAPInst/SWPM 版本，数据库可能包含：

* 安装 SAP ASE 时创建的单个 SAP ASE tempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及 SAP 安装例程所创建的附加 saptempdb
* 安装 SAP ASE 时创建的 SAP ASE tempdb，以及为满足 ERP/BW 特定 tempdb 需求而手动创建的附加 tempdb（例如，遵循 SAP 说明 [1752266]）

在特定的 ERP 或所有 BW 工作负荷中，从性能角度考虑，以下做法很有意义：将额外创建的 tempdb（通过 SWPM 或手动）的 tempdb 设备保存在单独的文件系统上，该文件系统可以用单个 Azure 数据磁盘或者跨多个 Azure 数据磁盘的 Linux RAID 表示。 如果没有附加 tempdb，建议创建一个（SAP 说明 [1752266]）。

在这类系统中，应针对额外创建的 tempdb 执行下列步骤：

* 将第一个 tempdb 目录移至 SAP 数据库的第一个文件系统
* 将 tempdb 目录添加到每个包含 SAP 数据库文件系统的磁盘

此配置让 tempdb 耗用的空间比系统驱动器能够提供的还多。 作为参考，可以在运行于本地的现有系统上检查 tempdb 目录的大小。 或者，这类配置会针对 tempdb 启用系统驱动器无法提供的 IOPS 数目。 再次提醒，本地运行的系统可以用来监视 tempdb 的 I/O 工作负荷。

永远不要将任何 SAP ASE 目录放入 VM 的 /mnt 或 /mnt/resource。 这也适用于 tempdb（即使 tempdb 中保留的对象只是暂时性的），因为 /mnt 或 /mnt/resource 是默认的非永久性 Azure VM 临时空间。 可以在[此文][virtual-machines-linux-how-to-attach-disk]中找到有关 Azure VM 临时空间的更多详细信息

#### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，我们建议确保先使用 SAP ASE 压缩，然后再将现有 SAP 数据库上传到 Azure。

之所以建议先执行压缩，再上载到 Azure（如果尚未这样做），原因有以下几点：

* 减少要上传到 Azure 的数据量
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短
* 较小的数据库大小可以降低磁盘分配的成本

数据和 LOB 压缩可以在 Azure 虚拟机托管的 VM 中运行，如同在本地运行一样。 有关如何检查是否已在现有 SAP ASE 数据库中使用压缩的更多详细信息，请查看 SAP 说明 [1750510]。 有关数据库压缩的其他信息，请参阅 SAP 说明 [2121797]。

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

需要确保 ICM 使用完全限定的主机名，并且此名称可在尝试打开 DBACockpit 的计算机上解析，具体取决于是否以及如何通过站点到站点、多站点或 ExpressRoute（跨界部署）连接托管 SAP 系统的 Azure 虚拟机。 请参阅 SAP 说明 [773830]，了解 ICM 如何根据配置文件参数来确定完全限定的主机名，并在必要时显式设置 icm/host_name_full 参数。

如果在仅限云方案中部署了 VM，但未在本地与 Azure 之间建立跨界连接，则需要定义一个公共 IP 地址和一个 domainlabel。 VM 的公共 DNS 名称格式将如下所示：

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
将 SAP ASE 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SAP ASE 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在补救存储崩溃方面，备份现在已变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 例如，可以通过还原备份，从 2 层 SAP 配置转移到同一个系统的 3 层系统设置。

在 Azure 中备份和还原数据库的方式与在本地一样。 请参阅 SAP 说明：

* [1588316]
* [1585981]

，以获取有关创建转储配置和计划备份的详细信息。 根据策略和需求，可以在现有的某个磁盘上配置磁盘的数据库和日志转储，也可以添加一个附加磁盘以供备份使用。 为了减少在发生错误时丢失数据的风险，建议使用不带任何数据库目录/文件的磁盘。

除了数据和 LOB 压缩，SAP ASE 还提供备份压缩。 若要让数据库和日志转储占用较少的空间，建议使用备份压缩。 有关详细信息，请参阅 SAP 说明 [1588316]。 如果打算从 Azure 虚拟机将备份或包含备份转储的 VHD 下载至本地，则压缩备份对于减少要传输的数据量也很重要。

请勿将 Azure VM 临时空间 /mnt 或 /mnt/resource 用作数据库或日志转储目标。

#### <a name="performance-considerations-for-backupsrestores"></a>备份/还原的性能注意事项
与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（Linux 软件 RAID、磁盘）越少，吞吐量就越低

若要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 在 SAP ASE 级别创建转储配置，该配置将使用多个目标目录来写入转储

本指南的前面部分已讨论过如何基于已装载的多个磁盘对卷划分带区。 有关在 SAP ASE 转储配置中使用多个目录的详细信息，请参阅存储过程 sp_config_dump 的相关文档，此存储过程用于在 [Sybase 信息中心](http://infocenter.sybase.com/help/index.jsp)创建转储配置。

### <a name="disaster-recovery-with-azure-vms"></a>Azure VM 的灾难恢复
#### <a name="data-replication-with-sap-sybase-replication-server"></a>使用 SAP Sybase 复制服务器复制数据
利用 SAP Sybase 复制服务器 (SRS)，SAP ASE 可提供热备用解决方案，以异步方式将数据库事务传输到远程位置。 

与在本地一样，也可以在 Azure 虚拟机托管的 VM 中正常安装和运行 SRS。

目前不支持通过 SAP 复制服务器实现 ASE HADR。 未来可能会针对 Microsoft Azure 平台进行测试和发行。

## <a name="specifics-to-oracle-database-on-windows"></a>有关 Windows 上的 Oracle 数据库的具体信息
Oracle 支持在 Microsoft Windows Hyper-V 和 Azure 上运行 Oracle 软件。 有关 Windows Hyper-V 和 Azure 常规支持的详细信息，请查看：<https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

根据常规支持，SAP 应用程序使用 Oracle 数据库的特定方案也受到支持。 文档的这一部分对此进行了详细介绍。

### <a name="oracle-version-support"></a>Oracle 版本支持
有关支持在 Azure 虚拟机上的 Oracle 中运行 SAP 的 Oracle 版本及相应 OS 版本，可参阅 SAP 说明 [2039619]。

有关在 Oracle 上运行 SAP Business Suite 的常规信息可在 1DX 中找到：<https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 Oracle 配置准则
#### <a name="storage-configuration"></a>存储配置
仅支持一个使用 NTFS 格式化磁盘的 Oracle 实例。 所有数据库文件都必须存储在基于 VHD 或托管磁盘的 NTFS 文件系统上。 这些磁盘装载到 Azure VM，基于 Azure 页 BLOB 存储 (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) 或托管磁盘 (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>)。 任何类型的网络驱动器或远程共享（例如 Azure 文件服务：

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

**不**支持 Oracle 数据库文件！

使用基于 Azure 页 BLOB 存储或托管磁盘的磁盘时，本文档的 [VM 和数据磁盘的缓存][dbms-guide-2.1]与 [Microsoft Azure 存储][dbms-guide-2.3]这两个章节中的表述也适用于利用 Oracle 数据库进行的部署。

如先前在文档通用部分中所述，Azure 磁盘的 IOPS 吞吐量存在配额。 确切的配额因所用 VM 类型而异。 可以在[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 找到 VM 类型及其配额的列表。

若要确定支持的 Azure VM 类型，请参阅 SAP 说明 [1928533]。

只要每个磁盘当前的 IOPS 配额能满足需求，就可以将所有 DB 文件存储在装载的单个磁盘上。 

如果需要更多 IOPS，我们建议使用 Windows 存储池（仅适用于 Windows Server 2012 和更高版本）或适用于 Windows 2008 R2 的 Windows 带区，基于已装载的多个磁盘来创建一个大型逻辑设备（另请参阅本文档的[软件 RAID][dbms-guide-2.2] 一章）。 这种方法可以简化磁盘空间的管理开销，避免跨已装载的多个磁盘手动分发文件。

#### <a name="backup--restore"></a>备份/还原
支持通过适用于 Oracle 的 SAP BR* 工具提供备份/还原功能，其方式与在标准 Windows Server 操作系统和 Hyper-V 上一样。 Oracle 恢复管理器 (RMAN) 也支持备份到磁盘以及从磁盘还原。

#### <a name="high-availability"></a>高可用性
支持通过 Oracle Data Guard 实现高可用性和灾难恢复。 可以在[此文档][virtual-machines-windows-classic-configure-oracle-data-guard]中找到详细信息。

#### <a name="other"></a>其他
Azure 可用性集或 SAP 监视等所有其他常规领域也适用于使用 Oracle 数据库进行的 VM 部署，如本文档的前三章所述。

## <a name="specifics-to-oracle-database-on-oracle-linux"></a>有关 Oracle Linux 上的 Oracle 数据库的具体信息
Oracle 支持在 Microsoft Windows Hyper-V 和 Azure 上运行 Oracle 软件。 有关 Windows Hyper-V 和 Azure 常规支持的详细信息，请查看：<https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces> 

根据常规支持，SAP 应用程序使用 Oracle 数据库的特定方案也受到支持。 文档的这一部分对此进行了详细介绍。

### <a name="oracle-version-support"></a>Oracle 版本支持
有关支持在 Azure 虚拟机上的 Oracle 中运行 SAP 的 Oracle 版本及相应 OS 版本，可参阅 SAP 说明 [2039619]。

有关在 Oracle 上运行 SAP Business Suite 的常规信息可在 1DX 中找到：<https://www.sap.com/community/topic/oracle.html>

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 Oracle 配置准则
#### <a name="storage-configuration"></a>存储配置
仅支持一个使用 ext3、ext4 和 xfs 格式化磁盘的 Oracle 实例。 所有数据库文件都必须存储在基于 VHD 或托管磁盘的这些文件系统上。 这些磁盘装载到 Azure VM，基于 Azure 页 BLOB 存储 (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) 或托管磁盘 (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>)。 任何类型的网络驱动器或远程共享（例如 Azure 文件服务：

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx> 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

**不**支持 Oracle 数据库文件！

使用基于 Azure 页 BLOB 存储或托管磁盘的磁盘时，本文档的 [VM 和数据磁盘的缓存][dbms-guide-2.1]与 [Microsoft Azure 存储][dbms-guide-2.3]这两个章节中的表述也适用于利用 Oracle 数据库进行的部署。

如先前在文档通用部分中所述，Azure 磁盘的 IOPS 吞吐量存在配额。 确切的配额因所用 VM 类型而异。 可以在[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 找到 VM 类型及其配额的列表。

若要确定支持的 Azure VM 类型，请参阅 SAP 说明 [1928533]

只要每个磁盘当前的 IOPS 配额能满足需求，就可以将所有 DB 文件存储在装载的单个磁盘上。 

如果需要更多 IOPS，我们建议使用 LVM（逻辑卷管理器）或 MDADM 跨多个已装载磁盘创建一个大型的逻辑卷。 另请参阅本文档的[软件 RAID][dbms-guide-2.2] 一章。 这种方法可以简化磁盘空间的管理开销，避免跨已装载的多个磁盘手动分发文件。

#### <a name="backup--restore"></a>备份/还原
支持通过适用于 Oracle 的 SAP BR* 工具提供备份/还原功能，其方式与在裸机和 Hyper-V 上一样。 Oracle 恢复管理器 (RMAN) 也支持备份到磁盘以及从磁盘还原。

#### <a name="high-availability"></a>高可用性
支持通过 Oracle Data Guard 实现高可用性和灾难恢复。 可以在[此文档][virtual-machines-windows-classic-configure-oracle-data-guard]中找到详细信息。

#### <a name="other"></a>其他
Azure 可用性集或 SAP 监视等所有其他常规领域也适用于使用 Oracle 数据库进行的 VM 部署，如本文档的前三章所述。

## <a name="specifics-for-the-sap-maxdb-database-on-windows"></a>有关 Windows 上的 SAP MaxDB 数据库的具体信息
### <a name="sap-maxdb-version-support"></a>SAP MaxDB 版本支持
SAP 目前支持 SAP MaxDB 版本 7.9，该版本可以与 Azure 中基于 SAP NetWeaver 的产品搭配使用。 适用于 SAP MaxDB 服务器的所有更新或者适用于可与基于 SAP NetWeaver 的产品搭配使用的 JDBC 和 ODBC 驱动程序的所有更新，仅通过 SAP Service Marketplace (<https://support.sap.com/swdc>) 提供。
有关在 SAP MaxDB 上运行 SAP NetWeaver 的常规信息可在 <https://www.sap.com/community/topic/maxdb.html> 中找到。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS 支持的 Microsoft Windows 版本和 Azure VM 类型
若要为 Azure 上的 SAP MaxDB DBMS 查找受支持的 Microsoft Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的操作系统 Microsoft Windows，也就是 Microsoft Windows 2012 R2。

### <a name="available-sap-maxdb-documentation"></a>可用的 SAP MaxDB 文档
可以在 SAP 说明 [767598] 中找到更新的 SAP MaxDB 文档列表

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP MaxDB 配置准则
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>存储配置
适用于 SAP MaxDB 的 Azure 存储最佳做法遵循 [RDBMS 部署的结构][dbms-guide-2]一章中所述的常规建议。

> [!IMPORTANT]
> 与其他数据库一样，SAP MaxDB 也有数据和日志文件。 不过，在 SAP MaxDB 术语中，正确的词汇是“卷”（不是“文件”）。 例如，有 SAP MaxDB 数据卷和日志卷。 请勿与 OS 磁盘卷混淆。 
> 
> 

简而言之，必须：

* 如果使用 Azure 存储帐户，则如 [Microsoft Azure 存储][dbms-guide-2.3]一章中指定的那样，将保存 SAP MaxDB 数据和日志卷（即文件）的 Azure 存储帐户设置为**本地冗余存储 (LRS)**。
* 将 SAP MaxDB 数据卷（即文件）的 IO 路径与日志卷（即文件）的 IO 路径隔开。 这表示 SAP MaxDB 数据卷（即文件）必须安装在一个逻辑驱动器上，而 SAP MaxDB 日志卷（即文件）必须安装在另一个逻辑驱动器上。
* 根据 [VM 和数据磁盘的缓存][dbms-guide-2.1]一章中所述，为每个磁盘设置适当的缓存类型，具体取决于是将其用于 SAP MaxDB 数据卷还是日志卷（即文件），以及是使用 Azure 标准存储还是 Azure 高级存储。
* 只要每个磁盘当前的 IOPS 配额能满足需求，就可以将所有数据卷存储在装载的单个磁盘上，同时将所有数据库日志卷存储在装载的另一个磁盘上。
* 如果需要更多 IOPS 和/或空间，我们建议使用 Microsoft Windows 存储池（仅适用于 Microsoft Windows Server 2012 和更高版本）或适用于 Microsoft Windows 2008 R2 的 Microsoft Windows 带区，基于已装载的多个磁盘来创建一个大型逻辑设备。 另请参阅本文档的[软件 RAID][dbms-guide-2.2] 一章。 这种方法可以简化磁盘空间的管理开销，避免跨已装载的多个磁盘手动分发文件。
* 若要满足最高的 IOPS 需求，可以使用 DS 系列和 GS 系列 VM 上提供的 Azure 高级存储。

![适用于 SAP MaxDB DBMS 的 Azure IaaS VM 的参考配置][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>备份和还原
将 SAP MaxDB 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SAP MaxDB 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在保护系统以免发生存储故障以及更严重的操作或管理故障方面，备份现在已变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑或人为错误。 因此，其目标是使用备份将数据库还原到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 例如，可以通过还原备份，从 2 层 SAP 配置转移到同一个系统的 3 层系统设置。

在 Azure 中备份和还原数据库的方式与在本地系统中一样，因此，可以使用标准的 SAP MaxDB 备份/还原工具，SAP 说明 [767598] 中列出的其中一个 SAP MaxDB 文档对这些工具进行了说明。 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>备份和还原的性能注意事项
与裸机部署一样，备份和还原的性能取决于可以并行读取的卷数目，以及这些卷的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，总体读取吞吐量就越低
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

要增加要写入的目标数目，可以使用或者结合使用以下两个选项，具体视需求而定：

* 将单独的卷专用于备份
* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区磁盘卷上的 IOPS 吞吐量
* 为下列各项配备单独的专用逻辑磁盘设备：
  * SAP MaxDB 备份卷（即文件）
  * SAP MaxDB 数据卷（即文件）
  * SAP MaxDB 日志卷（即文件）

本文档前面的[软件 RAID][dbms-guide-2.2] 一章中已讨论过如何基于已装载的多个磁盘对卷划分带区。 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>其他
Azure 可用性集或 SAP 监视等所有其他常规领域也适用于使用 SAP MaxDB 数据库进行的 VM 部署，如本文档的前三章所述。
其他特定于 SAP MaxDB 的设置对 Azure VM 是透明的，相关介绍请参阅 SAP 说明 [767598] 以及下列 SAP 说明中列出的不同文档：

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-on-windows"></a>有关 Windows 上的 SAP liveCache 的具体信息
### <a name="sap-livecache-version-support"></a>SAP liveCache 版本支持
Azure 虚拟机支持的 SAP liveCache 最低版本是针对 **EhP 2 for SAP SCM 7.0** 和更高版本发行的 **SAP LC/LCAPPS 10.0 SP 25**，其中包括 **liveCache 7.9.08.31** 和 **LCA-Build 25**。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS 支持的 Microsoft Windows 版本和 Azure VM 类型
若要为 Azure 上的 SAP liveCache DBMS 查找受支持的 Microsoft Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的操作系统 Microsoft Windows Server。 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP liveCache 配置准则
#### <a name="recommended-azure-vm-types"></a>建议的 Azure VM 类型
SAP liveCache 是一种执行大量计算的应用程序，因此，RAM 与 CPU 的数量和速度会对 SAP liveCache 性能产生重大影响。 

对于 SAP 支持的 Azure VM 类型（SAP 说明 [1928533]），分配给 VM 的所有虚拟 CPU 资源均由虚拟机监控程序的专用物理 CPU 资源提供支持。 不会发生过度预配（因此也不会发生 CPU 资源竞争）。

同样地，对于 SAP 支持的所有 Azure VM 实例类型，VM 内存会全部映射到物理内存 - 例如，不会使用过度预配（过载）。

从这个角度看，强烈建议使用新的 D 系列或 DS 系列（搭配 Azure 高级存储）Azure VM 类型，因为它们的处理器速度比 A 系列快 60%。 若要获得最高的 RAM 和 CPU 负载，可以使用 G 系列和 GS 系列（搭配 Azure 高级存储）VM，这两个系列配备最新的 Intel?? Xeon?? 处理器 E5 v3 系列，其内存是 D/DS 系列的两倍，固态硬盘存储 (SSD) 是 D/DS 系列的四倍。

#### <a name="storage-configuration"></a>存储配置
SAP liveCache 以 SAP MaxDB 技术为基础，因此，[存储配置][dbms-guide-8.4.1]一章中针对 SAP MaxDB 提出的所有 Azure 存储最佳做法建议也适用于 SAP liveCache。 

#### <a name="dedicated-azure-vm-for-livecache"></a>liveCache 专用的 Azure VM
SAP liveCache 会密集使用计算能力，因此，为了有效使用该技术，强烈建议将其部署在专用的 Azure 虚拟机上。 

![为有效使用 liveCache 而专用的 Azure VM][dbms-guide-figure-700]

#### <a name="backup-and-restore"></a>备份和还原
备份和还原（包括性能注意事项）已在相关的 SAP MaxDB 章节[备份和还原][dbms-guide-8.4.2]以及[备份和还原的性能注意事项][dbms-guide-8.4.3]中加以说明。 

#### <a name="other"></a>其他
所有其他常规领域已在与 SAP MaxDB 相关的[此章][dbms-guide-8.4.4]中加以说明。 

## <a name="specifics-for-the-sap-content-server-on-windows"></a>有关 Windows 上的 SAP 内容服务器的具体信息
SAP 内容服务器是基于服务器的独立组件，可以存储电子文档等不同格式的内容。 SAP 内容服务器通过技术开发提供，旨在以跨应用程序的方式用于任何 SAP 应用程序。 它安装于一个独立的系统上。 其存储的典型内容包括来自 Knowledge Warehouse 的培训材料和文档，或者源自 mySAP PLM 文档管理系统的技术绘图。 

### <a name="sap-content-server-version-support"></a>SAP 内容服务器版本支持
SAP 目前支持：

* **SAP 内容服务器**版本 **6.50（和更高版本）**
* **SAP MaxDB 版本 7.9**
* **Microsoft IIS (Internet Information Server) 版本 8.0（和更高版本）**

强烈建议使用最新版的 SAP 内容服务器（在撰写本文档时为 **6.50 SP4**），以及最新版的 **Microsoft IIS 8.5**。 

在 [SAP 产品可用性对照表 (PAM)][sap-pam] 中查看支持的最新 SAP 内容服务器和 Microsoft IIS 版本。

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP 内容服务器支持的 Microsoft Windows 和 Azure VM 类型
若要为 Azure 上的 SAP 内容服务器查找受支持的 Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的 Microsoft Windows Server。

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP 内容服务器配置准则
#### <a name="storage-configuration"></a>存储配置
如果将 SAP 内容服务器配置为将文件存储在 SAP MaxDB 数据库中，那么，[存储配置][dbms-guide-8.4.1]一章中针对 SAP MaxDB 提出的所有 Azure 存储最佳做法建议也适用于 SAP 内容服务器方案。 

如果将 SAP 内容服务器配置为将文件存储在文件系统中，则建议使用专用的逻辑驱动器。 如[软件 RAID][dbms-guide-2.2] 一章中所述，通过使用 Windows 存储空间，还可以增加逻辑磁盘的大小和 IOPS 吞吐量。 

#### <a name="sap-content-server-location"></a>SAP 内容服务器位置
SAP 内容服务器必须部署在部署了 SAP 系统的 Azure 区域和 Azure VNET 中。 可以自行决定要将 SAP 内容服务器组件部署在专用的 Azure VM 上还是运行 SAP 系统的 VM 上。 

![SAP 内容服务器专用的 Azure VM][dbms-guide-figure-800]

#### <a name="sap-cache-server-location"></a>SAP 缓存服务器位置
SAP 缓存服务器是一个基于服务器的附加组件，可在本地提供对（缓存）文档的访问权限。 SAP 缓存服务器会缓存 SAP 内容服务器的文档。 如果必须从不同的位置多次检索文档，这样做可以优化网络流量。 一般规则是，SAP 缓存服务器在物理上必须靠近访问 SAP 缓存服务器的客户端。 

可以使用以下两个选项：

1. **客户端是后端 SAP 系统**：如果将后端 SAP 系统配置为访问 SAP 内容服务器，则该 SAP 系统就是客户端。 由于 SAP 系统和 SAP 内容服务器部署在同一个 Azure 区域（在相同的 Azure 数据中心），它们在物理上是彼此靠近的。 因此，不需要专用的 SAP 缓存服务器。 SAP UI 客户端（SAP GUI 或 Web 浏览器）可直接访问 SAP 系统，而 SAP 系统会从 SAP 内容服务器检索文档。
2. **客户端是本地 Web 浏览器**：SAP 内容服务器可配置为由 Web 浏览器直接访问。 在此情况下，本地运行的 Web 浏览器就是 SAP 内容服务器的客户端。 本地数据中心与 Azure 数据中心位于不同的物理位置（理想情况下相邻）。 本地数据中心通过 Azure 站点到站点 VPN 或 ExpressRoute 连接到 Azure。 虽然这两个选项均提供到 Azure 的安全 VPN 网络连接，但站点到站点网络连接不会在本地数据中心与 Azure 数据中心之间提供网络带宽和延迟 SLA。 若要加快对文档的访问，可以执行以下操作之一：
   1. 安装本地 SAP 缓存服务器，靠近本地 Web 浏览器（[此][dbms-guide-900-sap-cache-server-on-premises]图中的选项）
   2. 配置 Azure ExpressRoute，以便在本地数据中心与 Azure 数据中心之间提供高速且低延迟的专用网络连接。

![用于安装本地 SAP 缓存服务器的选项][dbms-guide-figure-900]
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>备份/还原
如果将 SAP 内容服务器配置为将文件存储在 SAP MaxDB 数据库中，其备份/还原过程和性能注意事项已在 SAP MaxDB 章节[备份和还原][dbms-guide-8.4.2]以及[备份和还原的性能注意事项][dbms-guide-8.4.3]中加以说明。 

如果将 SAP 内容服务器配置为将文件存储在文件系统中，有一个选项是针对文档所在的整个文件结构执行手动备份/还原。 与 SAP MaxDB 备份/还原类似，建议为备份使用专用的磁盘卷。 

#### <a name="other"></a>其他
其他特定于 SAP 内容服务器的设置对 Azure VM 是透明的，相关介绍请参阅各种文档和 SAP 说明：

* <https://service.sap.com/contentserver> 
* SAP 说明 [1619726]  

## <a name="specifics-to-ibm-db2-for-luw-on-windows"></a>有关 Windows 上的 IBM DB2 for LUW 的具体信息
使用 Microsoft Azure，可以轻松地将运行于 IBM DB2 for Linux、UNIX 和 Windows (LUW) 的现有 SAP 应用程序迁移到 Azure 虚拟机。 借助 IBM DB2 for LUW 上的 SAP，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。
有关在 IBM DB2 for LUW 上运行 SAP Business Suite 的常规信息可在 SAP 社区网络 (SCN) 中找到，网址为 <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>。

有关 Azure 中 DB2 for LUW 上的 SAP 的其他信息和更新，请参阅 SAP 说明 [2233094]。 

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 for Linux、UNIX 和 Windows 版本支持
自 DB2 版本 10.5 起，支持 Microsoft Azure 虚拟机服务中 IBM DB2 for LUW 上的 SAP。

有关支持的 SAP 产品和 Azure VM 类型的信息，请参阅 SAP 说明 [1928533]。

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 IBM DB2 for Linux、UNIX 和 Windows 配置准则
#### <a name="storage-configuration"></a>存储配置
所有数据库文件都必须存储在基于直接附加磁盘的 NTFS 文件系统上。 这些磁盘装载到 Azure VM，基于 Azure 页 BLOB 存储 (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) 或托管磁盘 (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>)。 任何类型的网络驱动器或远程共享（例如以下 Azure 文件服务）都**不**支持数据库文件： 

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

如果使用基于 Azure 页 BLOB 存储或托管磁盘的磁盘，本文档的 [RDBMS 部署的结构][dbms-guide-2]一章中的表述也适用于利用 IBM DB2 for LUW 数据库进行的部署。 

如先前在文档通用部分中所述，磁盘的 IOPS 吞吐量存在配额。 确切的配额因所用 VM 类型而异。 可以在[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 找到 VM 类型及其配额的列表。

只要每个磁盘当前的 IOPS 配额够用，就可以将所有数据库文件存储在装载的单个磁盘上。 

有关性能注意事项的信息，另请参阅 SAP 安装指南中的“数据库目录的数据安全和性能注意事项”一章。

此外，也可以按本文档的[软件 RAID][dbms-guide-2.2] 一章中所述，使用 Windows 存储池（仅适用于 Windows Server 2012 和更高版本）或适用于 Windows 2008 R2 的 Windows 带区，基于多个磁盘来创建一个大型逻辑设备。
如果磁盘包含 sapdata 和 saptmp 目录的 DB2 存储路径，则必须将物理磁盘扇区的大小指定为 512 KB。 使用 Windows 存储池时，必须通过命令行界面使用参数 `-LogicalSectorSizeDefault`，以手动方式创建存储池。 有关详细信息，请参阅 <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>。

#### <a name="backuprestore"></a>备份/还原
支持通过 IBM DB2 for LUW 提供备份/还原功能，其方式与在标准 Windows Server 操作系统和 Hyper-V 上一样。

必须确保制定了有效的数据库备份策略。 

与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 使用多个目标目录来写入备份

#### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复
不支持 Microsoft 群集服务器 (MSCS)。

支持 DB2 高可用性灾难恢复 (HADR)。 如果 HA 配置的虚拟机具有有效的名称解析，Azure 中的设置将与任何本地设置无任何差别。 建议不要完全依赖于 IP 解析。

请勿将异地复制用于存储数据库磁盘的存储帐户。 有关详细信息，请参阅 [Microsoft Azure 存储][dbms-guide-2.3]和 [Azure VM 的高可用性和灾难恢复][dbms-guide-3]这两章。

#### <a name="other"></a>其他
Azure 可用性集或 SAP 监视等所有其他常规领域也适用于使用 IBM DB2 for LUW 进行的 VM 部署，如本文档的前三章所述。 

另请参阅[适用于 Azure 上的 SAP 的 SQL Server 总体摘要][dbms-guide-5.8]一章。

## <a name="specifics-to-ibm-db2-for-luw-on-linux"></a>有关 Linux 上的 IBM DB2 for LUW 的具体信息
使用 Microsoft Azure，可以轻松地将运行于 IBM DB2 for Linux、UNIX 和 Windows (LUW) 的现有 SAP 应用程序迁移到 Azure 虚拟机。 借助 IBM DB2 for LUW 上的 SAP，管理员和开发人员仍然可以使用在本地可用的相同开发和管理工具。 有关在 IBM DB2 for LUW 上运行 SAP Business Suite 的常规信息可在 SAP 社区网络 (SCN) 中找到，网址为 <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html>。

有关 Azure 中 DB2 for LUW 上的 SAP 的其他信息和更新，请参阅 SAP 说明 [2233094]。

### <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>IBM DB2 for Linux、UNIX 和 Windows 版本支持
自 DB2 版本 10.5 起，支持 Microsoft Azure 虚拟机服务中 IBM DB2 for LUW 上的 SAP。

有关支持的 SAP 产品和 Azure VM 类型的信息，请参阅 SAP 说明 [1928533]。

### <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 IBM DB2 for Linux、UNIX 和 Windows 配置准则
#### <a name="storage-configuration"></a>存储配置
所有数据库文件都必须存储在基于直接附加磁盘的文件系统上。 这些磁盘装载到 Azure VM，基于 Azure 页 BLOB 存储 (<https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>) 或托管磁盘 (<https://docs.microsoft.com/azure/storage/storage-managed-disks-overview>)。 任何类型的网络驱动器或远程共享（例如以下 Azure 文件服务）都**不**支持数据库文件：

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

如果使用基于 Azure 页 BLOB 存储的磁盘，本文档的 [RDBMS 部署的结构][dbms-guide-2]一章中的表述也适用于利用 IBM DB2 for LUW 数据库进行的部署。

如先前在文档通用部分中所述，磁盘的 IOPS 吞吐量存在配额。 确切的配额因所用 VM 类型而异。 可以在[此处 (Linux)][virtual-machines-sizes-linux] 和[此处 (Windows)][virtual-machines-sizes-windows] 找到 VM 类型及其配额的列表。

只要每个磁盘当前的 IOPS 配额够用，就可以将所有数据库文件存储在单个磁盘上。

有关性能注意事项，另请参阅 SAP 安装指南中的“数据库目录的数据安全和性能注意事项”一章。

此外，也可以按本文档的[软件 RAID][dbms-guide-2.2] 一章中所述，使用 LVM（逻辑卷管理器）或 MDADM 基于多个磁盘来创建一个大型逻辑设备。
如果磁盘包含 sapdata 和 saptmp 目录的 DB2 存储路径，则必须将物理磁盘扇区的大小指定为 512 KB。

#### <a name="backuprestore"></a>备份/还原
支持通过 IBM DB2 for LUW 提供备份/还原功能，其方式与在本地标准安装的 Linux 上一样。

必须确保制定了有效的数据库备份策略。

与裸机部署一样，备份/还原的性能取决于可以并行读取的卷数目，以及这些卷可能的吞吐量。 此外，备份压缩所使用的 CPU 使用率可能会在最多只有八个 CPU 线程的 VM 上扮演重要角色。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，读取的总体吞吐量就越小
* VM 中的 CPU 线程数目越少，备份压缩的影响就越大
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

要增加要写入的目标数目，可以使用/结合使用以下两个选项，具体视需求而定：

* 基于多个磁盘对备份目标卷划分带区，以改善该带区卷上的 IOPS 吞吐量
* 使用多个目标目录来写入备份

#### <a name="high-availability-and-disaster-recovery"></a>高可用性和灾难恢复
支持 DB2 高可用性灾难恢复 (HADR)。 如果 HA 配置的虚拟机具有有效的名称解析，Azure 中的设置将与任何本地设置无任何差别。 建议不要完全依赖于 IP 解析。

请勿将异地复制用于存储数据库磁盘的存储帐户。 有关详细信息，请参阅 [Microsoft Azure 存储][dbms-guide-2.3]和 [Azure VM 的高可用性和灾难恢复][dbms-guide-3]这两章。

#### <a name="other"></a>其他
Azure 可用性集或 SAP 监视等所有其他常规主题也适用于使用 IBM DB2 for LUW 进行的 VM 部署，如本文档的前三章所述。

另请参阅[适用于 Azure 上的 SAP 的 SQL Server 总体摘要][dbms-guide-5.8]一章。

