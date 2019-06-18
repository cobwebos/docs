---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171594"
---
可以通过以下步骤使用资源管理器部署模型和 Azure 门户创建一个 VNet。 有关虚拟网络的详细信息，请参阅[虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>为了让此 VNet 连接到本地位置，请与本地网络管理员协调操作，指定一个 IP 地址范围，将其专用于此虚拟网络。 如果 VPN 连接的两端存在重复的地址范围，则会以意外方式路由流量。 另外，若要将此 VNet 连接到其他 VNet，则地址空间不能与其他 VNet 重叠。 相应地规划网络配置。
>
>

1. 登录到 [Azure 门户](http://portal.azure.com)，然后选择“创建资源”  。 此时会打开一个“新建”页面  。

2. 在“搜索市场”字段中，输入“虚拟网络”，然后从返回的列表中选择“虚拟网络”    。 此时会打开“虚拟网络”页  。

   ![查找虚拟网络资源页](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "查找虚拟网络资源页")

3. 从页面底部附近的“选择部署模型”列表中，选择“资源管理器”，然后选择“创建”    。 此时会打开“创建虚拟网络”页  。

   ![创建虚拟网络页](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "创建虚拟网络页")

4. 在“创建虚拟网络”  页上，配置 VNet 设置。 填写字段时，当你在字段中输入的字符通过验证时，红色感叹号标记会变成绿色对钩标记。 某些值是自动填写的，你可以将其替换为自己的值：

   - **名称**：输入虚拟网络的名称。

   - **地址空间**：输入地址空间。 如果有多个要添加的地址空间，请在此处输入你的第一个地址空间。 可以在创建 VNet 后再添加其他地址空间。

   - **订阅**：确认列出的订阅是正确的。 可以使用下拉列表更改订阅。

   - **资源组**：选择现有的资源组，或输入新资源组的名称以创建新的资源组。 如果要创建新组，请根据计划的配置值来命名资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)。

   - **位置**：选择 VNet 的位置。 该位置确定要部署到此 VNet 的资源将位于哪里。

   - **子网**：添加子网**名称**和子网**地址范围**。 可在创建 VNet 后再添加其他子网。 
     
5. 选择“创建”  。
