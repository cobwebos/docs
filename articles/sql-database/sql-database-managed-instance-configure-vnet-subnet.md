---
title: 为 Azure SQL 数据库托管实例配置现有虚拟网络 | Microsoft Docs
description: 本文介绍如何配置可在其中部署 Azure SQL 数据库托管实例的现有虚拟网络和子网。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: c4ff12f0c9adcb9943a6e2426eaf2740ba171e39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60700463"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例配置现有虚拟网络

Azure SQL 数据库托管实例必须部署在专用于托管实例的 Azure [虚拟网络](../virtual-network/virtual-networks-overview.md)和子网中。 如果现有虚拟网络和子网是根据[托管实例虚拟网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)配置的，则可以使用现有虚拟网络和子网。

如果存在以下情况，可以使用本文中所述的脚本来验证和修改网络：

- 仍未配置某个新子网。
- 不确定该子网是否符合[要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。
- 进行更改后想要检查子网是否仍符合[网络要求](sql-database-managed-instance-connectivity-architecture.md#network-requirements)。

> [!Note]
> 只能在通过 Azure 资源管理器部署模型创建的虚拟网络中创建托管实例。 不支持通过经典部署模型创建的 Azure 虚拟网络。 根据[确定托管实例的子网大小](sql-database-managed-instance-determine-size-vnet-subnet.md)一文中的指导计算子网大小。 在子网中部署资源后，无法调整子网大小。

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

该脚本通过三个步骤来准备子网：

1. 验证：根据托管实例的网络要求验证所选虚拟网络和子网。
2. 确认：向用户显示为托管实例部署准备子网而需要执行的一组更改。 同时请求用户同意。
3. 准备：正确配置虚拟网络和子网。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是托管实例？](sql-database-managed-instance.md)。
- 有关如何创建虚拟网络、创建托管实例，以及从数据库备份还原数据库的教程，请参阅[创建 Azure SQL 数据库托管实例](sql-database-managed-instance-get-started.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](sql-database-managed-instance-custom-dns.md)。
