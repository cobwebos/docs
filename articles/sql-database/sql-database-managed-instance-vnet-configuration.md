---
title: "Azure SQL 数据库托管实例 VNet 配置 | Microsoft Docs"
description: "本主题介绍使用 Azure SQL 数据库托管实例的虚拟网络 (VNet) 的配置选项。"
services: sql-database
author: srdjan-bozovic
manager: cguyer
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 1a839a9bb2355da9451816828f6f9f0e99f43f5e
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置 VNet

Azure SQL 数据库托管实例（预览版）必须在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中部署。 此部署支持以下方案： 
- 直接从本地网络连接到托管实例 
- 将托管实例连接到链接服务器或其他本地数据存储 
- 将托管实例连接到 Azure 资源  

## <a name="plan"></a>计划

使用以下问题的解答，规划如何在虚拟网络中部署托管实例： 
- 计划部署单个还是多个托管实例？ 

  托管实例的数量决定了要分配给托管实例的子网的最小大小。 有关详细信息，请参阅[确定托管实例的子网大小](#create-a-new-virtual-network-for-managed-instances)。 
- 是要将托管实例部署到现有的虚拟网络，还是创建新的网络？ 

   如果打算使用现有的虚拟网络，则需要修改该网络的配置，以适应托管实例。 有关详细信息，请参阅[根据托管实例修改现有的虚拟网络](#modify-an-existing-virtual-network-for-managed-instances)。 

   如果打算创建新的虚拟网络，请参阅[为托管实例创建新的虚拟网络](#create-new-virtual-network-for-managed-instances)。

## <a name="requirements"></a>要求

若要创建托管实例，需在 VNet 中专门指定一个符合以下要求的子网：
- **为空**：该子网不能包含任何关联的其他云服务，并且不能是网关子网。 无法在包含除托管实例以外的资源的子网中创建托管实例，并且以后无法在子网中添加其他资源。
- **无 NSG**：该子网不能有关联的网络安全组。
- **具有特定的路由表**：该子网必须有一个用户路由表 (UDR)，并且向该路由表分配了 0.0.0.0/0 下一跃点 Internet 作为唯一路由。 有关详细信息，请参阅[创建并关联所需的路由表](#create-the-required-route-table-and-associate-it)
3. **可选的自定义 DNS**：如果在 VNet 中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 有关详细信息，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
4. **无服务终结点**：该子网不能有关联的服务终结点（存储或 SQL）。 创建 VNet 时，请务必禁用“服务终结点”选项。
5. **足够的 IP 地址**：该子网必须至少有 16 个 IP 地址。 有关详细信息，请参阅[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> 如果目标子网不符合上述所有要求，则无法部署新的托管实例。 目标 Vnet 和子网必须符合这些托管实例要求（部署之前和之后），因为任何违规都可能导致实例进入错误状态并变得不可用。 从该状态恢复需要使用符合要求的网络策略在 VNet 中创建新实例、重新创建实例级数据并还原数据库。 这会造成应用程序长时间关闭。

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>确定托管实例的子网大小

创建托管实例时，Azure 会根据预期期间选择的层大小，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。 

根据设计，托管实例至少需要子网中的 16 个 IP 地址，有时使用多达 256 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用子网掩码 /28 到 /24。 

如果打算在子网中部署多个托管实例并需要优化子网大小，可使用以下参数来完成计算： 

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要 
- 每个常规用途实例需要 2 个地址 

**示例**：你打算创建 8 个托管实例。 这意味着，需要 5 + 8 * 2 = 21 个 IP 地址。 由于 IP 范围定义为 2 的 N 次方，因此需要 32 个 (2^5) IP 地址。 因此，需要保留子网掩码为 /27 的子网。 

## <a name="create-a-new-virtual-network-for-managed-instances"></a>为托管实例创建新的虚拟网络 

创建 Azure 虚拟网络是创建托管实例的先决条件。 可以使用 Azure 门户、[PowerShell](../virtual-network/quick-create-powershell.md) 或 [Azure CLI](../virtual-network/quick-create-cli.md)。 以下部分介绍使用 Azure 门户的步骤。 此处所述的详细信息适用于上述每种方法。

1. 在 Azure 门户的左上角单击“创建资源”。
2. 找到并单击“虚拟网络”，确认是否已选择“资源管理器”作为部署模型，然后单击“创建”。

   ![虚拟网络创建](./media/sql-database-managed-instance-tutorial/virtual-network-create.png)

3. 如以下屏幕截图所示，在虚拟网络窗体中填写请求的信息：

   ![虚拟网络创建窗体](./media/sql-database-managed-instance-tutorial/virtual-network-create-form.png)

4. 单击“创建”。

   地址空间和子网以 CIDR 表示法指定。 

   > [!IMPORTANT]
   > 使用默认值会创建采用所有 VNet 地址空间的子网。 如果选择此选项，则无法在虚拟网络中创建除托管实例以外的其他任何资源。 

   建议的方法如下： 
   - 根据[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)部分计算子网大小  
   - 评估 VNet 其余部分的需求 
   - 相应地填写 VNet 和子网地址范围 

   确保“服务终结点”选项保留为“已禁用”。 

   ![虚拟网络创建窗体](./media/sql-database-managed-instance-tutorial/service-endpoint-disabled.png)

## <a name="create-the-required-route-table-and-associate-it"></a>创建并关联所需的路由表

1. 登录到 Azure 门户  
2. 找到并单击“路由表”，然后单击“路由表”页中的“创建”。

   ![路由表创建窗体](./media/sql-database-managed-instance-tutorial/route-table-create-form.png)

3. 如以下屏幕截图所示，创建 0.0.0.0/0 下一跃点 Internet 路由

   ![路由表添加](./media/sql-database-managed-instance-tutorial/route-table-add.png)

   ![路由](./media/sql-database-managed-instance-tutorial/route.png)

4. 如以下屏幕截图所示，将此路由关联到托管实例的子网：

    ![子网](./media/sql-database-managed-instance-tutorial/subnet.png)

    ![设置路由表](./media/sql-database-managed-instance-tutorial/set-route-table.png)

    ![设置路由表 - 保存](./media/sql-database-managed-instance-tutorial/set-route-table-save.png)


创建 VNet 后，便可以创建托管实例。  

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>根据托管实例修改现有的虚拟网络 

本部分的问题和解答说明如何将托管实例添加到现有的虚拟网络。 

**对现有虚拟网络使用的是经典模型还是资源管理器部署模型？** 

只能在资源管理器虚拟网络中创建托管实例。 

**是要为 SQL 托管实例创建新子网还是使用现有子网？**

若要创建新子网： 

- 根据[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)部分的指导计算子网大小。
- 遵循[添加、更改或删除虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)中的步骤。 
- 创建包含单个条目 **0.0.0.0/0** 的路由表作为下一跃点 Internet，并将其关联到托管实例的子网。  

若要在现有子网中创建托管实例： 
- 检查该子网是否为空 - 不能在包含其他资源的子网（包括网关子网）中创建托管实例 
- 根据[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)部分的指导计算子网大小，并验证子网大小是否合适。 
- 检查是否未在该子网上启用服务终结点。
- 确保没有任何网络安全组与该子网相关联 

**是否配置了自定义 DNS 服务器？** 

如果是，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。 

- 创建并关联所需的路由表：请参阅[创建并关联所需的路由表](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关如何创建 VNet、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-tutorial-portal.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
