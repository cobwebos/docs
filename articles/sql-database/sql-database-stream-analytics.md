---
title: 使用流分析集成的流数据（预览版）
description: 使用 Azure 流分析将数据流式传输到 Azure SQL 数据库。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820842"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>使用 Azure SQL 数据库流分析集成（预览版）流式传输数据

用户现在可以使用[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)从 Azure 门户中的 SQL 数据库直接引入、处理、查看和分析表中的实时流数据。 这种体验可实现多种方案，例如连接的汽车、远程监视、欺诈检测等。 在 Azure 门户中，可以选择事件源（事件中心/IoT 中心），查看传入实时事件，并选择用于存储事件的表。 你还可以在门户中编写流分析查询语言查询来转换传入事件并将其存储在所选表中。 这个新的入口点是流分析中已经存在的创建和配置体验的补充。 此体验从数据库上下文开始，使你能够快速设置流分析作业，并在 Azure SQL 数据库和流分析体验之间无缝导航。

![流分析流](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>主要优点

- 最小上下文切换：可以从门户中的 SQL 数据库开始，开始将实时数据引入到表中，而无需切换到任何其他服务。 
- 减少的步骤数：数据库和表的上下文用于预配置流分析作业。
- 预览数据的更易于使用：在所选表的上下文中预览来自事件源（事件中心/IoT 中心）的传入数据 


## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下资源：

- Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。 
- SQL 数据库。 有关详细信息，请参阅[在 AZURE SQL 数据库中创建单个数据库](sql-database-single-database-get-started.md)。
- 允许计算机连接到 Azure SQL server 的防火墙规则。 有关详细信息，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。


## <a name="configure-stream-analytics-integration"></a>配置流分析集成

1. 登录到 Azure 门户。 
2. 导航到要在其中引入流式传输数据的 SQL 数据库。 选择 "**流分析（预览版）** "。 

    ![流分析](media/sql-database-stream-analytics/stream-analytics.png)

3. 若要开始将流数据引入到此 SQL 数据库中，请选择 "**创建**" 并为流式处理作业指定一个名称，然后选择 "**下一步：输入**"。 

    ![创建流分析作业](media/sql-database-stream-analytics/create-job.png)

4. 输入事件源详细信息，然后选择 "**下一步：输出**"。

   - **输入类型**：事件中心/IoT 中心
   - **输入别名**：输入用于标识事件源的名称 
   - **订阅**：与 SQL 数据库订阅相同 
   - **事件中心命名空间**：命名空间的名称 
   - **事件中心名称**：所选命名空间中的事件中心的名称 
   - **事件中心策略名称**（默认为新建）：指定策略名称 
   - **事件中心使用者组**（默认为新建）：指定使用者组名称  
     - 建议为你从此处创建的每个新的 Azure 流分析作业创建使用者组和策略。 使用者组仅允许5个并发读取者，因此为每个作业提供专用使用者组将避免超出该限制可能产生的任何错误。 专用策略允许你轮换密钥或撤消权限，而不会影响其他资源。

    ![创建流分析作业](media/sql-database-stream-analytics/create-job-output.png)

5. 选择要将流数据引入其中的表。 完成后，选择 "**创建**"。
   - **用户名**、**密码**：输入用于 SQL server 身份验证的凭据。 选择“验证”。
   - **表**：选择 "**新建**" 或 "**使用现有**"。 在此流中，选择 "**创建**"。 启动流分析作业时，这将创建一个新表。

    ![创建流分析作业](media/sql-database-stream-analytics/create.png)

6. 此时将打开一个查询页，其中包含以下详细信息：

   - 你将从中引入数据的**输入**（输入事件源）  
   - 将存储已转换数据的**输出**（输出表） 
   - 带有 SELECT 语句的示例[SAQL 查询](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md)。 
   - **输入预览**：显示来自输入事件源的最新传入数据的快照。  
     - 自动检测数据中的序列化类型（JSON/CSV）。 还可以手动将其更改为 JSON/CSV/AVRO。 
     - 可以预览表格式或原始格式的传入数据。 
     - 如果显示的数据不是最新的，请选择“刷新”查看最新事件。 
     - 选择 "**选择时间范围**" 可针对传入事件的特定时间范围测试查询。 
     - 选择 "**上传示例输入**"，通过上传示例 JSON/CSV 文件来测试查询。 有关测试 SAQL 查询的详细信息，请参阅[使用示例数据测试 Azure 流分析作业](../stream-analytics/stream-analytics-test-query.md)。 

    ![测试查询](media/sql-database-stream-analytics/test-query.png)


   - **测试结果**：选择 "**测试查询**"，可以查看流式处理查询的结果 

    ![测试结果](media/sql-database-stream-analytics/test-results.png)

   - **测试结果架构**：在测试后显示流式处理查询结果的架构。 请确保测试结果架构与输出架构匹配。 

    ![测试结果架构](media/sql-database-stream-analytics/test-results-schema.png)


   - **输出架构**：包含在步骤5（新的或现有的）中选择的表的架构。
     - 新建：如果在步骤5中选择了此选项，则在启动流式处理作业之前，你将看不到此架构。 创建新表时，请选择相应的表索引。 有关表索引的详细信息，请参阅[所述的聚集索引和非聚集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)。
    - 使用现有的：如果在步骤5中选择了此选项，则会看到所选表的架构。 
 
7. 完成创作 & 测试查询后，请选择 "**保存查询**"。 选择 "**启动流分析作业**"，开始将已转换的数据引入到 SQL 表中。 完成以下字段后，请**启动**该作业。 
   - **输出开始时间**：定义作业的第一个输出的时间。  
     - 现在：作业将立即启动并处理新的传入数据。
     - 自定义：作业将立即启动，但会处理来自特定时间点（可能是过去或未来）的数据。 有关详细信息，请参阅[如何启动 Azure 流分析作业](../stream-analytics/start-job.md)。
   - **流式处理单元**： Azure 流分析按照将数据处理到服务所需的流式处理单元数来定价。 有关详细信息，请参阅[Azure 流分析定价](https://azure.microsoft.com/pricing/details/stream-analytics/)。 
   - **输出数据错误处理**：  
     - 重试：出错时，Azure 流分析会无限期地重试写入事件，直至写入成功为止。 重试不会超时。 最终，重试的事件会阻止所有后续事件的处理。 此选项是默认的输出错误处理策略。 
     - Drop： Azure 流分析将删除导致数据转换错误的任何输出事件。 无法恢复丢弃的事件以便稍后重新处理。 不管采用哪种输出错误处理策略配置，都会重试所有暂时性错误（例如网络错误）。 
   - **SQL 数据库输出设置**：一个选项，用于继承上一个查询步骤的分区方案，以便为表启用具有多个编写器的完全并行拓扑。 有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](../stream-analytics/stream-analytics-sql-output-perf.md)。
   - **最大批处理计数**：建议的对每个大容量插入事务发送的记录数的上限。  
    有关输出错误处理的详细信息，请参阅[Azure 流分析中的输出错误策略](../stream-analytics/stream-analytics-output-error-policy.md)。  

    ![启动作业](media/sql-database-stream-analytics/start-job.png)

8. 启动作业后，你将在列表中看到正在运行的作业，你可以执行以下操作： 
   - **启动/停止作业**：如果作业正在运行，则可以停止作业。 如果作业已停止，你可以启动作业。 
   - **编辑作业**：可以编辑查询。 如果要对作业进行更多更改（例如，添加更多输入/输出），然后在流分析中打开该作业。 当作业正在运行时，"编辑" 选项处于禁用状态。 
   - **预览输出表**：可以在 SQL 查询编辑器中预览该表。 
   - **在流分析中打开**：在流分析服务中打开作业，以查看监视、调试作业的详细信息。 


    ![流分析作业](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>后续步骤

- [Azure 流分析文档](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure 流分析解决方案模式](../stream-analytics/stream-analytics-solution-patterns.md)
