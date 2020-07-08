---
title: Azure SQL 托管实例的管理 API 参考
description: 了解如何创建和配置 Azure SQL 托管实例的托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3f0eb2c1ecb26475b25cd5ca41c9c61d5e2b47a3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695567"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>适用于 Azure SQL 托管实例的托管 API 参考
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

可以使用 Azure 门户、PowerShell、Azure CLI、REST API 和 Transact-sql 创建和配置 Azure SQL 托管实例的托管实例。 在本文中，可以找到可用于创建和配置托管实例的函数和 API 的概述。

## <a name="azure-portal-create-a-managed-instance"></a>Azure 门户：创建托管实例

有关演示如何创建托管实例的快速入门，请参阅[快速入门：创建托管实例](instance-create-quickstart.md)。

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell：创建和配置托管实例

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRM 模块中的命令的参数完全相同。

若要使用 Azure PowerShell 创建并管理托管实例，请使用以下 PowerShell cmdlet。 如果需要安装或升级 PowerShell，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

> [!TIP]
> 有关 PowerShell 示例脚本，请参阅[快速入门脚本：使用 PowerShell 库创建托管实例](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../quick-start-script-create-azure-sql-managed-instance-using-powershell/)。

| Cmdlet | 说明 |
| --- | --- |
|[New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)|创建托管实例。 |
|[Get-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstance)|返回有关托管实例的信息。|
|[Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)|设置托管实例的属性。|
|[Remove-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstance)|删除托管实例。|
|[New-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstancedatabase)|创建 SQL 托管实例数据库。|
|[Get-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabase)|返回有关 SQL 托管实例数据库的信息。|
|[Remove-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabase)|删除 SQL 托管实例数据库。|
|[Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)|还原 SQL 托管实例数据库。|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI：创建和配置托管实例

若要使用[Azure CLI](/cli/azure)创建和配置托管实例，请[对 SQL 托管实例使用以下 Azure CLI 命令](/cli/azure/sql/mi)。 使用[Azure Cloud Shell](/azure/cloud-shell/overview)在浏览器中运行 CLI，或者在 MacOS、Linux 或 Windows 上[安装](/cli/azure/install-azure-cli)它。

> [!TIP]
> 有关 Azure CLI 快速入门，请参阅[使用 Azure CLI 处理 SQL 托管实例](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)。

| Cmdlet | 说明 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |创建托管实例。|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|列出可用的托管实例。|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|获取托管实例的详细信息。|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|更新托管实例。|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|删除托管实例。|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |创建托管数据库。|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|列出可用的托管数据库。|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|还原托管的数据库。|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|删除托管数据库。|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-sql：创建和配置实例数据库

若要在创建托管实例之后创建和配置实例数据库，请使用以下 T-sql 命令。 您可以使用 Azure 门户、 [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)、 [Visual Studio Code](https://code.visualstudio.com/docs)或任何其他可以连接到服务器并传递 transact-sql 命令的程序来发出这些命令。

> [!TIP]
> 有关演示如何使用 Microsoft Windows 上的 SQL Server Management Studio 配置和连接到托管实例的快速入门，请参阅[快速入门：配置 AZURE VM 以连接到 AZURE sql 托管实例](connect-vm-instance-configure.md)和[快速入门：配置从本地到 azure sql 托管实例的点到站点连接](point-to-site-p2s-configure.md)。

> [!IMPORTANT]
> 不能使用 Transact-sql 创建或删除托管实例。

| Command | 描述 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|在 SQL 托管实例中创建新实例数据库。 您必须连接到 master 数据库才能创建新的数据库。|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |修改 SQL 托管实例中的实例数据库。|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API：创建和配置托管实例

若要创建和配置托管实例，请使用这些 REST API 请求。

| Command | 描述 |
| --- | --- |
|[托管实例 - 创建或更新](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|创建或更新托管实例。|
|[托管实例 - 删除](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|删除托管实例。|
|[托管实例 - 获取](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|获取托管实例。|
|[托管实例 - 列表](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|返回订阅中的托管实例的列表。|
|[托管实例 - 按资源组列出](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|返回资源组中的托管实例的列表。|
|[托管实例 - 更新](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|更新托管实例。|

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何将 SQL Server 数据库迁移到 Azure，请参阅[迁移到 Azure SQL 数据库](../database/migrate-to-database-from-sql-server.md)。
- 有关支持的功能的信息，请参阅[功能](../database/features-comparison.md)。
