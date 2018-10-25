---
title: 在 Azure 门户的专用 VHD 中创建 Windows VM | Microsoft Docs
description: 在 Azure 门户的 VHD 中创建新的 Windows VM。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/20/2018
ms.author: cynthn
ms.openlocfilehash: 905f00842c5ce74f681a6c5c09ff8bf6c7a9e162
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091243"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>使用 Azure 门户在 VHD 中创建 VM

有多种方法可在 Azure 中创建虚拟机 (VM)： 

- 如果已经有一个要使用的虚拟硬盘 (VHD)，或者需要从要使用的现有 VM 复制 VHD，可以将 VHD 附加到新 VM 作为 OS 磁盘，以此创建新的 VM。 

- 可从已删除的 VM 的 VHD 创建新的 VM。 例如，如果 Azure VM 运行不正常，则可以删除该 VM，然后使用其 VHD 创建新的 VM。 可以重用同一 VHD，也可以创建 VHD 的副本，方法是：先创建快照，然后根据快照创建新的托管磁盘。 虽然创建快照需要多个步骤，但它可以保留原始 VHD 并提供回退的依据。
 
- 可以通过上传本地 VHD 并将其附加到新 VM，在本地 VHD 上创建 Azure VM。 可使用 PowerShell 或其他工具将 VHD 上传到存储帐户，然后根据 VHD 创建托管磁盘。 有关详细信息，请参阅[上传专用 VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd)。 

如果想创建多个 VM，请勿使用专用磁盘。 对于较大的部署，应先[创建映像](capture-image-resource.md)，然后[使用该映像创建多个 VM](create-vm-generalized-managed.md)。


## <a name="copy-a-disk"></a>复制磁盘

创建一个快照，然后根据该快照创建一个磁盘。 此策略可以保留原始 VHD 作为回退的依据：

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中，选择“所有服务”。
2. 在“所有服务”搜索框中，输入“磁盘”，然后选择“磁盘”以显示可用磁盘列表。
3. 选择要使用的磁盘。 此时会显示该磁盘的“磁盘”页。
4. 从顶部菜单选择“创建快照”。 
5. 输入快照的“名称”。
6. 为快照选择一个**资源组**。 可以使用现有资源组，也可以创建新组。
7. 对于“帐户类型”，选择“标准 (HDD)”或“高级 (SSD)”存储。
8. 完成后选择“创建”，以便创建快照。
9. 创建快照后，选择左侧菜单中的“创建资源”。
10. 在搜索框中输入“托管磁盘”，然后从列表中选择“托管磁盘”。
11. 在“托管磁盘”页上选择“创建”。
12. 输入磁盘的名称。
13. 为磁盘选择一个**资源组**。 可以使用现有资源组，也可以创建新组。 该选择也将用作在其中根据磁盘创建 VM 的资源组。
14. 对于“帐户类型”，选择“标准 (HDD)”或“高级 (SSD)”存储。
15. 在“源类型”中，确保选中“快照”。
16. 在“源快照”下拉列表中，选择要使用的快照。
17. 根据需要进行其他方面的调整，然后选择“创建”以创建磁盘。

## <a name="create-a-vm-from-a-disk"></a>从磁盘创建 VM

有了要使用的托管磁盘 VHD 以后，即可在门户中创建 VM：

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中，选择“所有服务”。
2. 在“所有服务”搜索框中，输入“磁盘”，然后选择“磁盘”以显示可用磁盘列表。
3. 选择要使用的磁盘。 此时会打开该磁盘的“磁盘”页。
4. 在“概览”页中，确保“磁盘状态”列为“未附加”。 否则，可能需要将磁盘从 VM 拆离，或者需要删除 VM 以释放磁盘空间。
4. 在页面顶部的菜单中选择“创建 VM”。
5. 在新 VM 的“基本信息”页中输入一个“虚拟机名称”，然后选择现有“资源组”或创建新组。
6. 对于大小，选择“更改大小”以访问“大小”页。
7. 选择 VM 大小行，然后选择“选择”。
8. 在“网络”页中，可以让门户创建所有新的资源，也可以选择现有的“虚拟网络”和“网络安全组”。 门户始终为新 VM 创建新网络接口和公共 IP 地址。 
9. 在“管理”页上，对监视的选项进行任何更改。
10. 在“来宾配置”页上，根据需要添加任何扩展。
11. 完成操作后，选择“查看 + 创建”。 
12. 如果 VM 配置通过验证，则选择“创建”以启动部署。

## <a name="next-steps"></a>后续步骤

也可使用 PowerShell [将 VHD 上传到 Azure 并创建专用 VM](create-vm-specialized.md)。


