---
title: 在 Azure 流分析作业中使用 SQL 数据库引用数据
description: 本文介绍如何在 Azure 门户和 Visual Studio 中对 Azure 流分析作业使用 SQL 数据库作为参考数据输入。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: e6feca8cc87eadb2be5f43cafaa82195a18c3c75
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200379"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>将 SQL 数据库中的参考数据用于 Azure 流分析作业

Azure 流分析支持将 Azure SQL 数据库用作参考数据的输入源。 可以在 Azure 门户和 Visual Studio 中配合流分析工具将 SQL 数据库用作流分析作业的参考数据。 本文演示如何使用这两种方法。

## <a name="azure-portal"></a>Azure 门户

在 Azure 门户中使用以下步骤将 Azure SQL 数据库添加为参考输入源：

### <a name="portal-prerequisites"></a>门户先决条件

1. 创建流分析作业。

2. 创建流分析作业使用的存储帐户。

3. 创建包含数据集的 Azure SQL 数据库，流分析作业将使用该数据集作为参考数据。

### <a name="define-sql-database-reference-data-input"></a>定义 SQL 数据库参考数据输入

1. 在流分析作业中，选择“作业拓扑”下的“输入”。   单击“添加参考输入”并选择“SQL 数据库”。  

   ![流分析作业输入](./media/sql-reference-data/stream-analytics-inputs.png)

