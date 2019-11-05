---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522320"
---
若要使用 Azure 门户在 Resource Manager 部署模型中创建 VNet，请执行以下步骤。 如果是在教程中使用这些步骤，请使用**示例值**。 如果并非在教程中使用这些步骤，请务必将其中的值替换为自己的值。 有关使用虚拟网络的详细信息，请参阅 [虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>为了让此 VNet 连接到本地位置，需与本地网络管理员协调操作，指定一个 IP 地址范围，将其专用于此虚拟网络。 如果 VPN 连接的两侧存在重复的地址范围，则流量不会按预期的方式路由。 另外，若要将此 VNet 连接到其他 VNet，则地址空间不能与其他 VNet 重叠。 请注意对网络配置进行相应的计划。
>

1. 从 " [Azure 门户](https://portal.azure.com)" 菜单中，选择 "**创建资源**"。 

   ![在 Azure 门户中创建资源](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. 在“在市场中搜索”字段中，键入“虚拟网络”。 从返回的列表中找到“虚拟网络”，单击打开“虚拟网络”页。
3. 单击“创建”。 这会打开“创建虚拟网络”页。
4. 在“创建虚拟网络”页上，配置 VNet 设置。 填写字段时，如果在字段中输入的字符有效，红色感叹号标记会变成绿色对钩标记。 使用以下值：

   - **名称**： VNet1
   - **地址空间**：10.1.0.0/16
   - **订阅**：确认列出的订阅是你想要使用的订阅。 可以使用下拉列表更改订阅。
   - **资源组**： TestRG1 （单击 "**新建**" 创建新组）
   - **位置**：美国东部
   - **子网**：Frontend
   - **地址范围**：10.1.0.0/24

   ![创建虚拟网络页](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. 将 DDoS 作为基本、服务终结点和禁用的防火墙保持为禁用状态。
6. 单击“创建”以创建该 VNet 。