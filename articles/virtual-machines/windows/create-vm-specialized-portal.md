---
title: "在 Azure 门户的专用 VHD 中创建 Windows VM | Microsoft Docs"
description: "在 Azure 门户的 VHD 中创建新的 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: cynthn
ms.openlocfilehash: 33e94777047ea8a116ae6f393439521356a53509
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-vm-from-a-vhd-using-the-azure-portal"></a>使用 Azure 门户在 VHD 中创建 VM


可以通过多种方法在 Azure 中创建 VM。 如果已经有一个要使用的 VHD，或者需要从要使用的现有 VM 复制 VHD，则可将 VHD 作为 OS 磁盘附加，从而创建新的 VM。 此过程将 VHD 作为 OS 磁盘附加到新的 VM。

也可从已删除的 VM 的 VHD 创建新的 VM。 例如，如果 Azure VM 运行不正常，则可删除该 VM，然后使用 VHD 创建新的 VM。 可以重用同一 VHD，也可以创建 VHD 的副本，方法是：先创建快照，然后根据快照创建新的托管磁盘。 这需要更多的步骤，但可确保你能够保留原始 VHD，同时为你提供一个快照，在需要时可以回退。

你有一个本地 VM，可以用来在 Azure 中创建 VM。 可以上传 VHD 并将其附加到新的 VM。 若要上传 VHD，需使用 PowerShell 或其他工具将其上传到存储帐户，然后根据 VHD 创建托管磁盘。 有关详细信息，请参阅[上传专用 VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd)。

如果需要使用 VM 或 VHD 来创建多个 VM，则不应使用此方法。 对于较大的部署，应先[创建映像](capture-image-resource.md)，然后[使用该映像创建多个 VM](create-vm-generalized-managed.md)。


## <a name="copy-a-disk"></a>复制磁盘

创建一个快照，然后根据该快照创建一个磁盘。 这样就可以保留原始 VHD 作为回退的依据。

1. 在左侧菜单中，单击“所有资源”。
2. 在“所有类型”下拉列表中取消选中“全选”，然后向下滚动并选择“磁盘”，以便查找可用的磁盘。
3. 单击要使用的磁盘。 此时会打开磁盘的“概览”页。
4. 在“概览”页顶部的菜单上，单击“+ 创建快照”。 
5. 键入快照的名称。
6. 为快照选择一个**资源组**。 可以使用现有资源组，也可以创建新组。
7. 选择是使用标准 (HDD) 存储还是高级 (SDD) 存储。
8. 完成后单击“创建”，以便创建快照。
9. 创建快照以后，单击左侧菜单中的“+ 创建资源”。
10. 在搜索栏中键入“托管磁盘”，然后从列表中选择“托管磁盘”。
11. 在“托管磁盘”页上单击“创建”。
12. 键入磁盘名称。
13. 为磁盘选择一个**资源组**。 可以使用现有资源组，也可以创建新组。 该组也将是在其中根据磁盘创建 VM 的资源组。
14. 选择是使用标准 (HDD) 存储还是高级 (SDD) 存储。
15. 在“源类型”中，确保选中“快照”。
16. 在“源快照”下拉列表中，选择要使用的快照。
17. 根据需要进行其他方面的调整，然后单击“创建”以创建磁盘。

## <a name="create-a-vm-from-a-disk"></a>从磁盘创建 VM

有了要使用的托管磁盘 VHD 以后，即可在门户中创建 VM。

1. 在左侧菜单中，单击“所有资源”。
2. 在“所有类型”下拉列表中取消选中“全选”，然后向下滚动并选择“磁盘”，以便查找可用的磁盘。
3. 单击要使用的磁盘。 此时会打开磁盘的“概览”页。
在“概览”页中，确保“磁盘状态”列为“未附加”。 否则，可能需要将磁盘从 VM 拆离，或者需要删除 VM 以释放磁盘空间。
4. 在窗格顶部的菜单中，单击“+ 创建 VM”。
5. 在新 VM 的“基本信息”页中键入一个名称，然后选择现有资源组或创建新组。
6. 在“大小”页中选择 VM 大小，然后单击“选择”。
7. 在“设置”页中，可以让门户创建所有新的资源，也可以选择现有的**虚拟网络**和**网络安全组**。 门户始终为新 VM 创建新 NIC 和公共 IP 地址。 
8. 根据需要更改监视选项和添加扩展。
9. 完成后，单击“确定”。 
10. 如果 VM 配置通过验证，则单击“确定”即可启动部署。

## <a name="next-steps"></a>后续步骤

也可使用 PowerShell [将 VHD 上传到 Azure 并创建专用 VM](create-vm-specialized.md)。