2. 填写“流分析输入配置”。 选择数据库名称、服务器名称、用户名和密码。 如果希望参考数据输入定期刷新，请选择“打开”以指定刷新频率（采用 DD:HH:MM 格式）。 如果你的数据集较大且刷新频率较短，可以使用[增量查询](sql-reference-data.md#delta-query)。

   ![SQL 数据库参考配置](./media/sql-reference-data/sql-input-config.png)

3. 在 SQL 查询编辑器中测试快照查询。 有关详细信息，请参阅[使用 Azure 门户的 SQL 查询编辑器进行连接并查询数据](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>在作业配置中指定存储帐户

导航到“配置”下的“存储帐户设置”，然后选择“添加存储帐户”。   

   ![流分析存储帐户设置](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>启动作业

配置其他输入、输出和查询后，可以启动流分析作业。

## <a name="tools-for-visual-studio"></a>适用于 Visual Studio 的工具

在 Visual Studio 中使用以下步骤将 Azure SQL 数据库添加为参考输入源：

### <a name="visual-studio-prerequisites"></a>Visual Studio 先决条件

1. [安装用于 Visual Studio 的流分析工具](stream-analytics-tools-for-visual-studio-install.md)。 支持以下 Visual Studio 版本：

   * Visual Studio 2015
   * Visual Studio 2019

2. 通过[用于 Visual Studio 的流分析工具](stream-analytics-quick-create-vs.md)快速入门来熟悉工具。

3. 创建存储帐户。

### <a name="create-a-sql-database-table"></a>创建 SQL 数据库表

使用 SQL Server Management Studio 创建用于存储参考数据的表。 有关详细信息，请参阅[使用 SSMS 设计第一个 Azure SQL 数据库](../sql-database/sql-database-design-first-database.md)。

以下示例中使用的示例表是通过以下语句创建的：

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>选择自己的订阅

1. 在 Visual Studio 中，在“视图”菜单中选择“服务器资源管理器”   。

2. 右键单击“Azure”，选择“连接到 Microsoft Azure 订阅”，然后使用 Azure 帐户登录。  

### <a name="create-a-stream-analytics-project"></a>创建流分析项目

1. 选择“文件”>“新建项目”  。 

2. 在左侧的模板列表中，选择“流分析”，然后选择“Azure 流分析应用程序”   。 

3. 输入项目的“名称”、“位置”和“解决方案名称”，然后选择“确定”。    

   ![Visual Studio 中的新流分析项目](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>定义 SQL 数据库参考数据输入

1. 创建新输入。

   ![Visual Studio 中的新流分析输入](./media/sql-reference-data/stream-analytics-vs-input.png)

2. 在“解决方案资源管理器”中双击“Input.json”。  

3. 填写“流分析输入配置”。  选择数据库名称、服务器名称、刷新类型和刷新频率。 以 `DD:HH:MM` 格式指定刷新频率。

   ![Visual Studio 中的流分析输入配置](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   如果选择“仅执行一次”或“定期执行”，项目中的“Input.json”文件节点下会生成一个名为 **[Input Alias].snapshot.sql** 的 SQL CodeBehind 文件。 

   ![Visual Studio 中输入 Code Behind 文件](./media/sql-reference-data/once-or-periodically-codebehind.png)

   如果选择“使用增量数据定期刷新”，则会生成两个 SQL CodeBehind 文件： **[Input Alias].snapshot.sql** 和 **[Input Alias].delta.sql**。

   ![解决方案资源管理器中的 Code Behind 文件](./media/sql-reference-data/periodically-delta-codebehind.png)

4. 在编辑器中打开 SQL 文件并编写 SQL 查询。

5. 如果使用 Visual Studio 2019 并且已安装 SQL Server Data Tools，可以单击“执行”来测试查询。  此时会弹出一个向导窗口帮助你连接到 SQL 数据库；查询结果将显示在窗口底部。

### <a name="specify-storage-account"></a>指定存储帐户

打开“JobConfig.json”以指定用于存储 SQL 参考快照的存储帐户。 

   ![Visual Studio 中的流分析作业配置](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>在本地进行测试并部署到 Azure

将作业部署到 Azure 之前，可在本地针对实时输入数据测试查询逻辑。 有关此功能的详细信息，请参阅[使用用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览）](stream-analytics-live-data-local-testing.md)。 完成测试后，单击“提交到 Azure”。  请参考[使用用于 Visual Studio 的 Azure 流分析工具创建流分析](stream-analytics-quick-create-vs.md)快速入门来了解如何启动作业。

## <a name="delta-query"></a>增量查询

使用增量查询时，建议使用 [Azure SQL 数据库中的时态表](../sql-database/sql-database-temporal-tables.md)。

1. 在 Azure SQL 数据库中创建时态表。
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. 创作快照查询。 

   使用 **\@snapshotTime** 参数指示流分析运行时从在当前系统时间有效的 SQL 数据库时态表中获取参考数据集。 如果不提供此参数，可能会出于时钟偏差的原因获取不准确的基本参考数据集。 完整的快照查询示例如下所示：
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. 创作增量查询。 
   
   此查询检索从开始时间 **\@deltaStartTime** 到结束时间 **\@deltaEndTime** 范围内，在 SQL 数据库中插入或删除的所有行。 增量查询必须返回与快照查询以及列**操作**相同的列。  此列定义在 **\@deltaStartTime** 到 **\@deltaEndTime** 时间范围内是否插入或删除了行。 如果插入了记录，则生成的行将标记为 **1**；如果删除了记录，则标记为 **2**。 查询还必须从 SQL Server 端添加**水印**，以确保在增量期间内进行的所有更新都得到适当的捕获。 使用不带**水印**的增量查询可能导致引用数据集不正确。  

   对于更新的记录，时态表将通过捕获插入和删除操作来执行簿记。 然后，流分析运行时将增量查询的结果应用到前一快照，以保持参考数据的最新状态。 下面显示了增量查询的示例：

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as watermark 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as watermark 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   请注意，除了运行用于存储检查点的增量查询以外，流分析运行时还可以定期运行快照查询。

## <a name="test-your-query"></a>测试查询
   请务必验证查询是否返回流分析作业将用作参考数据的所需数据集。 若要测试查询，请转到门户上“作业拓扑”部分下的“输入”。 然后，可以在“SQL 数据库参考输入”中选择“示例数据”。 示例可用后，可以下载该文件并检查返回的数据是否符合预期。 如果希望优化开发和测试迭代，建议使用[适用于 Visual Studio 的流分析工具](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)。 也可以使用你喜欢的任何其他工具，首先确保查询从 Azure SQL 数据库返回正确的结果，然后在流分析作业中使用该查询。 

## <a name="faqs"></a>常见问题解答

**在 Azure 流分析中使用 SQL 参考数据输入是否会产生额外的费用？**

流分析作业中不会产生额外的[流单元费用](https://azure.microsoft.com/pricing/details/stream-analytics/)。 但是，流分析作业必须有一个关联的 Azure 存储帐户。 流分析作业查询 SQL DB（在作业启动和刷新间隔期间）来检索参考数据集，并将该快照存储在存储帐户中。 存储这些快照会产生额外的费用，详情请参阅 Azure 存储帐户的[定价页](https://azure.microsoft.com/pricing/details/storage/)。

**如何知道参考数据快照是从 SQL DB 查询的并在 Azure 流分析作业中使用？**

可以使用两个按“逻辑名称”筛选的指标（在指标 Azure 门户中）来监视 SQL 数据库参考数据输入的运行状况。

   * 大于：此指标测量从 SQL 数据库引用数据集中加载的记录数。
   * InputEventBytes：此指标测量在流分析作业的内存中加载的引用数据快照的大小。 

可以使用这两个指标的组合来推断作业是否正在查询 SQL 数据库以提取参考数据集，然后将其载入内存。

**是否需要特殊类型的 Azure SQL 数据库？**

Azure 流分析可与任何类型的 Azure SQL 数据库配合工作。 但必须知道，针对参考数据输入设置的刷新频率可能会影响查询负载。 若要使用增量查询选项，我们建议使用 Azure SQL 数据库中的时态表。

**Azure 流分析为何在 Azure 存储帐户中存储快照？**

流分析保证刚好进行一次事件处理，以及至少进行一次事件传送。 如果暂时性的问题影响了作业，只需进行少量的重放即可还原状态。 若要启用重放，必须将这些快照存储在 Azure 存储帐户中。 有关检查点重放的详细信息，请参阅 [Azure 流分析作业中的检查点和重放概念](stream-analytics-concepts-checkpoint-replay.md)。

## <a name="next-steps"></a>后续步骤

* [使用参考数据在流分析中查找](stream-analytics-use-reference-data.md)
* [快速入门：使用用于 Visual Studio 的 Azure 流分析工具创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据（预览版）](stream-analytics-live-data-local-testing.md)
