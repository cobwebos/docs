---
title: Azure 门户：创建单一数据库 - Azure SQL 数据库 | Microsoft Docs
description: 使用 Azure 门户在 Azure SQL 数据库中创建和查询单一数据库。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 66640bd60f6ba8c2f612a6bc9b88a07b13012997
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990028"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure SQL 数据库中创建单一数据库

在 Azure SQL 数据库中创建数据库时，创建[单一数据库](sql-database-single-database.md)是最快速且最简单的部署选项。 本快速入门介绍如何使用 Azure 门户创建并查询单一数据库。

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

执行本快速入门中的所有步骤都需要登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-single-database"></a>创建单一数据库

单一数据库有一组通过两个[购买模型] (sql-database-purchase-models.md)中的一个定义的计算、内存和存储资源。 创建单一数据库时，也定义一个 [SQL 数据库服务器](sql-database-servers.md)来管理它并将它放置在指定区域的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中。

若要创建包含 Adventure Works LT 示例数据的单一数据库，请执行以下操作：

1. 在 Azure 门户的左上角选择“创建资源”。
2. 选择“数据库”，然后选择“SQL 数据库”。
3. 在“创建 SQL 数据库”窗体中，键入或选择以下值：

   - **数据库名称**：输入 *mySampleDatabase*。
   - **订阅**：下拉列表并选择正确的订阅（如果它没有出现）。
   - **资源组**：选择“新建”，键入 myResourceGroup，然后选择“确定”。
   - **选择源**：下拉列表并选择“示例(AdventureWorksLT)”。

    >[!IMPORTANT]
    >确保选择“示例(AdventureWorksLT)”数据，这样就可以轻松地按照本指南以及使用此数据的其他 Azure SQL 数据库快速入门进行操作。
  
   ![创建单一数据库](./media/sql-database-get-started-portal/create-database-1.png)

4. 在“服务器”下，选择“新建”。
5. 在“新建服务器”窗体中，键入或选择以下值：

   - **服务器名称**：输入 *mysqlserver*。
   - **服务器管理员登录名**：键入“azureuser”。
   - **密码**：输入 *Azure1234567*。
   - **确认密码**：重新键入密码。
   - **位置**：下拉列表并选择任何有效位置。  

   >[!IMPORTANT]
   >请记得记录服务器管理员登录名和密码，以便可以登录服务器和数据库以获取此快速入门和其他快速入门。 如果忘记了登录名或密码，可在“SQL 服务器”页上获取登录名或重置密码。 若要打开“SQL 服务器”页，请在创建数据库后在数据库“概述”页上选择服务器名称。

    ![创建服务器](./media/sql-database-get-started-portal/create-database-server.png)

6. 选择“选择”。
7. 在“SQL 数据库”窗体中，选择“定价层”。 浏览每个服务层可用的 DTU 数和存储量。

   >[!NOTE]
   >本快速入门使用的是[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)，但[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)也可用。
   >[!IMPORTANT]
   >除以下区域外，其他所有区域的高级层目前均可提供超过 1 TB 的存储：英国北部、美国中西部、英国南部 2、中国东部、USDoDCentral、德国中部、USDoDEast、US Gov 西南部、US Gov 中南部、德国东北部、中国北部和 US Gov 东部。 在这些区域，高级层中的最大存储限制为 1 TB。 有关详细信息，请参阅[P11-P15 当前限制](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

8. 对于本快速入门，请选择“标准”服务层，然后使用滑块选择“10 DTU (S0)”和 1 GB 存储。
9. 选择“应用”。  

   ![选择定价](./media/sql-database-get-started-portal/create-database-s1.png)

10. 在“SQL 数据库”窗体中，选择“创建”来部署和预配资源组、服务器和数据库。

   部署需要几分钟时间。 可在工具栏上选择“通知”，以监视部署过程。

   ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-database"></a>查询数据库

创建数据库后，即可使用 Azure 门户中的内置查询工具连接到数据库并查询数据。

1. 在数据库的 SQL 数据库页的左侧菜单中，选择“查询编辑器(预览)”。

   ![登录到查询编辑器](./media/sql-database-get-started-portal/query-editor-login.png)

2. 输入你的登录信息，并选择“确定”。
3. 在“查询编辑器”窗格中输入以下查询。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. 选择“运行”，然后在“结果”窗格中查看查询结果。

   ![查询编辑器结果](./media/sql-database-get-started-portal/query-editor-results.png)

5. 关闭“查询编辑器”页，并在系统提示时选择“确定”，以放弃未保存的修改。

## <a name="clean-up-resources"></a>清理资源

如果希望转到[后续步骤](#next-steps)，请保留此资源组、数据库服务器和单一数据库。 后续步骤展示了如何使用各种方法连接和查询数据库。

使用完这些资源后，可以通过如下方式将其删除：

1. 在 Azure 门户的左侧菜单中，选择“资源组”，然后选择“myResourceGroup”。
2. 在资源组页上，选择“删除资源组”。
3. 在字段中输入 *myResourceGroup*，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

- 创建服务器级防火墙规则，以便通过本地或远程工具连接到单一数据库。 有关详细信息，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。
- 创建服务器级防火墙规则后，使用多种不同的工具和语言[连接和查询](sql-database-connect-query.md)数据库。
  - [使用 SQL Server Management Studio 连接和查询](sql-database-connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 若要使用 Azure CLI 创建单一数据库，请参阅 [Azure CLI 示例](sql-database-cli-samples.md)。
- 若要使用 Azure PowerShell 创建单一数据库，请参阅 [Azure PowerShell 示例](sql-database-powershell-samples.md)。
