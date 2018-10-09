---
title: Azure SQL 数据库托管实例 VNet 配置 | Microsoft Docs
description: 本主题介绍使用 Azure SQL 数据库托管实例的虚拟网络 (VNet) 的配置选项。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 9d3f867dad40017e8e97ec4f5e370533b018263c
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181156"
---
# <a name="configure-a-vnet-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置 VNet

Azure SQL 数据库托管实例必须部署在 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中。 此部署支持以下方案： 
- 直接从本地网络连接到托管实例 
- 将托管实例连接到链接服务器或其他本地数据存储 
- 将托管实例连接到 Azure 资源  

## <a name="plan"></a>计划

使用以下问题的解答，规划如何在虚拟网络中部署托管实例： 
- 计划部署单个还是多个托管实例？ 

  托管实例的数量决定了要分配给托管实例的子网的最小大小。 有关详细信息，请参阅[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)。 
- 是要将托管实例部署到现有的虚拟网络，还是创建新的网络？ 

   如果打算使用现有的虚拟网络，则需要修改该网络的配置，以适应托管实例。 有关详细信息，请参阅[根据托管实例修改现有的虚拟网络](#modify-an-existing-virtual-network-for-managed-instances)。 

   如果打算创建新的虚拟网络，请参阅[为托管实例创建新的虚拟网络](#create-a-new-virtual-network-for-a-managed-instance)。

## <a name="requirements"></a>要求

若要创建托管实例，请在虚拟网络内创建一个符合以下要求的专用子网（托管实例子网）：
- **专用子网**：托管实例子网不能包含与它关联的任何其他云服务，并且不能是网关子网。 无法在包含除托管实例以外的资源的子网中创建托管实例，并且以后无法在子网中添加其他资源。
- **兼容的网络安全组 (NSG)**：与托管实例子网关联的 NSG 必须将下表中显示的规则（强制性入站安全规则和强制性出站安全规则）置于任何其他规则之前。 可以使用某个 NSG 通过筛选端口 1433 上的流量来完全控制对托管实例数据终结点的访问。 
- **兼容的用户定义路由表 (UDR)**：托管实例子网必须有一个用户路由表，此表中 **0.0.0.0/0 Next Hop Internet** 已作为强制 UDR 分配给该子网。 此外，还可以添加 UDR，用以通过虚拟网络网关或虚拟网络设备 (NVA) 来路由以本地专用 IP 范围为目的地的流量。 
- **可选的自定义 DNS**：如果在虚拟网络中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 有关详细信息，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。 自定义 DNS 服务器必须能够解析下列域及其子域中的主机名：*microsoft.com*、*windows.net*、*windows.com*、*msocsp.com*、*digicert.com*、*live.com*、*microsoftonline.com* 和 *microsoftonline-p.com*。 
- **没有服务终结点**：托管实例子网不能有关联的服务终结点。 创建虚拟网络时，请务必禁用“服务终结点”选项。
- **足够的 IP 地址**：托管实例子网必须最少具有 16 个 IP 地址（建议的最小值是 32 个 IP 地址）。 有关详细信息，请参阅[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)

> [!IMPORTANT]
> 如果目标子网不符合上述所有要求，则无法部署新的托管实例。 创建托管实例时，会在子网上应用一个“网络意向策略”，以防止对网络配置进行不符合标准的更改。 从子网中删除最后一个实例后，“网络意向策略”也将被删除

### <a name="mandatory-inbound-security-rules"></a>强制性入站安全规则 

| 名称       |端口                        |协议|来源           |目的地|操作|
|------------|----------------------------|--------|-----------------|-----------|------|
|管理  |9000、9003、1438、1440、1452|TCP     |任意              |任意        |允许 |
|mi_subnet   |任意                         |任意     |MI SUBNET        |任意        |允许 |
|health_probe|任意                         |任意     |AzureLoadBalancer|任意        |允许 |

### <a name="mandatory-outbound-security-rules"></a>强制性出站安全规则 

| 名称       |端口          |协议|来源           |目的地|操作|
|------------|--------------|--------|-----------------|-----------|------|
|管理  |80、443、12000|TCP     |任意              |任意        |允许 |
|mi_subnet   |任意           |任意     |任意              |MI SUBNET  |允许 |

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

## <a name="create-a-new-virtual-network-for-a-managed-instance"></a>为托管实例创建新的虚拟网络

创建和配置虚拟网络的最简单方法是使用 Azure 资源管理器部署模板。

1. 登录到 Azure 门户。

2. 使用“部署到 Azure”按钮在 Azure 云中部署虚拟网络：

  <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="http://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

  此按钮将打开一个窗体，可以使用该窗体来配置你可以在其中部署托管实例的网络环境。

  > [!Note]
  > 此 Azure 资源管理器模板将部署包含两个子网的虚拟网络。 一个名为 **ManagedInstances** 的子网预留给托管实例并且已预配置了路由表，而名为 **Default** 的另一个子网用于应当访问托管实例的其他资源（例如 Azure 虚拟机）。 如果不需要 **Default** 子网，可以将其删除。

3. 配置网络环境。 在以下窗体上，可以配置网络环境的参数：

![配置 Azure 网络](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

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

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关如何创建 VNet、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)
