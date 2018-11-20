---
title: Azure 门户：创建 SQL 数据库 | Microsoft Docs
description: 在 Azure 门户中创建 SQL 数据库逻辑服务器、服务器级防火墙规则和数据库，并对其进行查询。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 66ee4ac8fe946696d6760891a086a672fa9fc412
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50914592"
---
# <a name="quickstart-create-an-azure-sql-database-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure SQL 数据库

本快速入门逐步讲解如何使用[基于 DTU 的购买模型](sql-database-service-tiers-dtu.md)在 Azure 中创建 SQL 数据库。 Azure SQL 数据库是一种“数据库即服务”产品，可用于在云中运行和缩放高可用性 SQL Server 数据库。 本快速入门介绍如何开始使用 Azure 门户创建 SQL 数据库，然后对其进行查询。

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

  >[!NOTE]
  >本教程使用的是基于 DTU 的购买模型，但[基于 vCore 的购买模型](sql-database-service-tiers-vcore.md)也可用。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-sql-database"></a>创建 SQL 数据库

创建 Azure SQL 数据库时，会使用定义好的一组[计算和存储资源](sql-database-service-tiers-dtu.md)。 数据库在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)和 [Azure SQL 数据库逻辑服务器](sql-database-features.md)中创建。

请按以下步骤创建包含 Adventure Works LT 示例数据的 SQL 数据库。

1. 在 Azure 门户的左上角单击“创建资源”。

2. 从“新建”页中选择“数据库”，然后从“新建”页的“SQL 数据库”中选择“创建”。

   ![创建数据库 - 1](./media/sql-database-get-started-portal/create-database-1.png)

3. 如上图所示，在“SQL 数据库”窗体中填写以下信息：   

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **数据库名称** | mySampleDatabase | 如需有效的数据库名称，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 |
   | **订阅** | 你的订阅  | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组**  | myResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | **选择源** | 示例 (AdventureWorksLT) | 将 AdventureWorksLT 架构和数据加载到新数据库中 |

   > [!IMPORTANT]
   > 必须选择此表单中的示例数据库，因为本快速入门中的其余部分会用到它。
   >

4. 在“服务器”下，单击“配置所需设置”，并在“SQL Server (逻辑服务器)”窗体中填写以下信息，如下图所示：   

   | 设置       | 建议的值 | 说明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **服务器名称** | 任何全局唯一名称 | 如需有效的服务器名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | 服务器管理员登录名 | 任何有效的名称 | 如需有效的登录名，请参阅 [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)（数据库标识符）。 |
   | **密码** | 任何有效的密码 | 密码必须至少有 8 个字符，且必须包含以下类别中的三个类别的字符：大写字符、小写字符、数字以及非字母数字字符。 |
   | **订阅** | 你的订阅 | 有关订阅的详细信息，请参阅[订阅](https://account.windowsazure.com/Subscriptions)。 |
   | **资源组** | myResourceGroup | 如需有效的资源组名称，请参阅 [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)（命名规则和限制）。 |
   | **位置** | 任何有效的位置 | 有关区域的信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。 |

   > [!IMPORTANT]
   > 此处指定的服务器管理员登录名和密码是以后在本快速入门中登录到服务器及其数据库所必需的。 请牢记或记录此信息，以后会使用到它。
   >  

   ![创建数据库 - 服务器](./media/sql-database-get-started-portal/create-database-server.png)

5. 完成表单操作后，单击“选择”。

6. 单击“定价层”，指定服务层、DTU 数和存储量。 浏览相关选项，了解适用于每个服务层的 DTU 数和存储量。

   > [!IMPORTANT]
   > 除以下区域外，所有区域当前均提供超过 1 TB 的高级层存储：英国北部、美国中西部、英国南部 2、中国东部、USDoDCentral、德国中部、USDoDEast、US Gov 西南部、US Gov 中南部、德国东北部、中国北部、US Gov 东部。 在其他区域，高级层中的最大存储限制为 1 TB。 请参阅 [P11-P15 当前限制]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

7. 对于本快速入门，请选择“标准”服务层，然后使用滑块选择“10 DTU (S0)”和“1”GB 存储。

   ![创建数据库 - s1](./media/sql-database-get-started-portal/create-database-s1.png)

8. 若要使用“附加存储”选项，请接受预览版条款。

   > [!IMPORTANT]
   > 除以下区域外的所有区域当前均提供超过 1 TB 的高级层存储：美国中西部、中国东部、USDoDCentral、USGov Iowa、德国中部、USDoDEast、US Gov 西南部、德国东北部、中国北部。 在其他区域，高级层中的最大存储限制为 1 TB。 请参阅 [P11-P15 当前限制]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)。  

9. 选择服务器层、DTU 数和存储量后，单击“应用”。  

10. 完成 SQL 数据库表单后，即可单击“创建”对数据库进行预配。 预配需要数分钟。

11. 在工具栏上，单击“通知”可监视部署过程。

     ![通知](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-sql-database"></a>查询 SQL 数据库

在 Azure 中创建示例数据库以后，即可使用 Azure 门户中的内置查询工具确认你是否能够连接到数据库并查询数据。

1. 在数据库的“SQL 数据库”页上，单击左侧菜单中的“查询编辑器(预览版)”，然后单击“登录”。

   ![登录](./media/sql-database-get-started-portal/query-editor-login.png)

2. 选择 SQL Server 身份验证，提供所需的登录信息，然后单击“确定”进行登录。

3. 完成 ServerAdmin 身份验证后，在查询编辑器窗格中键入以下查询。

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. 单击“运行”，然后在“结果”窗格中查看查询结果。

   ![查询编辑器结果](./media/sql-database-get-started-portal/query-editor-results.png)

5. 关闭“查询编辑器”页，单击“确定”放弃未保存的编辑内容。

## <a name="clean-up-resources"></a>清理资源

若要转到[后续步骤](#next-steps)，以便了解如何使用多种不同的方法连接和查询数据库，请保存这些资源。 但如果希望删除在本快速入门中创建的资源，请执行以下步骤。


1. 在 Azure 门户的左侧菜单中，单击“资源组”，然后单击“myResourceGroup”。
2. 在资源组页上单击“删除”，在文本框中键入 **myResourceGroup**，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

- 现在你已有数据库，需要创建服务器级防火墙规则以从本地工具连接到该数据库。 请参阅[创建服务器级防火墙规则](sql-database-get-started-portal-firewall.md)
- 如果创建服务器级防火墙规则，可以使用你喜欢的工具或语言进行[连接和查询](sql-database-connect-query.md)，包括
  - [使用 SQL Server Management Studio 连接和查询](sql-database-connect-query-ssms.md)
  - [使用 Azure Data Studio 连接和查询](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 若要使用 Azure CLI 创建数据库，请参阅 [Azure CLI 示例](sql-database-cli-samples.md)
- 若要使用 Azure PowerShell 创建数据库，请参阅 [Azure PowerShell 示例](sql-database-powershell-samples.md)