---
title: 适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署 | Microsoft Docs
description: 适用于 SAP 工作负荷的 SQL Server Azure 虚拟机 DBMS 部署
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
ms.date: 07/11/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db0d796a407c8e33501b0a312c78e8508f17297d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075088"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>适用于 SAP NetWeaver 的 SQL Server Azure 虚拟机 DBMS 部署

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

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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




本文档介绍在 Azure IaaS 中部署适用于 SAP 工作负荷的 SQL Server 时要考虑的多个不同领域。 在阅读本文档之前，应该已经阅读了[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)文档以及 [Azure 上的 SAP 工作负荷文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 



> [!IMPORTANT]
> 本文档的范围涵盖 SQL Server 上的 Windows 版本。 SAP 不支持带有任何 SAP 软件的 Linux 版 SQL Server。 本文档不讨论 Microsoft Azure Platform 的“平台即服务”产品 - Microsoft Azure SQL 数据库。 本文讨论的是如何运行 SQL Server 产品（已知适用于 Azure 虚拟机中的本地部署），以及如何运用 Azure 的“服务架构”功能。 这两种产品的数据库性能与功能差异很大，不应混用。 另请参阅：<https://azure.microsoft.com/services/sql-database/>
> 
>

通常应考虑使用最新的 SQL Server 版本在 Azure IaaS 中运行 SAP 工作负荷。 最新的 SQL Server 版本提供与一些 Azure 服务和功能的更好集成。 或者具有优化 Azure IaaS 基础结构中操作的更改。

在继续操作之前，我们建议查看[此文档][virtual-machines-sql-server-infrastructure-services]。

在下列章节中，将汇总并提及上述链接下的文档的某些部分。 另外，还将提及有关 SAP 的具体信息，并且更深入地说明一些概念。 不过，强烈建议先完整阅读上述文档，再阅读 SQL Server 特定文档。

在继续操作之前，应该先了解 IaaS 中 SQL Server 的一些特定信息：

* **SQL 版本支持**：对于 SAP 客户，Microsoft Azure 虚拟机上支持 SQL Server 2008 R2 和更高版本。 不支持更早版本。 有关更多详细信息，请查看此通用[支持声明](https://support.microsoft.com/kb/956893)。 Microsoft 通常也支持 SQL Server 2008。 不过，由于适用于 SAP 的重要功能是通过 SQL Server 2008 R2 引进的，因此，SQL Server 2008 R2 是适用于 SAP 的最低版本。 通常应考虑使用最新的 SQL Server 版本在 Azure IaaS 中运行 SAP 工作负荷。 最新的 SQL Server 版本提供与一些 Azure 服务和功能的更好集成。 或者具有优化 Azure IaaS 基础结构中操作的更改。 因此，本文仅限于 SQL Server 2016 和 SQL Server 2017。
* **SQL 性能**：相比其他公有云虚拟化产品，Microsoft Azure 托管的虚拟机将运行得非常顺利，但个别结果可能不同。 请参阅 [Azure 虚拟机中 SQL Server 的性能最佳做法](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)一文。
* 
  **使用来自 Azure 市场的映像**：部署新 Azure VM 的最快方式是使用来自 Microsoft Azure 市场的映像。 Azure 市场提供包含最新 SQL Server 版本的映像。 已经安装 SQL Server 的映像不能立即用于 SAP NetWeaver 应用程序。 原因是这些映像安装了默认的 SQL Server 排序规则，而不是 SAP NetWeaver 系统所需的排序规则。 若要使用此类映像，请查看[使用来自 Microsoft Azure 市场的 SQL Server 映像][dbms-guide-5.6]一章中所述的步骤。 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>适用于 SAP 相关 SQL Server 部署的 VM/VHD 结构建议
按照一般的说明，SQL Server 可执行文件应该位于或安装在 VM 的 OS 磁盘的系统驱动器（驱动器 C:\)）中。  通常情况下，SAP NetWeaver 工作负荷对大部分 SQL Server 系统数据库的使用率都不会很高。 因此，SQL Server 的系统数据库（master、msdb 和 model）也可以保留在驱动器 C:\ 上。 tempdb 应该是一个例外，对于 SAP 工作负载，它可能需要较高的数据卷或 I/O 操作卷。 I/O 工作负荷不应该应用于 OS VHD。 对于这类系统，应执行以下步骤：


* 除 A 系列 VM、tempdb 数据、日志文件外，所有 SAP 认证的 VM 类型（请参阅 SAP 说明 [1928533]）都可放置在非持久性驱动器 D:\ 上。 
* 不过，建议使用多个 tempdb 数据文件。 请注意，D:\ 驱动器卷因 VM 类型而异。 有关不同 VM 的驱动器 D:\ 精确大小的信息，请参阅 [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。

这些配置让 tempdb 耗用的空间比系统驱动器能够提供的还多。 非持久性驱动器 D:\ 还提供更大的 I/O 延迟和吞吐量（除 A 系列 VM 外）。 若要确定正确的 tempdb 大小，可以在现有系统上检查 tempdb 大小。 

>[!NOTE]
> 将 tempdb 数据文件和日志文件放入在驱动器 D:\ 上创建的文件夹时，需要确保 VM 重启后，该文件夹存在。 由于 VM 重启后新初始化了驱动器 D:\，所有文件和目录结构都已清除。[本文](http://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)中记录了在启动 SQL Server 服务之前，有可能在驱动器 D:\ 上重新创建最终目录结构。

运行包含 SAP 数据库的 SQL Server 且 tempdb 数据和 tempdb 日志文件放置于 D:\ 驱动器的 VM 配置应如下所示：

![SQL Server 的示例 VM 磁盘配置的图示](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

上图显示一个简单事例。 正如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)一文中所提到的一样，高级存储磁盘的数量和大小取决于不同的因素。 但是通常我们建议：

- 使用存储空间形成一个卷或少量卷，其中包含 SQL Server 数据文件。 此配置背后的原因是，在现实生活中，许多 SAP 数据库包含不同大小的数据库文件，而这些文件具有不同的 I/O 工作负荷。
- 使用存储空间提供足够的 IOPS 并为 SQL Server 事务日志文件提供存储空间。 潜在的 IOPS 工作负荷通常是调整事务日志卷大小的准绳，而不是 SQL Server 事务卷的潜在卷
- 只要性能足够好，就可以为 tempdb 使用驱动器 D:\。 如果由于 tmepdb 位于驱动器 D:\ 上，整体工作负荷受到限制，可能需要考虑按照[本文](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)中的建议，将 tempdb 移动到单独的高级存储磁盘。


### <a name="special-for-m-series-vms"></a>专用于 M 系列 VM
对于 Azure M 系列 VM，使用 Azure 写入加速器时，与 Azure 高级存储性能相比，可通过多种因素减少写入事务日志的延迟。 因此，应该为形成 SQL Server 事务日志卷的 VHD 部署 Azure 写入加速器。 有关详细信息，请阅读文档[写入加速器](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)。
  

### <a name="formatting-the-disks"></a>格式化磁盘
对于 SQL Server，包含 SQL Server 数据和日志文件的磁盘的 NTFS 块大小应该为 64KB。 不需要将 D:\ 驱动器格式化。 此驱动器已预先格式化。

要确保还原或创建数据库时不会通过清空文件内容来初始化数据文件，应该确保 SQL Server 服务运行所在的用户上下文具有特定的权限。 通常，Windows 管理员组中的用户拥有这些权限。 如果 SQL Server 服务在非 Windows 管理员用户的用户上下文中运行，需要为该用户分配“执行卷维护任务”用户权限。  请参阅这篇 Microsoft 知识库文章中的详细信息：<https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>数据库压缩的影响
在 I/O 带宽可能变成限制因素的配置中，每个减少 IOPS 的度量值都可能有助于分散可以在 Azure 等 IaaS 方案中运行的工作负荷。 因此，SAP 和 Microsoft 建议先应用 SQL Server 页面压缩，然后再将现有 SAP 数据库上传到 Azure（如果尚未这么做）。

之所以建议先执行数据库压缩，再上载到 Azure，原因有两点：

* 减少要上传的数据量。
* 假设可以在本地使用功能更强大的硬件（具有更多 CPU、更高 I/O 带宽或更少 I/O 延迟），则执行压缩的持续时间较短。
* 较小的数据库大小可以降低磁盘分配的成本

数据库压缩也可以在 Azure 虚拟机中运行，如同在本地运行一样。 有关如何压缩现有 SAP NetWeaver SQL Server 数据库的详细信息，请参阅[改进的 SAP 压缩工具 MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/)一文。 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 及更新版本 - 将数据库文件直接存储在 Azure Blob 存储上
SQL Server 2014 及更新版本提供了一种可能性：将数据库文件直接存储在 Azure Blob 存储上，而不需要用 VHD 来“包装”。 特别是在使用标准 Azure 存储或较小的 VM 类型时，此部署类型可以克服 IOPS 的限制，这些限制是通过可以装载到某些较小 VM 类型的有限磁盘数目来强制实施的。 此部署方式适用于 SQL Server 的用户数据库，但不适用于系统数据库。 这也适用于 SQL Server 的数据和日志文件。 如果想使用此方式部署 SAP SQL Server 数据库，而不是将它“包装”到 VHD，请记住：

* 所用存储帐户所在的 Azure 区域必须与部署运行 SQL Server 的 VM 时所用的存储帐户相同。
* 之前列出的有关将 VHD 分布到不同 Azure 存储帐户的注意事项也适用于这种部署方法。 意味着 I/O 操作计数会以 Azure 存储帐户的限制为依据。
* 代表 SQL Server 数据和日志文件的存储 Blob 的流量将计入特定 VM 类型的 VM 的网络带宽，而不是计入 VM 的存储 I/O 配额。 有关特定 VM 类型的网络带宽的信息，请参考 [Azure 中 Windows 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)一文。
* Azure 高级存储针对不同磁盘大小的 IOPS 和 I/O 吞吐量性能目标不再适用。 即使创建的 Blob 位于 Azure 高级存储上。 目标记录于 [VM 的高性能高级存储与托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage#scalability-and-performance-targets)一文中。 由于直接将 SQL Server 数据文件和日志文件至于存储在 Azure 高级存储上的 Blob，因此与 Azure 高级存储上的 VHD 相比，性能特征可能会有所不同。
* 在 M 系列 VM 上，Azure 写入加速器不能用于支持针对 SQL Server 事务日志文件的亚毫秒写入。 

有关此功能的详细信息，请参阅 [Microsoft Azure 中的 SQL Server 数据文件](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)一文

对于生产系统的建议是避免此配置，而是选择将 SQL Server 数据和日志文件放置在 Azure 高级存储 VHD 中，而不是直接放在 Azure Blob 上。


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014 缓冲池扩展
SQL Server 2014 引入了一项称为[缓冲池扩展](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017)的新功能。 此功能使用由服务器或 VM 的本地 SSD 支持的第二层缓存，来扩展保留于内存中的 SQL Server 缓冲池。 缓冲池扩展可“在内存中”保留更大的数据工作集。 相较于访问 Azure 标准存储，访问存储在 Azure VM 的本地 SSD 上的缓冲池扩展的速度要快得多。 根据针对 SQL Server 数据文件的建议，将缓冲池扩展与 Azure 高级存储读取缓存进行比较，缓冲池扩展不具有明显的优势。 原因在于这两个缓存（SQL Server 缓冲池扩展和高级存储读取缓存）都使用 Azure 计算节点的本地磁盘。

在使用 SQL Server 缓冲池扩展和 SAP 工作负荷的同时所获得的经验参差不齐，仍不能够对是否在所有情况下使用它提出明确建议。 理想情况下，SAP 应用程序所需的工作集适合于主内存。 Azure 同时提供内存高达 4 TB 的 VM，应该可以将工作集保留在内存中。 因此，缓冲池扩展的使用仅限于极少情况，并且不应成为主流事例。  

## <a name="backuprecovery-considerations-for-sql-server"></a>SQL Server 的备份/恢复注意事项
将 SQL Server 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SQL Server 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在补救存储崩溃方面，备份现在已变得不太重要。 维护适当备份和恢复计划的首要原因是，可以通过提供时间点恢复功能来补救逻辑/人为错误。 因此，其目标是使用备份将数据库还原回到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 

若要查看 Azure 中的不同 SQL Server 备份可能性，请阅读 [Azure 虚拟机中 SQL Server 的备份和还原](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery)一文。 本文介绍了多种不同的可能性。

### <a name="manual-backups"></a>手动备份
可以通过以下方式执行“手动”备份：

1. 在直接连接的 Azure 磁盘上执行常规 SQL Server 备份。 此方法的优点是，可以快速获得备份以进行系统刷新，并构建新系统作为现有 SAP 系统的副本
2.  SQL Server 2012 CU4 和更高版本可以将数据库备份到 Azure 存储 URL。
3.  Azure Blob 存储中数据库文件的文件快照备份。 此方法仅在 SQL Server 数据和日志文件位于 Azure Blob 存储上时适用

第一种方法众所周知，且在许多情况下也适用于本地环境。 不过，需要解决长期的备份位置的问题。 由于不希望备份在本地连接的 Azure 存储中保留 30 天或以上，需要使用 Azure 备份服务或其他第三方备份/恢复工具，此工具包含对备份的访问权限和保留期管理。 或者使用 Windows 存储空间在 Azure 中构建大型文件服务器。

[SQL Server 备份到 URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017) 一文更详细介绍了第二种方法。 不同版本的 SQL Server 在此功能上有一些变化。 因此，应查看文档以检查特定的 SQL Server 版本。 请务必注意，本文列出了许多限制。 可以根据以下条件执行备份：

- 单个 Azure 页 blob，这将备份大小限制为 1000 GB。 这也限制了可以实现的吞吐量。
- 多个（最多 64 个） Azure 块 blob，这将使理论备份大小为 12 TB。 但是，对客户数据库的测试显示，最大备份大小可能小于其理论限制大小。 在此情况下，用户需要负责管理备份的保留期以及对备份的访问。


### <a name="automated-backup-for-sql-server"></a>SQL Server 自动备份
自动备份为 Windows Azure VM 中运行的 SQL Server Standard 和 Enterprise 版本提供自动备份服务。 此服务由 [SQL Server IaaS 代理扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)提供。该扩展已自动安装在 Azure 门户中的 SQL Server Windows 虚拟机映像上。 如果在安装了 SQL Server 情况下部署自己的 OS 映像，需要单独安装 VM 扩展。 本[文章](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)介绍了必要的步骤。

有关此方法的功能的详细信息，请参阅以下文章：

- SQL Server 2014：[QL Server 2014 虚拟机 (Resource Manager) 的自动备份](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017：[用于 Azure 虚拟机（资源管理器）的自动备份 v2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

查看文档，可以看到最新 SQL Server 版本的功能有所改进。 [Microsoft Azure 的 SQL Server 托管备份](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017)一文中发布了有关 SQL Server 自动备份的详细信息。 理论备份大小限制为 12 TB。  对于高达 12 TB 的备份大小，自动备份是一种很好的方法。 由于多个 Blob 并行写入，因此预期吞吐量可大于 100 MB/秒。 
 

### <a name="azure-backup-for-sql-server-vms"></a>SQL Server VM 的 Azure 备份
此 SQL Server 备份新方法由 Azure 备份服务于 2018 年 6 月作为公共预览版提供。 备份 SQL Server 的方法与其他第三方使用的方法相同，即 SQL Server VSS/VDI 接口将备份流式传输到目标位置。 在此情况下，目标位置是 Azure 恢复服务保管库。

[此处](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)提供对此备份方法的详细描述，该方法增加了集中备份配置、监视和管理的诸多优势。 


### <a name="third-party-backup-solutions"></a>第三方备份解决方案
对于 Azure 上运行的 SAP 布局部分，相当多的 SAP 客户都不可能重新开始并引入全新备份解决方案。 因此，需要使用现有备份解决方案并将其扩展到 Azure。 将现有备份解决方案扩展到 Azure 时，通常能与此领域的大多数主要供应商良好协作。 


## 
  <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8">
  </a>使用来自 Microsoft Azure 市场的 SQL Server 映像
Microsoft 在 Azure 市场中提供已经包含 SQL Server 版本的 VM。 对于需要 SQL Server 和 Windows 许可证的 SAP 客户，通过运行已安装 SQL Server 的 VM，使用这些映像可能满足对许可证的需求。 若要针对 SAP 使用此类映像，必须注意以下事项：

* SQL Server 非评估版的购置成本高于从 Azure 市场部署的“仅限 Windows”VM。 请参阅以下文章来比较价格：<https://azure.microsoft.com/pricing/details/virtual-machines/windows/> 和 <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>。 
* 只能使用 SAP 支持的 SQL Server 版本。
* 安装于 VM（来自 Azure 市场）中的 SQL Server 实例的排序规则并不是 SAP NetWeaver 要求 SQL Server 实例运行的排序规则。 不过，可以遵循下一节的指示来更改排序规则。

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>更改 Microsoft Windows/SQL Server VM 的 SQL Server 排序规则
因为 Azure 市场中的 SQL Server 映像未设置为使用 SAP NetWeaver 应用程序所需的排序规则，所以需要在部署之后立即更改。 对于 SQL Server，一旦部署了 VM 且管理员能够登录已部署的 VM，就可以使用下列步骤来完成排序规则的更改：

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

如果结果不同，请停止部署 SAP，并调查为什么安装命令未按预期运行。 **不**支持将 SAP NetWeaver 应用程序部署到 SQL Server 代码页与上述代码页不同的 SQL Server 实例。

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Azure 中适用于 SAP 的 SQL Server 高可用性
在针对 SAP 的 Azure IaaS 部署中使用 SQL Server，可增加几种不同的可能性来部署高可用性的 DBMS 层。 如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)中所述，Azure 为部署在 Azure 可用性集中的单个 VM 和一对 VM 提供不同的正常运行时间 SLA。 假设条件是，对于需要在 Azure 可用性集中进行部署的生产部署，努力实现正常运行时间 SLA。 在此情况下，需要在此可用性集中部署至少两个 VM。 一个 VM 将运行主动 SQL Server 实例。 另一个 VM 将运行被动实例

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>使用 Windows 横向扩展文件服务器的 SQL Server 群集
借助 Windows Server 2016，Microsoft 引入了[存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)。 根据存储空间直通部署，支持 SQL Server FCI 群集。 [在 Azure 虚拟机上配置 SQL Server 故障转移群集实例](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)一文中可查看详细信息。 该解决方案还需要 Azure 负载均衡器来处理群集资源的虚拟 IP 地址。 SQL Server 数据库文件存储于存储空间中。 因此，需要构建基于 Azure 高级存储的 Windows 存储空间。 由于此解决方案的支持时间不长，因此没有已知的 SAP 客户在 SAP 生产方案中使用此解决方案。  

### <a name="sql-server-log-shipping"></a>SQL Server 日志传送
高可用性 (HA) 的方法之一是 SQL Server 日志传送。 如果参与 HA 配置的 VM 具有有效的名称解析，就不会出现问题，而且 Azure 中的设置与任何本地设置无任何差别。 有关设置日志传送的信息和日志传送的原理。 有关 SQL Server 日志传送的详细信息，请参阅[关于日志传送 (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017) 一文。

几乎不在 Azure 中使用 SQL Server 日志传送功能来实现一个 Azure 区域内的高可用性。 但是，在以下场景中，SAP 客户成功将日志传送与 Azure 结合使用：

- 从一个 Azure 区域到另一个 Azure 区域的灾难恢复场景
- 从本地到 Azure 区域的灾难恢复配置
- 从本地到 Azure 切换的场景。 在这些情况下，日志传送用于将 Azure 中的新 DBMS 部署与本地正在进行的生产系统同步。 切换时，生产被关闭并确保已将上次和最新的事务日志备份传输到 Azure DBMS 部署。 然后打开 Azure DBMS 部署用于生产。  



### <a name="database-mirroring"></a>数据库镜像
SAP 支持的数据库镜像（请参阅 SAP 说明 [965908]）依赖于在 SAP 连接字符串中定义故障转移伙伴。 在跨界方案中，我们假设这两个 VM 位于相同的域中，而且这两个 SQL Server 实例运行所在的用户上下文也是域用户，并在所涉及的这两个 SQL Server 实例中具备足够的特权。 因此，Azure 中的数据库镜像设置与典型的本地设置/配置并无差别。

至于仅限云的部署，最简单的方法是在 Azure 中设置另一个域，以便让这些 DBMS VM（最好是专用的 SAP VM）位于一个域中。

如果域不可行，也可以对数据库镜像终结点使用证书，如此处所述：<https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

有关如何在 Azure 中设置数据库镜像的教程，请参阅：<https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
由于 AlwaysOn 受本地 SAP 支持（请参阅 SAP 说明 [1772688]），因此，支持在 Azure 中将其与 SAP 搭配。 部署 SQL Server 可用性组侦听程序（请勿与 Azure 可用性集混淆）时有一些特殊注意事项，因为 Azure 目前不允许创建 AD/DNS 对象，而在本地是允许的。 因此，需要执行一些不同的安装步骤来克服 Azure 的特定行为。

以下是使用可用性组侦听器的一些注意事项：

* 只有将 Windows Server 2012 或更高版本作为 VM 的来宾 OS 时，才支持使用可用性组侦听器。 对于 Windows Server 2012，必须确保应用此补丁：<https://support.microsoft.com/kb/2854082> 
* 对于 Windows Server 2008 R2，此补丁并不存在，而 Always On 需要通过与数据库镜像相同的方式来使用，即，在连接字符串中指定故障转移伙伴（通过 SAP default.pfl 参数 dbs/mss/server 完成 - 请参阅 SAP 说明 [965908]）。
* 使用可用性组侦听器时，数据库 VM 需要连接到专用的负载均衡器。 为了避免 Azure 在这两个 VM 都意外关闭的情况下分配新的 IP 地址，应该在 Always On 配置中为这些 VM 的网络接口分配静态 IP 地址（有关如何定义静态 IP 地址的信息，请参阅[此文][virtual-networks-reserved-private-ip]）
* 构建群集需要分配特殊 IP 地址的 WSFC 群集配置时，需执行一些特殊的步骤，因为 Azure 及其当前功能会为群集名称分配与创建群集所在的节点相同的 IP 地址。 这表示必须执行手动步骤，为群集分配不同的 IP 地址。
* 可用性组侦听器将创建于具有 TCP/IP 终结点的 Azure 中，这些终结点会分配给运行可用性组主要和次要副本的 VM。
* 可能需要使用 ACL 来保护这些终结点。

有关在 Azure VM 中使用 SQL Server 部署 Always On 的详细文档列表如下：

- [介绍 Azure 虚拟机上的 SQL Server Always On 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。
- [在位于不同区域的 Azure 虚拟机上配置 Always On 可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)。
- [在 Azure 中为 Always On 可用性组配置负载均衡器](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)。

>[!NOTE]
> 如果要为可用性组侦听程序的虚拟 IP 地址配置 Azure 负载均衡器，请确保已配置 DirectServerReturn。 配置此选项将减少 SAP 应用层和 DBMS 层之间的网络往返延迟。 

SQL Server Always On 是 Azure 中用于 SAP 工作负荷部署的最常用高可用性和灾难恢复功能。 大多数客户在单个 Azure 区域内使用 Always On 实现高可用性。 如果部署仅限于两个节点，则有两种连接选择：

- 使用可用性组侦听程序。 若使用可用性组侦听程序，需要部署 Azure 负载均衡器。 这通常是部署的默认方法。 SAP 应用程序将配置为针对可用性组侦听程序而不是针对单个节点进行连接
- 使用 SQL Server 数据库镜像的连接参数。 在此情况下，需要以命名两个节点名称的方式配置 SAP 应用程序的连接。 有关此类 SAP 端 配置的确切详细信息，请参阅 SAP 说明 [#965908](https://launchpad.support.sap.com/#/notes/965908)。 通过使用此选项，无需配置可用性组侦听程序。 并且不需要任何 SQL Server 高可用性的 Azure 负载均衡器。 由于 SQL Server 实例的传入流量不通过Azure 负载均衡器路由，因此 SAP 应用层和 DBMS 层之间的网络延迟较低。 但请记住，此选项仅在将可用性组限制为跨两个实例时适用。 

不少客户正在利用 SQL Server Always On 功能在 Azure 区域之间实现其他灾难恢复功能。 少数客户还使用此功能从次要副本执行备份。 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server 透明数据加密
有许多客户在 Azure 中部署 SAP SQL Server 数据库时，使用 SQL Server [透明数据加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)。 SAP 完全支持 SQL Server TDE 功能（请参阅 SAP 说明 [#1380493](https://launchpad.support.sap.com/#/notes/1380493)）。 

### <a name="applying-sql-server-tde"></a>应用 SQL Server TDE
从另一个本地运行的 DBMS 执行异类迁移到 Azure 中运行的 Windows/SQL Server 时，应提前在 SQL Server 中创建空的目标数据库。 接下来将应用 SQL Server TDE 功能。 同时仍在本地运行生产系统。 希望在此序列中执行的原因是，加密空数据库的进程可能需要相当长一段时间。 然后 SAP 导入进程将在停机阶段将数据导入加密数据库。 与在停机阶段的导出阶段之后加密数据库的开销相比，导入到加密数据库的开销的时间影响更小。 尝试在数据库上运行 SAP 工作负荷情况下应用 TDE 时，会产生负面体验。 因此，建议将 TDE 部署视为需要在特定数据库上没有 SAP 工作负载的情况下完成的活动。

将 SAP SQL Server 数据库从本地移动到 Azure 时，我们建议测试在其上可最快应用加密的基础结构。 对此，请记住这些事实：

- 不能定义用于将数据加密应用于数据库的线程数。 线程数主要取决于 SQL Server 数据和日志文件分布的磁盘卷数。 意味着卷（驱动器号）越不同，将并行执行加密的线程越多。 此类配置与之前的磁盘配置建议有点矛盾，该建议是在 Azure VM 中为 SQL Server 数据库文件构建一个或少量存储空间。 具有少量卷的配置将导致执行加密的线程数较少。 单个线程加密正在读取 64KB 的盘区、对其进行加密，然后将记录写入事务日志文件，同时告知已加密盘区。 因此，事务日志上的负载适中。
- 在较旧的 SQL Server 版本中，加密 SQL Server 数据库时，备份压缩不再有效。 当计划在本地加密 SQL Server 数据库，然后将备份复制到 Azure 以还原 Azure 中的数据库时，这种行为可能会发展成一个问题。 SQL Server 备份压缩通常可实现因子为 4 的压缩比。
- 借助 SQL Server 2016，SQL Server 引入了新功能，也允许以有效的方式压缩加密的数据库。 请参阅[此博客](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/)了解详细信息。
 
将 TDE 加密的应用程序仅与极少的 SAP 工作负荷一起处理时，应测试在特定配置中测试，以确定在本地将 TDE 应用于 SAP 数据库更好还是在 Azure 中应用更好。 在 Azure 中，在过度预配基础结构方面，确实具有更大的灵活性，并且在应用 TDE 后可收缩基础结构。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault
Azure 提供 [Key Vault](https://azure.microsoft.com/services/key-vault/) 服务以存储加密密钥。 另一方面，SQL Server 提供了一个连接器，可将 Azure Key Vault 用作 TDE 证书的存储。

有关将 Azure Key Vault 用于 SQL Server TDE 的详细信息列表如下：

- [使用 Azure Key Vault (SQL Server) 的可扩展密钥管理](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017)。
- [使用 Azure Key Vault 的 SQL Server TDE 可扩展密钥管理 - 设置步骤](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017)。
- [SQL Server 连接器维护和故障排除](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017)。
- [客户关于 SQL Server 透明数据加密 - TDE + Azure Key Vault 的更多问题](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/)。


>[!IMPORTANT]
>使用 SQL Server TDE，尤其是与 Azure Key Vault 搭配使用时，建议使用 SQL Server 2014、SQL Server 2016 和 SQL Server 2017 的最新修补程序。 因为根据客户反馈，优化和修复已应用于代码。 有关示例，请查看 [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm)。
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>适用于 Azure 上的 SAP 的 SQL Server 总体摘要
本指南提供了许多建议，因此，建议在规划 Azure 部署之前，反复阅读本指南。 但是，一般而言，请务必遵循最通用的 Azure 特定 DBMS 建议：

1. 使用最新的 DBMS 版本（例如 SQL Server 2017），其在 Azure 中最具优势。 
2. 在 Azure 中仔细规划 SAP 系统布局，以平衡数据文件布局和 Azure 限制：
   * 不要有太多磁盘，但必须足以确保可以达到所需的 IOPS。
   * 如果不使用托管磁盘，请记住，不仅每个 Azure 存储帐户的 IOPS 受到限制，每个 Azure 订阅的存储帐户数目也受到限制（[更多详细信息][azure-subscription-service-limits]）。 
   * 只有在需要达到更高的吞吐量时，才在磁盘上划分带区。
3. 永远不要在 D:\ 驱动器上安装软件或放置任何需要永久保留的文件，因为它不是永久性的，此驱动器上的所有内容都会在 Windows 重新启动时丢失。
4. 不要对 Azure 标准存储使用磁盘缓存。
5. 不要使用 Azure 异地复制的 Azure 标准存储帐户。  对 DBMS 工作负荷使用本地冗余。
6. 使用 DBMS 供应商的 HA/DR 解决方案来复制数据库数据。
7. 始终使用名称解析，不要依赖 IP 地址。
8. 使用 SQL Server TDE 时，应用最新的 SQL Server 修补程序。
9. 尽可能使用最高强度的数据库压缩。 对 SQL Server 而言，即页面压缩。
10. 谨慎使用来自 Azure 市场的 SQL Server 映像。 如果使用 SQL Server 映像，就必须更改实例排序规则，才能在其上安装任何 SAP NetWeaver 系统。
11. 按照[部署指南][deployment-guide]中的说明，安装和配置适用于 Azure 的 SAP 主机监视。