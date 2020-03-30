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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533260"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>为 Azure SQL 数据库托管实例启用服务辅助子网配置
服务辅助子网配置为托管托管实例的子网提供自动化网络配置管理。 通过服务辅助子网配置，用户可以完全控制对数据的访问（TDS 流量流），而托管实例则负责确保管理流量的不间断流动，以实现 SLA。

自动配置的网络安全组和路由表规则对客户可见，并带有前缀_Microsoft.Sql-managedInstances_UseOnly_*的注释。

打开`Microsoft.Sql/managedInstances`资源提供程序的[子网委派](../virtual-network/subnet-delegation-overview.md)后，将自动启用服务辅助配置。

> [!IMPORTANT] 
> 启用子网委派后，在从子网中删除最后一个虚拟群集之前，无法将其关闭。 有关如何删除虚拟群集的更多详细信息，请参阅以下[文章](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 由于服务辅助子网配置是维护 SLA 的基本功能，因此从 2020 年 5 月 1 日起，将无法在未委派给托管实例资源提供程序的子网中部署托管实例。 2020 年 7 月 1 日，包含托管实例的所有子网将自动委派给托管实例资源提供程序。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>为新部署启用子网委派
要将托管实例部署到空子网，您需要将其委托给`Microsoft.Sql/managedInstances`资源提供程序，如下[文所述](../virtual-network/manage-subnet-delegation.md)。 _请注意，引用的文章使用`Microsoft.DBforPostgreSQL/serversv2`资源提供程序例如。您需要改用`Microsoft.Sql/managedInstances`资源提供程序。_

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>为现有部署启用子网委派

为了为现有托管实例部署启用子网委派，您需要找出虚拟网络子网的放置位置。 

要了解这一点，`Virtual network/subnet`您可以在托管`Overview`实例的门户边栏选项卡上进行检查。

作为替代方法，您可以运行以下 PowerShell 命令来了解这一点。 将**订阅 ID**替换为订阅 ID。 将 **rg-name** 替换为托管实例的资源组，将 **mi-name** 替换为托管实例的名称。

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

找到托管实例子网后，需要将其委托给`Microsoft.Sql/managedInstances`资源提供程序，如下[文所述](../virtual-network/manage-subnet-delegation.md)。 _请注意，引用的文章使用`Microsoft.DBforPostgreSQL/serversv2`资源提供程序例如。您需要改用`Microsoft.Sql/managedInstances`资源提供程序。_


> [!IMPORTANT]
> 启用服务辅助配置不会导致子网中已有的托管实例的故障转移或连接中断。
