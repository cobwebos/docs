---
title: "在 Microsoft Azure 中发现、识别个人数据并对其进行分类 | Microsoft Docs"
description: "了解有关搜索、分类、发现和识别数据的信息"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 953df2f0dbbccc153b0a4206c9c0b5f5ea9a51a7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="discover-identify-and-classify-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中发现、识别个人数据并对其进行分类

本文介绍如何在多个 Azure 工具和服务中发现、识别个人数据并对其进行分类，包括 Azure 数据目录、Azure Active Directory、SQL 数据库、用于 Azure HDInsight 中 Hadoop 群集的 Power Query、Azure 信息保护、Azure 搜索和用于 Azure Cosmos DB 的 SQL 查询。

## <a name="scenario-problem-statement-and-goal"></a>情景、问题陈述和目标

一个总部位于美国的体育公司收集各种个人数据和其他数据。 包括客户和员工数据。 该公司将数据保存在多个数据库，并存储在其 Azure 环境中的不同位置。 除了出售体育设备，他们还主持和管理全世界（包括欧盟）精英体育赛事的注册，某些情况下收集的客户数据包括医疗信息。

因为公司每年主持许多国际自行车比赛，并且在世界各地有职工队伍，所以有几个非常大的数据集。 该公司还有客户和员工使用的开发人员生成的应用程序。

该公司希望解决以下问题：

- 客户和员工的个人数据必须区分于公司收集的其他数据，确保正确访问和安全性。
- 数据管理员需要跨 Azure 环境的多个区域轻松发现客户个人数据的位置。
- 必须标记共享文档和电子邮件通信中的客户和员工个人数据，帮助确保数据安全。
- 公司的应用开发人员需要能够在其 Web 和移动应用中轻松搜索客户和员工个人数据。
- 开发人员还需查询其文档数据库的个人数据。

### <a name="company-goals"></a>公司目标

- 所有客户和员工的个人数据均须在 Azure 数据目录中进行标记/批注，以便轻松找到。 理想情况下，分开标记/批注客户和员工的个人数据。
- 必须可以轻松找到来自客户和员工用户配置文件的个人数据和位于 Azure Active Directory 中的工作信息。
- 必须可以轻松查询位于多个 SQL 数据库的个人数据。 
- 公司的某些大型数据集通过 Azure HDInsight 进行管理并存储在 Hadoop 中。 它们必须导入到 Excel，从而可以查询个人数据。
- 文档和电子邮件通信中共享的个人数据必须进行分类、标记，并通过 Azure 信息保护进行保护。
- 公司的应用开发人员必须能够在生成的应用中发现客户和员工个人数据，这可通过 Azure 搜索完成。
- 开发人员必须能够在其文档数据库中找到个人数据。

## <a name="azure-active-directory-data-discovery"></a>Azure Active Directory：数据发现

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的基于云的多租户目录和标识管理服务。 在 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 环境中，可以通过使用 [Azure 门户](https://portal.azure.com/)查找客户和员工用户配置文件和包含个人数据的用户工作信息。

如果想对特定用户查找或更改个人数据时这格外有用。 还可添加或更改用户配置文件和工作信息。 必须使用目录的全局管理员帐户登录。

### <a name="how-do-i-locate-or-view-user-profile-and-work-information"></a>如何查找或查看用户配置文件和工作信息？

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“更多服务”，在文本框中输入“用户和组”，并选择 **Enter**。

   ![如何查找用户配置文件和工作信息](media/how-to-discover-classify-personal-data-azure/user-profile.png)

3. 在“用户和组”边栏选项卡中，选择“用户”。

  ![打开用户和组](media/how-to-discover-classify-personal-data-azure/users-groups.png)

4. 在“用户和组 - 用户”边栏选项卡上，从列表中选择一个用户，然后在选定用户的边栏选项卡上，选择“配置文件”查看可能包含个人数据的用户配置文件信息。

  ![选择用户](media/how-to-discover-classify-personal-data-azure/select-user.png)

5. 如果需要添加或更改用户配置文件信息，可在添加或修改后在命令栏中选择“保存”。
6. 在选定用户的边栏选项卡上，选择“工作信息”查看可能包含个人数据的用户工作信息。

 ![查看工作信息](media/how-to-discover-classify-personal-data-azure/work-info.png)

7. 如果需要添加或更改用户工作信息，可在添加或修改后在命令栏中选择“保存”。

## <a name="azure-sql-database-data-discovery"></a>Azure SQL 数据库：数据发现

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50)是一个云数据库，可帮助开发人员生成和维护应用程序。 可以使用标准 SQL 查询在 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50)中查找个人数据。 Azure SQL 弹性查询（预览）使用户可以执行跨数据库查询。

详细的 [SQL 数据库](../sql-database/sql-database-technical-overview.md)教程介绍使用 SQL 数据库的多个方面，包括如何生成数据库和运行数据查询。 以下是该教程信息的摘要，其中包括指向特定部分的链接。

