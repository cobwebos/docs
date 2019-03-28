---
title: 适用于 SQL 数据库的 Azure CLI 脚本示例 | Microsoft Docs
description: 创建和管理 Azure SQL 数据库服务器、弹性池、数据库和防火墙的 Azure CLI 脚本示例。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: e3a75acb6d591a9fdf1e632369cc2e9ae6b6099d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447837"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>适用于 Azure SQL 数据库的 Azure CLI 示例

可以使用 <a href="/cli/azure">Azure CLI</a> 配置 Azure SQL 数据库。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="single-database--elastic-pools"></a>单一数据库和弹性池

下表包括 Azure SQL 数据库的 Azure CLI 脚本示例的链接。

| |  |
|---|---|
|**创建单一数据库和弹性池**||
| [创建单一数据库和配置防火墙规则](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 脚本示例创建单个 Azure SQL 数据库并配置服务器级防火墙规则。 |
| [创建弹性池并移动入池数据库](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 脚本示例创建 SQL 弹性池，移动入池 Azure SQL 数据库，并更改计算大小。|
|**缩放单一数据库和弹性池**||
| [缩放单一数据库](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 脚本示例在查询数据库的大小信息后，将单个 Azure SQL 数据库缩放为不同的计算大小。 |
| [缩放弹性池](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | 此 CLI 脚本示例将 SQL 弹性池缩放为不同的计算大小。  |
|||

详细了解[单一数据库 Azure CLI API](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)。

## <a name="managed-instance"></a>托管实例

下表包括适用于 Azure SQL 数据库 - 托管实例的 Azure CLI 脚本示例的链接。

| |  |
|---|---|
| [创建托管实例](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | 此 CLI 脚本展示了如何创建托管实例。 |
| [更新托管实例](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | 此 CLI 脚本展示了如何更新托管实例。 |
| [将数据库移动到另一个托管实例](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | 此 CLI 脚本展示了如何将数据库的备份从一个实例还原到另一个实例。 |
|||

详细了解[托管实例 Azure CLI API](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) 并[在此处查找更多示例](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。
