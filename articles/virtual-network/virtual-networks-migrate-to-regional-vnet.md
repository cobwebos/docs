---
title: "将 Azure 虚拟网络（经典）从地缘组迁移到区域 | Microsoft Docs"
description: "了解如何将虚拟网络（经典）从地缘组迁移到区域。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: b9b3bd0f2184ac85261166d5fe2ab67e1bf319d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>将虚拟网络（经典）从地缘组迁移到区域

> [!IMPORTANT]
> Azure 具有用于创建和处理资源的两个不同的部署模型： [Resource Manager 和经典](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本文介绍使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 部署模型。

地缘组确保在相同地缘组中创建的资源由彼此靠近的服务器实际托管，从而加快这些资源的通信速度。 过去，地缘组是创建虚拟网络（经典）的必要条件。 当时，托管虚拟网络（经典）的网络管理器服务只能在一组物理服务器或缩放单位内工作。 体系结构改进已将网络管理的范畴扩大到了区域。

由于这些体系结构的改进，不再建议或需要对虚拟网络（经典）使用地缘组。 现在，对虚拟网络（经典）使用地缘组的做法已被区域取代。 与区域关联的虚拟网络（经典）称为区域虚拟网络。

我们建议在一般情况下不要使用地缘组。 除了虚拟网络要求外，使用地缘组来确保计算（经典）和存储（经典）等资源的位置相互靠近同样重要。 但是，对于当前的 Azure 网络体系结构，不再需要满足这些位置要求。

> [!IMPORTANT]
> 尽管从技术上讲仍可以创建与地缘组关联的虚拟网络，但是，没有令人信服的理由让人这么做。 许多虚拟网络功能，例如网络安全组，仅当使用区域虚拟网络时才可用，而不能用于与地缘组关联的虚拟网络。
> 
> 

## <a name="edit-the-network-configuration-file"></a>编辑网络配置文件

1. 导出网络配置文件。 若要了解如何使用 PowerShell 或 Azure 命令行接口 (CLI) 1.0 导出网络配置文件，请参阅[使用网络配置文件配置虚拟网络](virtual-networks-using-network-configuration-file.md#export)。
2. 编辑网络配置文件，并将 **AffinityGroup** 替换为 **Location**。 为 **Location** 指定一个 Azure [区域](https://azure.microsoft.com/regions)。
   
   > [!NOTE]
   > **Location** 是已为与虚拟网络（经典）关联的地缘组指定的区域。 例如，如果虚拟网络（经典）与位于美国西部的地缘组关联，则在迁移时，**Location** 必须指向美国西部。 
   > 
   > 
   
    编辑网络配置文件中的以下行，将相应值替换成自己的值： 
   
    **旧值：** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **新值：** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. 保存所做的更改，并将网络配置[导入](virtual-networks-using-network-configuration-file.md#import)到 Azure。

> [!NOTE]
> 此迁移不会导致服务出现任何停机情况。
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>如果在地缘组中有一个 VM（经典）该怎么办
当前位于地缘组中的 VM（经典）不需要从地缘组中删除。 在 VM 部署后，系统会将其部署到单个缩放单位。 地缘组可以限制可用于新 VM 部署的 VM 大小集，但是部署的任何现有 VM 已限制为在其中部署该 VM 的缩放单位中可用的 VM 大小集。 由于该 VM 已部署到缩放单位，因此，从地缘组中删除该 VM 不会对 VM 产生影响。
