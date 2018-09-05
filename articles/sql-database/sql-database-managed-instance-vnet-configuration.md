---
title: Azure SQL 数据库托管实例 VNet 配置 | Microsoft Docs
description: 本主题介绍使用 Azure SQL 数据库托管实例的虚拟网络 (VNet) 的配置选项。
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: b17749999f7903746651403c5948933332dbee5d
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047926"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置 VNet

Azure SQL 数据库托管实例（预览版）必须在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中部署。 此部署支持以下方案： 
- 直接从本地网络连接到托管实例 
- 将托管实例连接到链接服务器或其他本地数据存储 
- 将托管实例连接到 Azure 资源  

## <a name="plan"></a>计划

使用以下问题的解答，规划如何在虚拟网络中部署托管实例： 
- 计划部署单个还是多个托管实例？ 

  托管实例的数量决定了要分配给托管实例的子网的最小大小。 有关详细信息，请参阅[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)。 
- 是要将托管实例部署到现有的虚拟网络，还是创建新的网络？ 

   如果打算使用现有的虚拟网络，则需要修改该网络的配置，以适应托管实例。 有关详细信息，请参阅[根据托管实例修改现有的虚拟网络](#modify-an-existing-virtual-network-for-managed-instances)。 

   如果打算创建新的虚拟网络，请参阅[为托管实例创建新的虚拟网络](#create-a-new-virtual-network-for-managed-instances)。

## <a name="requirements"></a>要求

若要创建托管实例，需要专门使用 VNet 中符合以下要求的一个子网：
- **专用子网**：该子网不能包含任何关联的其他云服务，并且不能是网关子网。 无法在包含除托管实例以外的资源的子网中创建托管实例，并且以后无法在子网中添加其他资源。
- **无 NSG**：该子网不能有关联的网络安全组。 
- **具有特定的路由表**：该子网必须有一个用户路由表 (UDR)，并且向该路由表分配了 0.0.0.0/0 下一跃点 Internet 作为唯一路由。 有关详细信息，请参阅[创建并关联所需的路由表](#create-the-required-route-table-and-associate-it)
3. **可选的自定义 DNS**：如果在 VNet 中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 有关详细信息，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
4. **无服务终结点**：该子网不能有关联的服务终结点。 创建 VNet 时，请务必禁用“服务终结点”选项。
5. **足够的 IP 地址**：该子网必须最少具有 16 个 IP 地址（建议的最小值是 32 个 IP 地址）。 有关详细信息，请参阅[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> 如果目标子网不符合上述所有要求，则无法部署新的托管实例。 目标 Vnet 和子网必须符合这些托管实例要求（部署之前和之后），因为任何违规都可能导致实例进入错误状态并变得不可用。 从该状态恢复需要使用符合要求的网络策略在 VNet 中创建新实例、重新创建实例级数据并还原数据库。 这会造成应用程序长时间关闭。

引入“网络意向策略”后，在创建托管实例后，你可以在托管实例子网上添加网络安全组 (NSG)。

现在，你可以使用 NSG 通过筛选发往端口 1433 的网络流量来缩小应用程序和用户可以从中查询和管理数据的 IP 范围。 

> [!IMPORTANT]
> 在配置将限定对端口 1433 的访问的 NSG 规则时，还需要插入下表中显示的最高优先级入站规则。 否则，网络意向策略将因为更改不合规而阻止更改。

| 名称       |端口                        |协议|来源           |目的地|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|任意     |任意              |任意        |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允许 |

路由体验已改进，除了 0.0.0.0/0 下一跃点类型的 Internet 路由之外，现在还可以添加 UDR 来将流量通过虚拟网络网关或虚拟网络设备 (NVA) 路由到本地专用 IP 范围。

##  <a name="determine-the-size-of-subnet-for-managed-instances"></a>确定托管实例的子网大小

创建托管实例时，Azure 会根据预配期间选择的层，分配一定数量的虚拟机。 由于这些虚拟机与子网关联，因此它们需要 IP 地址。 为了确保日常操作和服务维护期间保持高可用性，Azure 可能会分配更多的虚拟机。 因此，子网中所需的 IP 地址数大于该子网中的托管实例数。 

根据设计，托管实例至少需要子网中的 16 个 IP 地址，有时使用多达 256 个 IP 地址。 因此，在定义子网 IP 范围时，可以使用子网掩码 /28 到 /24。 

> [!IMPORTANT]
> 具有 16 个 IP 地址的子网大小是绝对最小值，进一步实现托管实例横向扩展的潜力有限。强烈建议选择前缀为 /27 或更低值的子网。 

如果打算在子网中部署多个托管实例并需要优化子网大小，可使用以下参数来完成计算： 

- Azure 使用子网中的 5 个 IP 地址来满足自身的需要 
- 每个常规用途实例需要 2 个地址 
- 每个业务关键实例需要四个地址

**示例**：你计划拥有三个常规用途和两个业务关键托管实例。 这意味着，需要 5 + 3 * 2 + 2 * 4 = 19 个 IP 地址。 由于 IP 范围定义为 2 的 N 次方，因此需要 32 个 (2^5) IP 地址。 因此，需要保留子网掩码为 /27 的子网。 

> [!IMPORTANT]
> 上方显示的计算将随着进一步改进而变得过时。 

## <a name="create-a-new-virtual-network-for-managed-instance-using-azure-resource-manager-deployment"></a>使用 Azure 资源管理器部署为托管实例创建新的虚拟网络

创建和配置虚拟网络的最简单方法是使用 Azure 资源管理器部署模板。

1. 登录到 Azure 门户。

2. 使用“部署到 Azure”按钮在 Azure 云中部署虚拟网络：

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  此按钮将打开一个窗体，可以使用该窗体来配置你可以在其中部署托管实例的网络环境。

  > [!Note]
  > 此 Azure 资源管理器模板将部署包含两个子网的虚拟网络。 一个名为 **ManagedInstances** 的子网预留给托管实例并且已预配置了路由表，而名为 **Default** 的另一个子网用于应当访问托管实例的其他资源（例如 Azure 虚拟机）。 如果不需要 **Default** 子网，可以将其删除。

3. 配置网络环境。 在以下窗体上，可以配置网络环境的参数：

![配置 Azure 网络](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

可以更改 VNet 和子网的名称并调整与网络资源关联的 IP 范围。 按下“购买”按钮后，此窗体将创建并配置你的环境。 如果不需要两个子网，可以删除默认子网。 

## <a name="modify-an-existing-virtual-network-for-managed-instances"></a>根据托管实例修改现有的虚拟网络 

本部分的问题和解答说明如何将托管实例添加到现有的虚拟网络。 

**对现有虚拟网络使用的是经典模型还是资源管理器部署模型？** 

只能在资源管理器虚拟网络中创建托管实例。 

**是要为 SQL 托管实例创建新子网还是使用现有子网？**

若要创建新子网： 

- 根据[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)部分的指导计算子网大小。
- 执行[添加、更改或删除虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)中的步骤。 
- 创建包含单个条目 **0.0.0.0/0** 的路由表作为下一跃点 Internet，并将其关联到托管实例的子网。  

如果希望在现有子网内创建托管实例，建议使用以下 PowerShell 脚本来准备子网。
```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```
子网准备是通过三个简单步骤完成的：

- 验证 - 针对托管实例网络要求验证所选的虚拟网络和子网
- 确认 - 向用户显示为托管实例部署准备子网而需要执行的一组更改并请求用户同意
- 准备 - 正确配置虚拟网络和子网

**是否配置了自定义 DNS 服务器？** 

如果是，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。 

- 创建并关联所需的路由表：请参阅[创建并关联所需的路由表](#create-the-required-route-table-and-associate-it)

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关如何创建 VNet、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-create-tutorial-portal.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
