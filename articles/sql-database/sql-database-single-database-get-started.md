---
title: 创建单一数据库 - Azure SQL 数据库 | Microsoft Docs
description: 使用 Azure 门户、PowerShell 和 Azure CLI 在 Azure SQL 数据库中创建和查询单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: ninarn
ms.reviewer: carlrab, sstein
ms.date: 07/29/2019
ms.openlocfilehash: f9c2ed77075affdfdf464dbf8a468629fc2e610d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640038"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal-powershell-and-azure-cli"></a>快速入门：使用 Azure 门户、PowerShell 和 Azure CLI 在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](sql-database-single-database.md)是最快速且最简单的部署选项。 本快速入门介绍如何使用 Azure 门户创建并查询单一数据库。

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。 

执行本快速入门中的所有步骤都需要登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-single-database"></a>创建单一数据库

在预配或无服务器（预览版）计算层中可以创建单一数据库。

- 预配计算层中的单一数据库具有已定义数量的预分配计算资源以及使用两个[购买模型](sql-database-purchase-models.md)之一的一系列内存和存储资源。
- 无服务器计算层中的单一数据库具有一系列自动扩展的计算资源，以及指定数量的每内核内存和指定数量的存储资源，且仅在[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)中提供。

创建单一数据库时，也定义一个 [SQL 数据库服务器](sql-database-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中。

> [!NOTE]
> 本快速入门使用的是[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)，但[基于 DTU 的购买模型](sql-database-service-tiers-DTU.md)也可用。

若要创建包含 Adventure Works LT 示例数据的单一数据库，请执行以下操作：

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="query-the-database"></a>查询数据库

创建数据库后，即可使用 Azure 门户中的内置查询工具连接到数据库并查询数据。

1. 在数据库的 SQL 数据库页的左侧菜单中，选择“查询编辑器(预览)”   。

   ![登录到查询编辑器](./media/sql-database-get-started-portal/query-editor-login.png)

2. 输入你的登录信息，并选择“确定”  。
3. 在“查询编辑器”窗格中输入以下查询  。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. 选择“运行”，然后在“结果”窗格中查看查询结果。  

   ![查询编辑器结果](./media/sql-database-get-started-portal/query-editor-results.png)

5. 关闭“查询编辑器”页，并在系统提示时选择“确定”，以放弃未保存的修改   。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、数据库服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

使用完这些资源后，可以通过如下方式将其删除：

1. 在 Azure 门户的左侧菜单中，选择“资源组”，然后选择“myResourceGroup”   。
2. 在资源组页上，选择“删除资源组”  。
3. 在字段中输入 *myResourceGroup*，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

- 创建服务器级防火墙规则，以便通过本地或远程工具连接到单一数据库。 有关详细信息，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。
- 创建服务器级防火墙规则后，使用多种不同的工具和语言[连接和查询](sql-database-connect-query.md)数据库。
  - [使用 SQL Server Management Studio 连接和查询](sql-database-connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 要使用 Azure CLI 在预配计算层中创建单一数据库，请参阅 [Azure CLI 示例](sql-database-cli-samples.md)。
- 要使用 Azure PowerShell 在预配计算层中创建单一数据库，请参阅 [Azure PowerShell 示例](sql-database-powershell-samples.md)。
- 若要在无服务器计算层中使用 Azure Powershell 创建单一数据库，请参阅[创建无服务器数据库](sql-database-serverless.md#create-new-database-in-serverless-compute-tier)。
