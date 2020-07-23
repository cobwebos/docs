---
title: 配置现有虚拟网络
titleSuffix: Azure SQL Managed Instance
description: 本文介绍如何配置可在其中部署 Azure SQL 托管实例的现有虚拟网络和子网。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 5d91fc0d5dd51ea5ef766f23519f38202b24a337
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711300"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>为 Azure SQL 托管实例配置现有虚拟网络
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

必须在 Azure[虚拟网络](../../virtual-network/virtual-networks-overview.md)中部署 azure SQL 托管实例，并将专用于仅用于托管实例的子网。 如果基于[SQL 托管实例虚拟网络要求](connectivity-architecture-overview.md#network-requirements)配置了现有虚拟网络和子网，则可以使用该虚拟网络和子网。

如果存在以下情况，可以使用本文中所述的脚本来验证和修改网络：

- 仍未配置某个新子网。
- 不确定该子网是否符合[要求](connectivity-architecture-overview.md#network-requirements)。
- 进行更改后想要检查子网是否仍符合[网络要求](connectivity-architecture-overview.md#network-requirements)。

> [!Note]
> 只能在通过 Azure 资源管理器部署模型创建的虚拟网络中创建托管实例。 不支持通过经典部署模型创建的 Azure 虚拟网络。 按照[确定 SQL 托管实例的子网大小一](vnet-subnet-determine-size.md)文中的准则计算子网大小。 在子网中部署资源后，无法调整子网大小。
>
> 创建托管实例后，不支持将实例或 VNet 移到另一个资源组或订阅。

## <a name="validate-and-modify-an-existing-virtual-network"></a>验证并修改现有虚拟网络

如果要在现有子网中创建托管实例，建议使用以下 PowerShell 脚本来准备子网：

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

该脚本通过三个步骤来准备子网：

1. Validate：验证所选的虚拟网络和子网的 SQL 托管实例网络要求。
2. 确认：向用户显示为 SQL 托管实例部署准备子网所需的一组更改。 同时请求用户同意。
3. 准备：正确配置虚拟网络和子网。

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[什么是 SQL 托管实例？](sql-managed-instance-paas-overview.md)。
- 有关演示如何创建虚拟网络、创建托管实例和从数据库备份还原数据库的教程，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关 DNS 问题，请参阅[配置自定义 DNS](custom-dns-configure.md)。
