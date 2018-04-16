---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1d648b6f7727743c6d2f2c6e050778777e44ddf4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
若要使用 Azure 门户在 Resource Manager 部署模型中创建 VNet，请执行以下步骤。 如果是在教程中使用这些步骤，请使用[示例值](#values)。 如果并非在教程中使用这些步骤，请务必将其中的值替换为自己的值。 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>为了让此 VNet 连接到本地位置，需与本地网络管理员协调操作，指定一个 IP 地址范围，将其专用于此虚拟网络。 如果 VPN 连接的两侧存在重复的地址范围，则流量不会按预期的方式路由。 另外，若要将此 VNet 连接到其他 VNet，则地址空间不能与其他 VNet 重叠。 请注意对网络配置进行相应的计划。
>
>

1. 从浏览器导航到 [Azure 门户](http://portal.azure.com)并使用 Azure 帐户登录。
2. 单击“创建资源”。 在“搜索 Marketplace”字段中，键入“虚拟网络”。 从返回的列表中找到“虚拟网络”，单击打开“虚拟网络”页。
3. 从靠近“虚拟网络”页底部的“选择部署模型”列表中，选择“资源管理器”，然后单击“创建”。 这会打开“创建虚拟网络”页。

  ![“创建虚拟网络”页](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "“创建虚拟网络”页")
1. 在“创建虚拟网络”页上，配置 VNet 设置。 填写字段时，如果在字段中输入的字符有效，红色感叹号标记会变成绿色对钩标记。

  - “名称”：输入虚拟网络的名称。 本示例使用 VNet1。
  - **地址空间**：输入地址空间。 如果有多个要添加的地址空间，请添加第一个地址空间。 可在创建 VNet 后再添加其他地址空间。 请确保指定的地址空间与本地位置的地址空间不重叠。
  - “订阅”：确认列出的是正确的订阅。 可以使用下拉列表更改订阅。
  - **资源组**：选择现有的资源组，或键入新资源组的名称以创建新的资源组。 如果要创建新组，请根据计划的配置值来命名资源组。 有关资源组的详细信息，请访问 [Azure 资源管理器概述](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)。
  - **位置**：选择 VNet 的位置。 该位置确定要部署到此 VNet 的资源所在的位置。
  - **子网**：添加首个子网名称和子网地址范围。 创建此 VNet 后，可以再添加其他子网和网关子网。 

5. 如果希望能够在仪表板上轻松查找 VNet，请选择“固定到仪表板”，并单击“创建”。 单击“创建”后，将看到仪表板上的磁贴反映了 VNet 的进度。 创建 VNet 时，该磁贴会更改。