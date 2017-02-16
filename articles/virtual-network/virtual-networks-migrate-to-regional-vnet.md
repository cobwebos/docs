---
title: "将 Azure 虚拟网络从地缘组迁移到某个区域 | 经典 | Microsoft 文档"
description: "了解如何将虚拟网络从地缘组迁移到某个区域。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: f74379c3e310a7bbcf659ef610727bff19639022
ms.openlocfilehash: c495af3e818758cc5fe99af9b5f07506a16b59ef


---
# <a name="how-to-migrate-a-virtual-network-from-an-affinity-group-to-a-region"></a>如何将虚拟网络从地缘组迁移到某个区域
可以使用地缘组来确保在相同地缘组中创建的资源由彼此靠近的服务器实际托管，从而加快这些资源的通信速度。 过去，地缘组是创建虚拟网络 (VNet) 的必要条件。 当时，托管 VNet 的网络管理器服务只能在一组物理服务器或缩放单位内工作。 体系结构改进已将网络管理的范畴扩大到了区域。

由于这些体系结构的改进，不再建议或需要对虚拟网络使用地缘组。 对 VNet 使用地缘组将被区域取代。 与区域关联的虚拟网络称为区域 VNet。

此外，我们建议在一般情况下不要使用地缘组。 除了 VNet 要求外，使用地缘组来确保计算和存储等资源的位置相互靠近同样重要。 但是，对于当前的 Azure 网络体系结构，不再需要满足这些位置要求。 有关可能需要使用地缘组的其余少数特定情况，请参阅[地缘组和 VM](#Affinity-groups-and-VMs)。

## <a name="creating-and-migrating-to-regional-vnets"></a>创建和迁移到区域 VNet
今后，在创建新的 VNet 时，可以使用*区域*。 你可以将此视为管理门户中的一个选项。 请注意，在网络配置文件中，这显示为*位置*。

> [!IMPORTANT]
> 尽管从技术上讲仍可以创建与地缘组关联的虚拟网络，但是，没有令人信服的理由让人这么做。 许多新功能，例如网络安全组，仅当使用区域 VNet 时才可用，而不能用于与地缘组关联的虚拟网络。
> 
> 

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>关于与地缘组关联的 VNet
已启用当前与地缘组关联的虚拟网络以迁移到区域 VNet。 若要迁移到区域 VNet，请执行以下步骤：

1. 导出网络配置文件。 可以使用 PowerShell 或管理门户。 有关使用管理门户的说明，请参阅[使用网络配置文件配置 VNet](virtual-networks-using-network-configuration-file.md)。
2. 编辑网络配置文件，将旧值替换为新值。 
   
   > [!NOTE]
   > **位置**是已为与 VNet 关联的地缘组指定的区域。 例如，如果你的 VNet 与位于美国西部的地缘组关联，则在迁移时，你的“位置”必须指向美国西部。 
   > 
   > 
   
    编辑网络配置文件中的以下行，将相应值替换为你自己的值： 
   
    **旧值：** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **新值：** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. 保存所做的更改，并将网络配置[导入](virtual-networks-using-network-configuration-file.md)到 Azure。

> [!NOTE]
> 此迁移不会导致你的服务出现任何停机情况。
> 
> 

## <a name="affinity-groups-and-vms"></a>地缘组和 VM
如前所述，一般不再建议对 VM 使用地缘组。 仅当一组 VM 必须在 VM 之间拥有绝对最低网络延迟时，才应该使用地缘组。 将 VM 置于地缘组中后，VM 将全部置于相同计算群集或缩放单位中。

必须注意，使用地缘组可能会造成两个负面后果：

* VM 大小集将限制为计算缩放单位所提供的 VM 大小集。
* 无法分配新 VM 的概率较高。 如果地缘组的特定缩放单位超出能力范畴，则会发生这种情况。

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>如果你在地缘组中有一个 VM 该怎么办
当前位于地缘组中的 VM 不需要从地缘组中删除。

在 VM 部署后，系统会将其部署到单个缩放单位。 地缘组可以限制可用于新 VM 部署的 VM 大小集，但是部署的任何现有 VM 已限制为在其中部署该 VM 的缩放单位中可用的 VM 大小集。 因此，从地缘组中删除 VM 将不产生任何影响。




<!--HONumber=Feb17_HO1-->