### <a name="how-do-i-build-a-sql-database"></a>如何生成 SQL 数据库？

可通过三种方式执行：

- 可在 [Azure 门户](https://portal.azure.com/)中创建 Azure SQL 数据库。 教程中将使用资源组和逻辑服务器中一组特定的计算和存储资源。 将使用来自名为 AdventureWorks 的虚拟公司的示例数据。 还将创建服务器级防火墙规则。 若要了解如果操作，请参阅[在 Azure 门户中创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)教程。

  ![创建 Azure SQL 数据库](media/how-to-discover-classify-personal-data-azure/create-database.png)
- 还可在 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) CLI（一个基于浏览器的命令行工具）中创建 SQL 数据库。 此工具在 Azure 门户中可用，且可直接从中运行。 在本教程中启动工具、定义脚本变量、创建资源组和逻辑服务器并配置服务器防火墙规则。 然后使用示例数据创建数据库。 若要了解如何使用此方法创建数据库，请参阅[使用 Azure CLI 创建单个 Azure SQL 数据库](../sql-database/sql-database-get-started-cli.md)教程。

  ![CLIT 教程](media/how-to-discover-classify-personal-data-azure/cli-tutorial.png)

>[!NOTE]
Azure CLI 通常由 Linux 管理员和开发者使用。 一些用户发现这比 PowerShell（第三个选项）更容易且更直观。

- 最后，可以使用 PowerShell 创建 SQL 数据库，它是用来创建和管理 Azure 和其他资源的命令行/脚本工具。 在本教程中启动工具、定义脚本变量、创建资源组和逻辑服务器并配置服务器防火墙规则。 然后使用示例数据创建数据库。

教程需要 Azure PowerShell 模块 4.0 或更高版本。 运行 Get-Module -ListAvailable AzureRM 查找版本。 如果需要进行安装或升级，请参阅《安装 Azure PowerShell 模块》。

