---
title: 允许 lab creator 在 Azure 实验室服务中选取位置
description: 本文介绍实验室帐户管理员如何允许实验室创建者为其实验室选取位置。
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
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444360"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>允许 lab creator 在 Azure 实验室服务中选取实验室位置
在 Azure 实验室服务中，实验室帐户所有者可以允许实验室创建者（教师）为其创建的实验室选取位置。 此位置可能与实验室帐户的位置不同。 位置是一组 Azure 区域。 例如，美国位置是一组区域，如 "美国东部"、"美国西部" 等。 

作为实验室帐户所有者，可以在创建实验室帐户时以及创建实验室帐户（或现有实验室帐户）后，选择 "**允许实验室创建者选取实验室位置**" 选项。 

## <a name="at-the-time-of-lab-account-creation"></a>创建实验室帐户时
创建实验室帐户时，将在第一个屏幕（"**基本**信息" 选项卡）上看到此选项。 

![启用实验室时启用选项](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

如果在 "**高级**" 选项卡中为实验室帐户选择对等虚拟网络，则此选项处于禁用状态。  

![启用对等虚拟网络后](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>创建实验室帐户后
创建实验室帐户后，可以通过执行以下步骤来启用或禁用此选项： 

1. 在 "**实验室帐户**" 页上，选择左侧菜单上的 "**实验室设置**"。
2. 如果要允许 lab creator 选择实验室位置，请选择 "**允许实验室创建者选取实验室位置**" 选项。 如果已禁用，则会在实验室帐户所在的同一位置自动创建实验室。 
    
    为**对等虚拟网络**字段选择虚拟网络时，将禁用此字段。 这是因为实验室帐户中的实验室必须与实验室帐户位于同一区域，才能访问对等虚拟网络中的资源。 
1. 在工具栏上选择“保存”。 

    ![实验室设置](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>不允许进行虚拟网络和位置选择
在此方案中，你尚未启用 "**允许实验室创建者选取实验室位置**" 选项。 

![无实验室位置](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

然后，实验室创建者（教师）将看不到选择实验室位置的选项。 他们将看到可供使用的每个大小选项的每小时价格。 当他们创建实验室时，它将在与实验室帐户所在的 Azure 区域相同的位置中创建。 例如，如果实验室帐户在**美国西部**，则可以在**美国中南部**创建实验室，但不会在**加拿大东部**创建实验室。 我们不能保证我们选择的区域中的任何内容，因为它位于此位置。 如果大小当前受到限制，则实验室创建者将看到一个复选框，用户可以在其中查看通常支持但当前不可用的大小。 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>不允许在 "虚拟网络" 和 "位置选择"
在此方案中，禁用了 "**允许实验室创建者选取实验室位置**" 选项，因为你已为实验室帐户选择了对等虚拟网络。 然后，实验室创建者将看到与上一个选项相同的屏幕。 由于所有 Vm 都必须与虚拟网络位于同一 Azure 区域，因此，将在虚拟网络所在的同一 Azure 区域中创建实验室。 如果该特定区域限制为大小，则大小将显示为 "不可用"。 

## <a name="location-selection-is-enabled"></a>已启用位置选择
当你选择 "**允许 lab creator" 选取实验室位置**时，实验室创建者（教师）会看到一个选项，用于在创建实验室时选择位置。 

![选择实验室位置](../media/allow-lab-creator-pick-lab-location/location-selection.png)

实验室创建者查看大小所在的所有位置的价格范围，并可选择一个位置。 将在映射到该位置的任何 Azure 区域中创建实验室。

如果某个位置受到约束，则默认情况下它不会显示在列表中。 展开下拉列表，然后选择 "**显示此大小不可用的位置**"。 

![显示不可用位置](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>成本
之前，定价基于你为实验室选择的 VM 大小。 现在，价格取决于操作系统（OS）、大小和位置的组合。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [将实验室的网络连接到对等虚拟网络](how-to-connect-peer-virtual-network.md)
- [将共享映像库附加到实验室](how-to-attach-detach-shared-image-gallery.md)
- [添加用户作为实验室所有者](how-to-add-user-lab-owner.md)
- [查看实验室的防火墙设置](how-to-configure-firewall-settings.md)
- [为实验室配置其他设置](how-to-configure-lab-accounts.md)