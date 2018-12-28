---
title: Azure SQL 数据库托管实例配置现有 VNet/子网 | Microsoft Docs
description: 本主题介绍了如何配置可在其中部署 Azure SQL 数据库托管实例的现有虚拟网络 (VNet) 和子网。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 53aba5192ddf57598965fcfe0db5f2b18423c7e9
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344998"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置现有 VNet

Azure SQL 数据库托管实例必须部署在专用于托管实例的 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 和子网中。 如果现有 VNet 和子网是根据[托管实例 VNet 要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)配置的，则可以使用现有 VNet 和子网。 

如果你有尚未配置的新子网，并且不确定该子网是否满足[要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)，或者你在进行一些更改后想要检查子网是否仍满足[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)，则可以使用本部分中介绍的脚本验证并修改你的网络。 

  > [!Note]
  > 只能在资源管理器虚拟网络中创建托管实例。 不支持使用经典部署模型部署的 Azure VNet。 请确保按照[确定托管实例的子网大小](#determine-the-size-of-subnet-for-managed-instances)中的准则计算子网大小，因为在子网中部署资源后将无法调整子网大小。

## <a name="validate-and-modify-an-existing-virtual-network"></a>验证并修改现有虚拟网络 

如果希望在现有子网内创建托管实例，建议使用以下 PowerShell 脚本来准备子网：
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

1. 验证 - 针对托管实例网络要求验证所选的虚拟网络和子网。
2. 确认 - 向用户显示为托管实例部署准备子网而需要执行的一组更改并请求用户同意。
3. 准备 - 正确配置虚拟网络和子网。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例](sql-database-managed-instance.md)
- 有关如何创建 VNet、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