```PowerShell
New-AzureRmSQLDatabase -ResourceGroupName $resourcegroupname `
-ServerName $servername `
-DatabaseName $databasename `
-RequestedServiceObjectiveName "s0"
```

若要了解如何使用此方法创建数据库，请参阅[使用 PowerShell 创建单一 Azure SQL 数据库](../sql-database/sql-database-get-started-powershell.md)教程。

>[!Note]
Windows 管理员倾向于使用 PowerShell，但是其中一些更喜欢 Azure CLI。

### <a name="how-do-i-search-for-personal-data-in-sql-database-in-the-azure-portal"></a>如何在 Azure 门户中搜索 SQL 数据库中的个人数据？**

可以使用 Azure 门户中内置的查询编辑器工具搜索个人数据。 使用 SQL 服务器管理员登录名和密码登录该工具，然后输入查询。

  ![使用门户搜索 sql](media/how-to-discover-classify-personal-data-azure/search-sql-portal.png)

教程的第 5 步显示查询编辑器窗格中的示例查询，但其重点不在个人或敏感信息上（它还结合来自两个表格的数据并为返回的数据集中的源列创建别名）。 下面的屏幕快照显示第 5 个步骤中的查询以及返回的结果窗格：

  ![查询编辑器](media/how-to-discover-classify-personal-data-azure/query-editor.png)

如果数据库名为 MyTable，则个人信息的示例查询可能包括姓名、社会安全号码和 ID 号码，如下所示：

“SELECT Name, SSN, ID number FROM MyTable”

运行查询，然后在“结果”窗格中查看结果。

若要详细了解如何在 Azure 门户中查询 SQL 数据库，请参阅教程的[查询 SQL 数据库](../sql-database/sql-database-get-started-portal.md)部分。

### <a name="how-do-i-search-for-data-across-multiple-databases"></a>如何跨多个数据库搜索数据？

SQL 弹性查询（预览）可以执行跨数据库和多个数据的查询并返回单一结果。 [教程概述](../sql-database/sql-database-elastic-query-overview.md)包括方案的详细介绍并解释垂直和水平数据库分区的不同。 水平分区称为“分片”。

  ![垂直分区](media/how-to-discover-classify-personal-data-azure/vertical-partition.png)

  ![水平分区](media/how-to-discover-classify-personal-data-azure/horizontal.png)

若要开始，请参阅 [Azure SQL 数据库弹性查询概述（预览版）](../sql-database/sql-database-elastic-query-overview.md)页。

#### <a name="power-query-for-importing-azure-hdinsight-hadoop-clusters-data-discovery-for-large-data-sets"></a>Power Query（用于导入 Azure HDInsight Hadoop 群集）：大型数据集的数据发现

Hadoop 是用于大型数据集的开源 Apache 存储和处理服务，该数据集在 Hadoop 中进行分析和存储。 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) 允许用户在 Azure 中处理 Hadoop 群集。 Power Query 是 Excel 加载项，帮助用户发现来自不同源的数据等。

与 Azure HDInsight 中 Hadoop 群集关联的个人数据可通过 Power Query 导入 Excel。 数据处于 Excel 中后，可以使用查询来识别它。

#### <a name="how-do-i-use-excel-power-query-to-import-hadoop-clusters-in-azure-hdinsight-into-excel"></a>如何使用 Excel Power Query 将 Azure HDInsight 中的 Hadoop 群集导入 Excel？

HDInsight 教程将引导你完成整个过程。 该教程介绍先决条件，并附有指向 [Azure HDInsight 入门](../hdinsight/hdinsight-hadoop-linux-tutorial-get-started.md)教程的链接。 教程说明涵盖 Excel 2016、2013 和 2010（步骤和更早版本的 Excel 略有不同）。 如果没有 Excel Power Query 加载项，教程会介绍如何获取。 在 Excel 中开始完成教程，并且需有与群集关联的 Azure Blob 存储帐户。

  ![在 Excel 中查询](media/how-to-discover-classify-personal-data-azure/excel.png)

若要了解如何操作，请参阅[使用 Power Query 将 Excel 连接到 Hadoop](../hdinsight/hdinsight-connect-excel-power-query.md)教程。

来源：[使用 Power Query 将 Excel 连接到 Hadoop](../hdinsight/hdinsight-connect-excel-power-query.md)

## <a name="azure-information-protection-personal-data-classification-for-documents-and-email"></a>Azure 信息保护：文档和电子邮件的个人信息分类

[Azure 信息保护](https://www.microsoft.com/cloud-platform/azure-information-protection)可以帮助 Azure 客户应用标签来分类和保护内部或外部共享的文档和电子邮件通信。 其中某些项可能包含客户或员工的个人信息。 管理员或用户可以自动或手动定义规则和条件。 例如，如果用户正在保存包括信用卡信息的文档，他/她将看见管理者配置的标记建议。

### <a name="how-do-i-try-it"></a>如何尝试？

如果希望尝试 Azure 信息保护看看它是否适用于组织，请参阅[快速入门教程](https://docs.microsoft.com/information-protection/get-started/infoprotect-quick-start-tutorial)。 该教程通过实际操作指导完成五个基本步骤 - 安装、配置策略、查看分类、进行标记、共享，耗时不到半小时。

### <a name="how-do-i-deploy-it"></a>如何部署？

如果希望为组织部署 Azure 信息保护，请参阅[分类、标记和保护部署路线图](https://docs.microsoft.com/information-protection/plan-design/deployment-roadmap)。

### <a name="is-there-anything-else-i-should-know"></a>是否有其它我应知道的内容？

有关帮助明确如何设置的补充信息，请参阅[就绪、设置、保护！](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)
博客文章。 并检查列在下面的“了解详细信息”链接，详细了解 Azure 信息保护。

## <a name="azure-search-data-discovery-for-developer-apps"></a>Azure 搜索：开发人员应用的数据发现

[Azure 搜索](https://azure.microsoft.com/services/search/)是用于开发人员的云搜索解决方案，为应用程序提供丰富的数据搜索体验。 Azure 搜索允许跨用户定义的索引查找源自 Azure Cosmo DB、Azure SQL 数据库、Azure Blob 存储、Azure 表存储的数据或自定义客户 JSON 数据。 还可使用 Azure 搜索 REST API 构建 Lucene 查询来搜索个人数据类型或特定个人的个人数据。 功能包括全文搜索、简单的查询语法和 Lucene 查询语法。 

## <a name="how-do-i-use-sql-to-query-data"></a>如何使用 SQL 查询数据？

若要从基础开始，请访问 [Azure CosmosD DB：如何使用 SQL 进行查询](../cosmos-db/tutorial-query-documentdb.md)教程。 该教程提供一个示例文档及两个示例 SQL 查询和结果。

有关构建 SQL 查询的深度指南，请参阅[用于 Azure Cosmos DB Document DB API 的 SQL 查询](../cosmos-db/documentdb-sql-query.md)

如果是 Azure Cosmos DB 新手并希望了解如何创建数据库、添加集合和数据，请参阅 [Azure Cosmos DB：生成 DocumentDB API Web 应用](../cosmos-db/create-documentdb-dotnet.md)快速入门教程。 如果希望使用 .NET 以外的语言（如 Java 或 Python）完成此操作，只需在转到站点后选择首选的语言。

## <a name="next-steps"></a>后续步骤

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50)

[什么是 SQL 数据库？](../sql-database/sql-database-technical-overview.md)

[Azure 门户中的 SQL 数据库查询编辑器] (https://azure.microsoft.com/blog/t-sql-query-editor-in-browser-azure-portal/)

[什么是 Azure 信息保护？](https://docs.microsoft.com/information-protection/understand-explore/what-is-information-protection)

[什么是 Azure Rights Management？](https://docs.microsoft.com/information-protection/understand-explore/what-is-azure-rms)

[Azure 信息保护：就绪、设置、保护！](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/21/azure-information-protection-ready-set-protect/)

