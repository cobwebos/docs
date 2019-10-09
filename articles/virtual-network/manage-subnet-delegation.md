---
title: 在 Azure 虚拟网络中添加或删除子网委派
titlesuffix: Azure Virtual Network
description: 了解如何在 azure 中为 azure 中的服务添加或删除委托子网。
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 5fa340fc3c839d74f292f551b73184ea4df1c0f1
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175949"
---
# <a name="add-or-remove-a-subnet-delegation"></a>添加或删除子网委派

子网委派为服务提供了显式权限，以便能够在部署服务时使用唯一标识符在子网中创建服务专属资源。 本文介绍如何为 Azure 服务添加或删除委托子网。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="create-the-virtual-network"></a>创建虚拟网络

在本部分中，将创建一个虚拟网络和子网，稍后会将该虚拟网络委托给 Azure 服务。

1. 在屏幕的左上方，选择“创建资源” > “网络” > “虚拟网络”。
1. 在“创建虚拟网络”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 名称 | 输入 *MyVirtualNetwork*。 |
    | 地址空间 | 输入 *10.0.0.0/16*。 |
    | 订阅 | 选择订阅。|
    | 资源组 | 选择“新建”，输入 myResourceGroup，然后选择“确定”。 |
    | Location | 选择**EastUS**。|
    | 子网 - 名称 | 输入 *mySubnet*。 |
    | 子网 - 地址范围 | 输入 *10.0.0.0/24*。 |
    |||
1. 将 rest 保留为默认值，然后选择 "**创建**"。

## <a name="permissons"></a>权限

如果你未创建要委派给 Azure 服务的子网，则需要以下权限： `Microsoft.Network/virtualNetworks/subnets/write`。

内置[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色还包含所需的权限。

## <a name="delegate-a-subnet-to-an-azure-service"></a>将子网委托给 Azure 服务

在本部分中，将在上一节中创建的子网委托给 Azure 服务。

1. 在门户的搜索栏中，输入 *myVirtualNetwork*。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
2. 在搜索结果中，选择 " *myVirtualNetwork*"。
3. 在 "**设置**" 下选择**子网**，然后选择 " **mySubnet**"。
4. 在 " *mySubnet* " 页上，对于 "**子网委派**列表"，从 "**委托子网到服务**" 下列出的服务中进行选择（例如**DBforPostgreSQL/serversv2**）。  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>从 Azure 服务中删除子网委派

1. 在门户的搜索栏中，输入 *myVirtualNetwork*。 当“myVirtualNetwork”出现在搜索结果中时，将其选中。
2. 在搜索结果中，选择 " *myVirtualNetwork*"。
3. 在 "**设置**" 下选择**子网**，然后选择 " **mySubnet**"。
4. 在 " *mySubnet* " 页中，对于 "**子网委派**列表"，请从 "**委托子网到服务**" 下列出的服务中选择**None** 。 

## <a name="next-steps"></a>后续步骤
- 了解如何[在 Azure 中管理子网](virtual-network-manage-subnet.md)。
