---
title: 为 Azure SQL 数据库托管实例启用服务辅助子网配置
description: 为 Azure SQL 数据库托管实例启用服务辅助子网配置
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79533260"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例启用服务辅助子网配置
服务辅助子网配置为承载托管实例的子网提供自动网络配置管理。 使用服务辅助子网配置，用户可以完全控制对数据的访问（TDS 流量流），而托管实例则需要负责确保管理流量不间断流动，以满足 SLA。

自动配置的网络安全组和路由表规则对客户可见并带有前缀_managedInstances_UseOnly__。

启用资源提供程序的`Microsoft.Sql/managedInstances` [子网委派](../virtual-network/subnet-delegation-overview.md)后，会自动启用服务辅助配置。

> [!IMPORTANT] 
> 启用子网委派后，在从子网中删除最后一个虚拟群集后，才能将其关闭。 有关如何删除虚拟群集的详细信息，请参阅以下[文章](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 由于服务辅助子网配置是维护 SLA 的重要功能，从第一个2020开始，因此无法在未委派给托管实例资源提供程序的子网中部署托管实例。 7月1日2020，所有包含托管实例的子网将自动委托给托管实例资源提供程序。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>为新部署启用子网委托
若要将托管实例部署到空子网，你需要将`Microsoft.Sql/managedInstances`其委托给资源提供程序，如以下[文章](../virtual-network/manage-subnet-delegation.md)中所述。 _请注意，引用项目使用`Microsoft.DBforPostgreSQL/serversv2`资源提供程序（例如）。你需要改用`Microsoft.Sql/managedInstances`资源提供程序。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>为现有部署启用子网委托

若要为现有的托管实例部署启用子网委托，需要在其中放置虚拟网络子网。 

要了解这一点，可以`Virtual network/subnet`查看托管`Overview`实例的门户边栏选项卡。

作为替代方法，你可以运行以下 PowerShell 命令来了解这一点。 将**订阅**id 替换为你的订阅 id。 将 **rg-name** 替换为托管实例的资源组，将 **mi-name** 替换为托管实例的名称。

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

找到托管实例子网后，需要将其委托给`Microsoft.Sql/managedInstances`资源提供程序，如以下[文章](../virtual-network/manage-subnet-delegation.md)中所述。 _请注意，引用项目使用`Microsoft.DBforPostgreSQL/serversv2`资源提供程序（例如）。你需要改用`Microsoft.Sql/managedInstances`资源提供程序。_


> [!IMPORTANT]
> 对于已在子网中的托管实例，启用服务辅助配置不会导致故障转移或中断连接。
