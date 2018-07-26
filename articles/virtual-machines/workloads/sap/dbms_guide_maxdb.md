---
title: Azure VM 上的 SAP MaxDB、liveCache 和内容服务器部署 | Microsoft Docs
description: Azure 上的 SAP MaxDB、liveCache 和内容服务器部署
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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 408d43f07179f9f18c05f22fdd4ea36a3a90cb49
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075099"
---
# <a name="sap-maxdb-livecache-and-content-server-deployment-on-azure-vms"></a>Azure VM 上的 SAP MaxDB、liveCache 和内容服务器部署

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
[dbms-guide-figure-900]:media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png

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




本文档介绍在 Azure IaaS 中部署 MaxDB、liveCache 和内容服务器时要考虑的多个不同领域。 在阅读本文档之前，应该已经阅读了[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署注意事项](dbms_guide_general.md)文档以及 [Azure 上的 SAP 工作负荷文档](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)中的其他指南。 

## <a name="specifics-for-the-sap-maxdb-deployments-on-windows"></a>有关 Windows 上的 SAP MaxDB 部署的具体信息
### <a name="sap-maxdb-version-support-on-azure"></a>Azure 上的 SAP MaxDB 版本支持
SAP 目前支持 SAP MaxDB 版本 7.9 或更高版本，该版本可以与 Azure 中基于 SAP NetWeaver 的产品搭配使用。 适用于 SAP MaxDB 服务器的所有更新或者适用于可与基于 SAP NetWeaver 的产品搭配使用的 JDBC 和 ODBC 驱动程序的所有更新，仅通过 SAP Service Marketplace (<https://support.sap.com/swdc>) 提供。
有关在 SAP MaxDB 上运行 SAP NetWeaver 的常规信息可在 <https://www.sap.com/community/topic/maxdb.html> 中找到。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-maxdb-dbms"></a>SAP MaxDB DBMS 支持的 Microsoft Windows 版本和 Azure VM 类型
若要为 Azure 上的 SAP MaxDB DBMS 查找受支持的 Microsoft Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的操作系统 Microsoft Windows，也就是 Microsoft Windows 2016。

### <a name="available-sap-maxdb-documentation-for-maxdb"></a>可用的 MaxDB 的 SAP MaxDB 文档
可以在 SAP 说明 [767598] 中找到更新的 SAP MaxDB 文档列表

### <a name="sap-maxdb-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP MaxDB 配置准则
#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>存储配置
适用于 SAP MaxDB 的 Azure 存储最佳做法遵循 [RDBMS 部署的结构][dbms-guide-2]一章中所述的常规建议。

> [!IMPORTANT]
> 与其他数据库一样，SAP MaxDB 也有数据和日志文件。 不过，在 SAP MaxDB 术语中，正确的词汇是“卷”（不是“文件”）。 例如，有 SAP MaxDB 数据卷和日志卷。 请勿与 OS 磁盘卷混淆。 
> 
> 

简而言之，必须：

* 如果使用 Azure 存储帐户，请将保存 SAP MaxDB 数据和日志卷（数据和日志文件）的 Azure 存储帐户设置为本地冗余存储 (LRS)，如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中所述。
* 将 SAP MaxDB 数据卷（数据文件）的 IO 路径与日志卷（日志文件）的 IO 路径隔开。 这表示 SAP MaxDB 数据卷（数据文件）必须安装在一个逻辑驱动器上，而 SAP MaxDB 日志卷（日志文件）必须安装在另一个逻辑驱动器上。
* 根据[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中所述，为每个磁盘设置适当的缓存类型，具体取决于是将其用于 SAP MaxDB 数据卷还是日志卷（数据和日志文件），以及是使用 Azure 标准存储还是 Azure 高级存储。
* 只要每个磁盘当前的 IOPS 配额能满足需求，就可以将所有数据卷存储在装载的单个磁盘上，同时将所有数据库日志卷存储在装载的另一个磁盘上。
* 如果需要更多 IOPS 和/或空间，建议使用 Microsoft Windows 存储池（仅适用于 Microsoft Windows Server 2012 和更高版本），基于已装载的多个磁盘来创建一个大型逻辑设备。 有关详细信息，另请参阅[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)。 这种方法可以简化磁盘空间的管理开销，避免跨已装载的多个磁盘手动分发文件。
* 强烈建议使用适用于 MaxDB 部署的 Azure 高级存储。 

![适用于 SAP MaxDB DBMS 的 Azure IaaS VM 的参考配置](./media/dbms_maxdb_deployment_guide/Simple_disk_structure_maxdb.PNG)


#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>备份和还原
将 SAP MaxDB 部署到 Azure 时，必须检查备份方法。 即使系统不是生产系统，也必须定期备份 SAP MaxDB 托管的 SAP 数据库。 由于 Azure 存储会保留三个映像，因此，在保护系统以免发生存储故障以及更严重的操作或管理故障方面，备份现在已变得不太重要。 维护适当备份和还原计划的主要原因是，可以通过提供时间点恢复功能来补救逻辑或人为错误。 因此，其目标是使用备份将数据库还原到某个时间点，或者通过复制现有数据库，在 Azure 中使用备份来植入另一个系统。 

在 Azure 中备份和还原数据库的方式与在本地系统中一样，因此，可以使用标准的 SAP MaxDB 备份/还原工具，SAP 说明 [767598] 中列出的其中一个 SAP MaxDB 文档对这些工具进行了说明。 

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>备份和还原的性能注意事项
与裸机部署一样，备份和还原的性能取决于可以并行读取的卷数目，以及这些卷的吞吐量。 因此，可以假设：

* 存储数据库设备所用的磁盘数目越少，总体读取吞吐量就越低
* 要写入备份的目标（带区目录、磁盘）越少，吞吐量就越低

要增加要写入的目标数目，可以使用或者结合使用以下两个选项，具体视需求而定：

* 将单独的卷专用于备份
* 基于已装载的多个磁盘对备份目标卷划分带区，以改善该带区磁盘卷上的 IOPS 吞吐量
* 为下列各项配备单独的专用逻辑磁盘设备：
  * SAP MaxDB 备份卷（即文件）
  * SAP MaxDB 数据卷（即文件）
  * SAP MaxDB 日志卷（即文件）

基于已装载的多个磁盘对卷划分带区，这之前已在[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)中进行了讨论。 

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>其他注意事项
如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)所述，Azure 可用性集或 SAP 监视等所有其他常规领域也适用。  使用 SAP MaxDB 数据库进行 VM 部署。
其他特定于 SAP MaxDB 的设置对 Azure VM 是透明的，相关介绍请参阅 SAP 说明 [767598] 以及下列 SAP 说明中列出的不同文档：

* [826037] 
* [1139904]
* [1173395]

## <a name="specifics-for-sap-livecache-deployments-on-windows"></a>有关 Windows 上的 SAP liveCache 部署的具体信息
### <a name="sap-livecache-version-support"></a>SAP liveCache 版本支持
Azure 虚拟机支持的 SAP liveCache 最低版本是针对 EhP 2 for SAP SCM 7.0 和更新版本发行的 SAP LC/LCAPPS 10.0 SP 25，其中包括 liveCache 7.9.08.31 和 LCA-Build 25。

### <a name="supported-microsoft-windows-versions-and-azure-vm-types-for-sap-livecache-dbms"></a>SAP liveCache DBMS 支持的 Microsoft Windows 版本和 Azure VM 类型
若要为 Azure 上的 SAP liveCache DBMS 查找受支持的 Microsoft Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的操作系统 Microsoft Windows Server。 

### <a name="sap-livecache-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP liveCache 配置准则
#### <a name="recommended-azure-vm-types-for-livecache"></a>建议的适用于 liveCache 的 Azure VM 类型
SAP liveCache 是一种执行大量计算的应用程序，因此，RAM 与 CPU 的数量和速度会对 SAP liveCache 性能产生重大影响。 

对于 SAP 支持的 Azure VM 类型（SAP 说明 [1928533]），分配给 VM 的所有虚拟 CPU 资源均由虚拟机监控程序的专用物理 CPU 资源提供支持。 不会发生过度预配（因此也不会发生 CPU 资源竞争）。

同样地，对于 SAP 支持的所有 Azure VM 实例类型，VM 内存会全部映射到物理内存 - 例如，不会使用过度预配（过载）。

从这个角度来看，强烈建议使用最新的 Dv2、Dv3、Ev3 和 M 系列 VM。 不同 VM 类型的选择取决于所需的 liveCache 和 CPU 资源需要的内存。 与所有其他 DBMS 部署一样，建议将 Azure 高级存储用于性能关键卷。

#### <a name="storage-configuration-for-livecache-in-azure"></a>Azure 中 liveCache 的存储配置
SAP liveCache 以 SAP MaxDB 技术为基础，因此，本文档中针对 SAP MaxDB 提出的所有 Azure 存储最佳做法建议也适用于 SAP liveCache。 

#### <a name="dedicated-azure-vm-for-livecache-scenario"></a>liveCache 方案专用的 Azure VM
SAP liveCache 会密集使用计算能力，因此，为了有效使用该技术，强烈建议将其部署在专用的 Azure 虚拟机上。 

![为有效使用 liveCache 而专用的 Azure VM](./media/dbms_maxdb_deployment_guide/700-livecach-prod.PNG)


#### <a name="backup-and-restore-for-livecache-in-azure"></a>在 Azure 中备份和还原 liveCache
备份和还原（包括性能注意事项）已在本文档相关的 SAP MaxDB 章节中加以说明。 

#### <a name="other-considerations"></a>其他注意事项
所有其他常规领域已在与 SAP MaxDB 相关的章节中加以说明。 

## <a name="specifics-for-the-sap-content-server-deployment-on-windows-in-azure"></a>有关 Azure 中 Windows 上的 SAP 内容服务器部署的具体信息
SAP 内容服务器是基于服务器的独立组件，可以存储电子文档等不同格式的内容。 SAP 内容服务器通过技术开发提供，旨在以跨应用程序的方式用于任何 SAP 应用程序。 它安装于一个独立的系统上。 其存储的典型内容包括来自 Knowledge Warehouse 的培训材料和文档，或者源自 mySAP PLM 文档管理系统的技术绘图。 

### <a name="sap-content-server-version-support-for-azure-vms"></a>Azure VM 的 SAP 内容服务器版本支持
SAP 目前支持：

* **SAP 内容服务器**版本 **6.50（和更高版本）**
* **SAP MaxDB 版本 7.9**
* **Microsoft IIS (Internet Information Server) 版本 8.0（和更高版本）**

强烈建议使用最新版的 SAP 内容服务器以及最新版的 Microsoft IIS。 

在 [SAP 产品可用性对照表 (PAM)][sap-pam] 中查看支持的最新 SAP 内容服务器和 Microsoft IIS 版本。

### <a name="supported-microsoft-windows-and-azure-vm-types-for-sap-content-server"></a>SAP 内容服务器支持的 Microsoft Windows 和 Azure VM 类型
若要为 Azure 上的 SAP 内容服务器查找受支持的 Windows 版本，请参阅：

* [SAP 产品可用性对照表 (PAM)][sap-pam]
* SAP 说明 [1928533]

强烈建议使用最新版本的 Microsoft Windows Server。

### <a name="sap-content-server-configuration-guidelines-for-sap-installations-in-azure-vms"></a>在 Azure VM 中安装 SAP 的 SAP 内容服务器配置准则
#### <a name="storage-configuration-foir-content-server-in-azure"></a>Azure 中内容服务器的存储配置
如果将 SAP 内容服务器配置为将文件存储在 SAP MaxDB 数据库中，那么，本文档中针对 SAP MaxDB 提出的所有 Azure 存储最佳做法建议也适用于 SAP 内容服务器方案。 

如果将 SAP 内容服务器配置为将文件存储在文件系统中，则建议使用专用的逻辑驱动器。 如[适用于 SAP 工作负荷的 Azure 虚拟机 DBMS 部署的注意事项](dbms_guide_general.md)所述，通过使用 Windows 存储空间，还可以增加逻辑磁盘的大小和 IOPS 吞吐量。 

#### <a name="sap-content-server-location"></a>SAP 内容服务器位置
SAP 内容服务器必须部署在部署了 SAP 系统的 Azure 区域和 Azure VNET 中。 可以自行决定要将 SAP 内容服务器组件部署在专用的 Azure VM 上还是运行 SAP 系统的 VM 上。 

![SAP 内容服务器专用的 Azure VM](./media/dbms_maxdb_deployment_guide/800-azure-vm-sap-content-server.png)


#### <a name="sap-cache-server-location"></a>SAP 缓存服务器位置
SAP 缓存服务器是一个基于服务器的附加组件，可在本地提供对（缓存）文档的访问权限。 SAP 缓存服务器会缓存 SAP 内容服务器的文档。 如果必须从不同的位置多次检索文档，这样做可以优化网络流量。 一般规则是，SAP 缓存服务器在物理上必须靠近访问 SAP 缓存服务器的客户端。 

可以使用以下两个选项：

1. **客户端是后端 SAP 系统**：如果将后端 SAP 系统配置为访问 SAP 内容服务器，则该 SAP 系统就是客户端。 由于 SAP 系统和 SAP 内容服务器部署在同一个 Azure 区域（在相同的 Azure 数据中心），它们在物理上是彼此靠近的。 因此，不需要专用的 SAP 缓存服务器。 SAP UI 客户端（SAP GUI 或 Web 浏览器）可直接访问 SAP 系统，而 SAP 系统会从 SAP 内容服务器检索文档。
2. **客户端是本地 Web 浏览器**：SAP 内容服务器可配置为由 Web 浏览器直接访问。 在此情况下，本地运行的 Web 浏览器就是 SAP 内容服务器的客户端。 本地数据中心与 Azure 数据中心位于不同的物理位置（理想情况下相邻）。 本地数据中心通过 Azure 站点到站点 VPN 或 ExpressRoute 连接到 Azure。 虽然这两个选项均提供到 Azure 的安全 VPN 网络连接，但站点到站点网络连接不会在本地数据中心与 Azure 数据中心之间提供网络带宽和延迟 SLA。 若要加快对文档的访问，可以执行以下操作之一：
   1. 安装本地 SAP 缓存服务器，靠近本地 Web 浏览器（[此][dbms-guide-900-sap-cache-server-on-premises]图中的选项）
   2. 配置 Azure ExpressRoute，以便在本地数据中心与 Azure 数据中心之间提供高速且低延迟的专用网络连接。

![用于安装本地 SAP 缓存服务器的选项](./media/dbms_maxdb_deployment_guide/900-sap-cache-server-on-premises.png)
<a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### <a name="backup--restore"></a>备份/还原
如果将 SAP 内容服务器配置为将文件存储在 SAP MaxDB 数据库中，其备份/还原过程和性能注意事项已在本文档的 SAP MaxDB 章节中加以说明。 

如果将 SAP 内容服务器配置为将文件存储在文件系统中，有一个选项是针对文档所在的整个文件结构执行手动备份/还原。 与 SAP MaxDB 备份/还原类似，建议为备份使用专用的磁盘卷。 

#### <a name="other"></a>其他
其他特定于 SAP 内容服务器的设置对 Azure VM 是透明的，相关介绍请参阅各种文档和 SAP 说明：

* <https://service.sap.com/contentserver> 
* SAP 说明 [1619726]  
