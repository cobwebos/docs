---
title: 监视管理操作
titleSuffix: Azure SQL Managed Instance
description: 了解监视 Azure SQL 托管实例管理操作的不同方式。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994275"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>监视 Azure SQL 托管实例管理操作
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 托管实例提供对 [管理操作](management-operations-overview.md) 的监视，你可以使用这些操作来部署新的托管实例，更新实例属性，或在不再需要时删除实例。 

## <a name="overview"></a>概述

所有管理操作可分类为：

- 部署实例（创建新实例）。
- 实例更新（更改实例属性，例如 vCore 数或预留存储）。
- 删除实例。

大多数管理操作都是 [长时间运行的操作](management-operations-overview.md#duration)。 因此，需要监视状态或跟踪操作步骤的进度。 

可以通过多种方式来监视托管实例管理操作：

- [资源组部署](../../azure-resource-manager/templates/deployment-history.md)
- [活动日志](../../azure-monitor/platform/activity-log.md)
- [托管实例操作 API](#managed-instance-operations-api)


下表比较了管理操作监视选项： 

| 选项 | 保留 | 支持取消 | 创建 | 更新 | 删除 | 取消 | 步骤 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 资源组部署 | 无限<sup>1</sup> | 否<sup>2</sup> | 可见 | 可见 | 不可见 | 可见 | 不可见 |
| 活动日志 | 90 天 | 否 | 可见 | 可见 | 可见 | 可见 |  不可见 |
| 托管实例操作 API | 24 小时 | [是](management-operations-cancel.md) | 可见 | 可见 | 可见 | 可见 | 可见 |
|  |  |  |  |  |  |  | |

<sup>1</sup> 资源组的部署历史记录限制为800个部署。

<sup>2</sup> 资源组部署支持取消操作。 但是，由于取消逻辑，将只会取消执行 "取消" 操作后计划部署的操作。 取消资源组部署时，不会取消正在进行的部署。 由于托管实例部署由 Azure 资源管理器透视) 中的一个长时间运行步骤 (，因此取消资源组部署将不会取消托管实例部署，并且操作将完成。 

## <a name="managed-instance-operations-api"></a>托管实例操作 API

管理操作 Api 专门设计用于监视操作。 监视托管实例操作可提供有关操作参数和操作步骤的见解，以及 [取消特定操作](management-operations-cancel.md)。 除了操作详细信息和取消命令外，此 API 可用于具有多个资源部署的自动化脚本中-基于进度步骤，你可以开始某些依赖资源部署。

以下是 Api： 

| Command | 说明 |
| --- | --- |
|[托管实例操作-获取](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|获取对托管实例的管理操作。|
|[托管实例操作-取消](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|取消对托管实例的异步操作。|
|[托管实例操作-按托管实例列出](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|获取对托管实例执行的操作的列表。|

> [!NOTE]
> 使用 API 版本2020-02-02 查看操作列表中的托管实例创建操作。 这是 Azure 门户中使用的默认版本以及最新的 PowerShell 和 Azure CLI 包。

## <a name="monitor-operations"></a>监视操作

# <a name="portal"></a>[门户](#tab/azure-portal)

在 Azure 门户中，使用 "托管实例 **概述** " 页可以监视托管实例操作。 

例如，在 "**概述**" 页上的创建过程开始时，将显示**创建操作**： 

![托管实例创建进度](./media/management-operations-monitor/monitoring-create-operation.png)

选择 " **正在进行的操作** " 打开 **正在进行的操作** 页，并查看 **创建** 或 **更新** 操作。 也可以从此页面 [取消](management-operations-cancel.md) 操作。  

![托管实例操作详细信息](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> [可以取消](management-operations-cancel.md)通过 Azure 门户、PowerShell、Azure CLI 或使用 REST API 版本2020-02-02 的其他工具提交的创建操作。 REST API 版本低于2020-02-02 的版本将启动实例部署，但部署不会在操作 API 中列出，并且不能取消。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation cmdlet 获取有关托管实例上的操作的信息。 您可以通过提供操作名称，查看托管实例上的所有操作或查看特定的操作。

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

有关详细的命令说明，请参阅 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az sql mi op 列表获取对托管实例执行的操作的列表。 如果尚未安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

有关详细的命令说明，请参阅 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)。

---

## <a name="next-steps"></a>后续步骤

- 若要了解如何创建第一个托管实例，请参阅[快速入门指南](instance-create-quickstart.md)。
- 有关功能和比较列表，请参阅 [通用 SQL 功能](../database/features-comparison.md)。
- 有关 VNet 配置的详细信息，请参阅 [SQL 托管实例 VNet 配置](connectivity-architecture-overview.md)。
- 有关创建托管实例以及从备份文件还原数据库的快速入门，请参阅[创建托管实例](instance-create-quickstart.md)。
- 有关使用 Azure 数据库迁移服务进行迁移的教程，请参阅[使用数据库迁移服务进行 SQL 托管实例迁移](../../dms/tutorial-sql-server-to-managed-instance.md)。
