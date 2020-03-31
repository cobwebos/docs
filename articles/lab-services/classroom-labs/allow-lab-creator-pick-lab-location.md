---
title: 允许实验室创建者在 Azure 实验室服务中选择位置
description: 本文介绍实验室帐户管理员如何允许实验室创建者为其实验室选择位置。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444360"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>允许实验室创建者在 Azure 实验室服务中选择实验室的位置
在 Azure 实验室服务中，实验室帐户所有者可以允许实验室创建者（教育者）为其创建的实验室选择位置。 此位置可能不同于实验室帐户的位置。 位置是一组 Azure 区域。 例如，美国位置是一组区域，如美国东部、美国西部等。 

作为实验室帐户所有者，您可以选择"**允许实验室创建者在**创建实验室帐户时以及创建实验室帐户（或现有实验室帐户）后选择实验室位置选项。 

## <a name="at-the-time-of-lab-account-creation"></a>在创建实验室帐户时
创建实验室帐户时，在第一个屏幕上看到此选项（**基本选项卡**）。 

![在实验室创建时启用该选项](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

如果在 **"高级"** 选项卡中为实验室帐户选择对等虚拟网络，则禁用此选项。  

![启用对等虚拟网络时](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>创建实验室帐户后
创建实验室帐户后，可以通过以下步骤启用或禁用此选项： 

1. 在 **"实验室帐户"** 页上，选择左侧菜单上的 **"实验室"设置**。
2. 如果要允许实验室创建者为实验室选择位置，请选择"**允许实验室创建者选择实验室位置**"选项。 如果禁用，则实验室将自动创建在实验室帐户存在的同一位置。 
    
    当您为**对等虚拟网络**字段选择虚拟网络时，此字段将禁用。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，以便它们访问对等虚拟网络中的资源。 
1. 在工具栏上选择“保存”。**** 

    ![实验室设置](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>不允许虚拟网络和位置选择
在这种情况下，您尚未启用 **"允许实验室创建者选择实验室位置**"选项。 

![无实验室位置](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

然后，实验室创建者（教育工作者）看不到选择实验室位置的选项。 他们将看到每个尺寸选项每小时的价格。 创建实验室时，将在 Azure 区域中创建该实验室，该区域位于其实验室帐户位于的 Azure 区域的同一位置。 例如，如果实验室帐户**位于美国西部**，则实验室可能**在美国中南部**创建，但不会**在加拿大东部**创建。 我们不保证我们选择的区域，除了它的位置。 如果大小当前受到限制，则实验室创建者将看到一个复选框，其中他们可以看到我们通常支持但当前不可用的大小。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>不允许在虚拟网络和位置选择中
在这种情况下，**禁用"允许实验室创建者选择实验室位置**"选项，因为您为实验室帐户选择了对等虚拟网络。 然后，实验室创建者将看到与前一个选项相同的屏幕。 由于所有 VM 都必须与虚拟网络位于同一 Azure 区域中，因此将在虚拟网络位于的同一 Azure 区域中创建实验室。 如果该特定区域的大小受到限制，则大小将显示为不可用。 

## <a name="location-selection-is-enabled"></a>启用位置选择
当您选择 **"允许实验室创建者选择实验室位置"** 时，实验室创建者（教育者）将看到一个选项，用于在创建实验室时选择位置。 

![选择实验室位置](../media/allow-lab-creator-pick-lab-location/location-selection.png)

实验室创建者查看大小所在的所有位置的价格范围，并可以选择位置。 该实验室将在映射到该位置的任何 Azure 区域中创建。

如果位置受约束，默认情况下不会显示在列表中。 展开下拉列表，然后选择 **"显示此大小的不可用位置**"。 

![显示不可用的位置](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>成本
以前，定价基于您为实验室选择的 VM 大小。 现在，价格基于操作系统 （OS）、大小和位置的组合。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将实验室网络与对等虚拟网络连接](how-to-connect-peer-virtual-network.md)
- [将共享图像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [将用户添加为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)