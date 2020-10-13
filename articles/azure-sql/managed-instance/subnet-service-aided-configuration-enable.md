---
title: 为 Azure SQL 托管实例启用服务辅助子网配置
description: 为 Azure SQL 托管实例启用服务辅助子网配置
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: 67b398194d9094cd99fccaa85ed0df3be362ce2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618006"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-managed-instance"></a>为 Azure SQL 托管实例启用服务辅助子网配置
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

服务辅助子网配置可为承载托管实例的子网提供自动网络配置管理。 使用服务辅助子网配置，用户能完全控制数据访问（TDS 流量），而托管实例负责确保管理流量不间断传输以满足 SLA。

自动配置的网络安全组和路由表规则对客户可见并使用前缀 Microsoft.Sql-managedInstances_UseOnly 进行批注。

在为 `Microsoft.Sql/managedInstances` 资源提供程序开启[子网委派](../../virtual-network/subnet-delegation-overview.md)后，会自动启用服务辅助配置。

> [!IMPORTANT] 
> 开启子网委派后，在从子网中删除最后一个虚拟群集之前，无法将它关闭。 有关如何删除虚拟群集的更多详细信息，请参阅以下[文章](virtual-cluster-delete.md#delete-a-virtual-cluster-from-the-azure-portal)。

> [!NOTE] 
> 由于服务辅助子网配置是维护 SLA 的必要功能，因此从 2020 年 5 月 1 日开始，无法在未委派给托管实例资源提供程序的子网中部署托管实例。 在 2020 年 7 月 1 日，所有包含托管实例的子网都会自动委派给托管实例资源提供程序。 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>为新部署启用子网委派
若要将托管实例部署到空子网中，需要将它委派给 `Microsoft.Sql/managedInstances` 资源提供程序，如以下[文章](../../virtual-network/manage-subnet-delegation.md)中所述。 请注意，引用的文章使用 `Microsoft.DBforPostgreSQL/serversv2` 资源提供程序作为示例。你需要改用 `Microsoft.Sql/managedInstances` 资源提供程序。

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>为现有部署启用子网委派

若要为现有托管实例部署启用子网委派，需要找出它所在的虚拟网络子网。 

若要了解此信息，可以在托管实例的 `Overview` 门户边栏选项卡上查看 `Virtual network/subnet`。

作为替代方法，可以运行以下 PowerShell 命令来了解此信息。 将 subscription-id 替换为自己的订阅 ID。 此外，将 rg-name 替换为托管实例的资源组，并将 mi-name 替换为托管实例的名称。

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

找到托管实例子网后，需要将它委派给 `Microsoft.Sql/managedInstances` 资源提供程序，如以下[文章](../../virtual-network/manage-subnet-delegation.md)中所述。 请注意，引用的文章使用 `Microsoft.DBforPostgreSQL/serversv2` 资源提供程序作为示例。你需要改用 `Microsoft.Sql/managedInstances` 资源提供程序。


> [!IMPORTANT]
> 对于已在子网中的托管实例，启用服务辅助配置不会导致故障转移或连接中断。
