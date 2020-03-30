---
title: 使用流分析集成流数据（预览）
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820842"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>使用 Azure SQL 数据库流分析集成（预览）流式传输数据

用户现在可以使用[Azure 流分析](../stream-analytics/stream-analytics-introduction.md)直接从 Azure 门户中的 SQL 数据库中将实时流数据引入、处理、查看和分析到表中。 此体验支持各种方案，如互联汽车、远程监控、欺诈检测等。 在 Azure 门户中，您可以选择事件源（事件中心/IoT 中心）、查看传入的实时事件以及选择要存储事件的表。 您还可以在门户中编写流分析查询语言查询，以转换传入事件并将其存储在所选表中。 此新入口点是流分析中已经存在的创建和配置体验的补充。 此体验从数据库的上下文开始，使您能够快速设置流分析作业，并在 Azure SQL 数据库和流分析体验之间无缝导航。

![流分析流](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>主要优点

- 最小上下文切换：可以从门户中的 SQL 数据库开始，并开始将实时数据引入表中，而无需切换到任何其他服务。 
- 减少步骤数：数据库和表的上下文用于预配置流分析作业。
- 预览数据的其他易用性：在所选表的上下文中预览来自事件源（事件中心/IoT 中心）的传入数据 


## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要准备好以下资源：

- Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。 
- SQL 数据库。 有关详细信息，请参阅[在 Azure SQL 数据库中创建单个数据库](sql-database-single-database-get-started.md)。
- 允许计算机连接到 Azure SQL 服务器的防火墙规则。 有关详细信息，请参阅[创建服务器级防火墙规则](sql-database-server-level-firewall-rule.md)。


## <a name="configure-stream-analytics-integration"></a>配置流分析集成

1. 登录到 Azure 门户。   
2. 导航到要引入流数据的 SQL 数据库。 选择**流分析（预览）。** 

    ![流分析](media/sql-database-stream-analytics/stream-analytics.png)

3. 要开始将流式处理数据引入此 SQL 数据库，请选择"**创建"** 并指定流式处理作业的名称，然后选择"**下一步：输入**"。 

    ![创建流分析作业](media/sql-database-stream-analytics/create-job.png)

4. 输入事件源详细信息，然后选择 **"下一步：输出**"。

   - **输入类型**：事件中心/IoT 中心
   - **输入别名**：输入名称以标识事件源 
   - **订阅**： 与 SQL 数据库订阅相同 
   - **事件中心命名空间**：命名空间的名称 
   - **事件中心名称**：选定命名空间中事件中心的名称 
   - **事件中心策略名称**（默认为创建新）：提供策略名称 
   - **事件中心使用者组**（默认为创建新）：提供使用者组名称  
     - 我们建议您为在此处创建的每个新的 Azure 流分析作业创建一个使用者组和策略。 使用者组仅允许五个并发读取器，因此为每个作业提供专用使用者组将避免因超出该限制而可能出现的任何错误。 专用策略允许您轮换密钥或撤消权限，而不会影响其他资源。

    ![创建流分析作业](media/sql-database-stream-analytics/create-job-output.png)

5. 选择要将流式数据引入到哪个表。 完成后，选择 **"创建**"。
   - **用户名**，**密码**： 输入 SQL 服务器身份验证的凭据。 选择“验证”。****
   - **表**： 选择 **"创建新**"或使用**现有**。 在此流中，让我们选择 **"创建**"。 这将在启动流分析作业时创建新表。

    ![创建流分析作业](media/sql-database-stream-analytics/create.png)

6. 将打开一个查询页，其中包含以下详细信息：

   - 输入 **（** 输入事件源），您将从中引入数据  
   - 将存储转换数据的**输出**（输出表） 
   - 使用 SELECT 语句对[SAQL 查询](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md)进行采样。 
   - **输入预览**：显示来自输入事件源的最新传入数据的快照。  
     - 将自动检测数据中的序列化类型 （JSON/CSV）。 您也可以手动将其更改为 JSON/CSV/AVRO。 
     - 您可以预览表格式或原始格式的传入数据。 
     - 如果显示的数据不是最新的，请选择“刷新”查看最新事件。**** 
     - **选择"选择时间范围**"以根据传入事件的特定时间范围测试查询。 
     - 选择 **"上传示例输入**"以通过上传示例 JSON/CSV 文件来测试查询。 有关测试 SAQL 查询的详细信息，请参阅[使用示例数据测试 Azure 流分析作业](../stream-analytics/stream-analytics-test-query.md)。 

    ![测试查询](media/sql-database-stream-analytics/test-query.png)


   - **测试结果**：选择**测试查询**，您可以看到流式处理查询的结果 

    ![测试结果](media/sql-database-stream-analytics/test-results.png)

   - **测试结果架构**：在测试后显示流式处理查询结果的架构。 确保测试结果架构与您的输出架构匹配。 

    ![测试结果架构](media/sql-database-stream-analytics/test-results-schema.png)


   - **输出架构**：它包含您在步骤 5（新或现有）中选择的表的架构。
     - 创建新：如果在步骤 5 中选择此选项，则在开始流式处理作业之前，您还看不到架构。 创建新表时，请选择相应的表索引。 有关表索引的详细信息，请参阅[描述群集和非群集索引](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)。
    - 使用现有：如果在步骤 5 中选择此选项，您将看到所选表的架构。 
 
7. 完成&测试查询后，选择 **"保存查询**"。 选择 **"开始流分析"作业**，开始将转换后的数据引入 SQL 表。 完成以下字段后，**启动**作业。 
   - **输出开始时间**：这定义作业的第一次输出的时间。  
     - 现在：作业将立即开始并处理新的传入数据。
     - 自定义：作业现在开始，但将从特定时间点（可以是过去或将来）处理数据。 有关详细信息，请参阅[如何启动 Azure 流分析作业](../stream-analytics/start-job.md)。
   - **流式处理单元**：Azure 流分析按将数据处理到服务所需的流单元数定价。 有关详细信息，请参阅[Azure 流分析定价](https://azure.microsoft.com/pricing/details/stream-analytics/)。 
   - **输出数据错误处理**：  
     - 重试：发生错误时，Azure 流分析会无限期地重试写入事件，直到写入成功。 重试不会超时。 最终，重试的事件会阻止所有后续事件的处理。 此选项是默认的输出错误处理策略。 
     - 删除：Azure 流分析将删除导致数据转换错误的任何输出事件。 无法恢复丢弃的事件以便稍后重新处理。 不管采用哪种输出错误处理策略配置，都会重试所有暂时性错误（例如网络错误）。 
   - **SQL 数据库输出设置**：用于继承上一个查询步骤的分区方案的选项，用于启用具有多个编写器的完全并行拓扑到表。 有关详细信息，请参阅从 [Azure 流分析输出到 Azure SQL 数据库](../stream-analytics/stream-analytics-sql-output-perf.md)。
   - **最大批处理计数**：建议对随每个批量插入事务发送的记录数的上限。  
    有关输出错误处理的详细信息，请参阅[Azure 流分析中的输出错误策略](../stream-analytics/stream-analytics-output-error-policy.md)。  

    ![开始作业](media/sql-database-stream-analytics/start-job.png)

8. 启动作业后，您将在列表中看到"运行"作业，并可以执行以下操作： 
   - **开始/停止作业**：如果作业正在运行，则可以停止作业。 如果作业已停止，则可以启动该作业。 
   - **编辑作业**：您可以编辑查询。 如果要对作业执行更多更改，则添加更多输入/输出，然后在流分析中打开作业。 作业运行时禁用编辑选项。 
   - **预览输出表**：您可以在 SQL 查询编辑器中预览表。 
   - **在流分析中打开**：在流分析服务中打开作业以查看监视、调试作业的详细信息。 


    ![流分析作业](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>后续步骤

- [Azure 流分析文档](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure 流分析解决方案模式](../stream-analytics/stream-analytics-solution-patterns.md)
