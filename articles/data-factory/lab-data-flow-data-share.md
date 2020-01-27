---
title: 使用 Azure 数据工厂和 Azure Data Share 实现数据集成
description: 使用 Azure 数据工厂与 Azure Data Share 复制、转换和共享数据
author: djpmsft
ms.author: daperlov
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 6c501205812ac72da8cd970b61b71e493888cef1
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156720"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>使用 Azure 数据工厂和 Azure Data Share 实现数据集成

当客户开始组建其新式数据仓库和分析项目时，他们不仅需要更多的数据，而且还需要能够洞察整个数据资产中的数据。 本讲座深入探讨如何对 Azure 数据工厂和 Azure Data Share 做出改进，以简化 Azure 中的数据集成和管理。 从启用无代码的 ETL/ELT，到基于数据创建综合视图，Azure 数据工厂的改进可让数据工程师自信地引入更多数据，从而为企业带来增大的价值。 借助 Azure Data Share 能够以受控的方式实现业务到业务共享。

在本讲座中，你要使用 Azure 数据工厂 (ADF) 将数据从 Azure SQL 数据库 (SQL DB) 引入 Azure Data Lake Storage Gen2 (ADLS Gen2)。 数据进入 Data Lake 后，你将通过映射数据流（数据工厂的本机转换服务）转换数据，并在 Azure Synapse Analytics（前称为 SQL 数据仓库）中接收数据。 然后，使用 Azure Data Share 来共享包含转换后的数据以及其他一些数据的表。 

本实验中使用的数据为纽约市出租车数据。 若要将此数据导入 Azure SQL 数据库，请下载 [taxi-data bacpac 文件](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)。

## <a name="prerequisites"></a>必备条件

* **Azure 订阅**：如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

* **Azure SQL 数据库**：如果没有 SQL 数据库，请了解如何[创建 SQL 数据库帐户](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 存储帐户**：如果没有 ADLS Gen2 的存储帐户，请了解如何[创建 ADLS Gen2 存储帐户](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)。

* **Azure Synapse Analytics（前称为 SQL 数据仓库）** ：如果没有 Azure Synapse Analytics（前称为 SQL 数据仓库），请参阅如何[创建 Azure Synapse Analytics 实例](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)。

* **Azure 数据工厂**：如果尚未创建数据工厂，请参阅如何[创建数据工厂](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal)。

* **Azure Data Share**：如果尚未创建数据共享，请参阅如何[创建数据共享](https://docs.microsoft.com/azure/data-share/share-your-data#create-a-data-share-account)。

## <a name="set-up-your-azure-data-factory-environment"></a>设置 Azure 数据工厂环境

本部分介绍如何从 Azure 门户访问 Azure 数据工厂用户体验 (ADF UX)。 进入 ADF UX 后，为我们正在使用的每个数据存储配置三个链接服务：Azure SQL 数据库、ADLS Gen2 和 Azure Synapse Analytics。

在 Azure 数据工厂链接服务中，定义外部资源的连接信息。 Azure 数据工厂目前支持 85 个以上的连接器。

### <a name="open-the-azure-data-factory-ux"></a>打开 Azure 数据工厂 UX

1. 在 Microsoft Edge 或 Google Chrome 中打开 [Azure 门户](https://portal.azure.com)。
1. 使用页面顶部的搜索栏搜索“数据工厂”

    ![门户](media/lab-data-flow-data-share/portal1.png)
1. 单击数据工厂资源打开其资源边栏选项卡。

    ![门户](media/lab-data-flow-data-share/portal2.png)
1. 单击“创作和监视”打开 ADF UX。  也可以在 adf.azure.com 中访问 ADF UX。

    ![门户](media/lab-data-flow-data-share/portal3.png)
1. 你将重定向到 ADF UX 的主页。 此页包含快速入门、指导视频和教程链接，以帮助你了解数据工厂的概念。 若要开始创作，请单击左侧栏中的铅笔图标。

    ![门户](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>创建 Azure SQL 数据库链接服务

1. 在创作页中，可以创建管道、数据集、数据流、触发器和链接服务等数据工厂资源。 若要创建链接服务，请单击右下角的“连接”按钮。 

    ![门户](media/lab-data-flow-data-share/configure2.png)
1. 在“连接”选项卡中，单击“新建”以添加新的链接服务。 

    ![门户](media/lab-data-flow-data-share/configure3.png)
1. 要配置的第一个链接服务是 Azure SQL 数据库。 可以使用搜索栏来筛选数据存储列表。 单击“Azure SQL 数据库”磁贴，然后单击“继续”。 

    ![门户](media/lab-data-flow-data-share/configure4.png)
1. 在 SQL 数据库配置窗格中，输入“SQLDB”作为链接服务名称。 输入凭据，使数据工厂能够连接到该数据库。 如果使用的是 SQL 身份验证，请输入服务器名称、数据库、用户名和密码。 可以单击“测试连接”来验证连接信息是否正确。  完成后，单击“创建”。 

    ![门户](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>创建 Azure Synapse Analytics 链接服务

1. 重复相同的过程来添加 Azure Synapse Analytics 链接服务。 在“连接”选项卡中，单击“新建”。  选择“Azure Synapse Analytics (前称为 SQL 数据仓库)”磁贴，然后单击“继续”。 

    ![门户](media/lab-data-flow-data-share/configure6.png)
1. 在链接服务配置窗格中，输入“SQLDW”作为链接服务名称。 输入凭据，使数据工厂能够连接到该数据库。 如果使用的是 SQL 身份验证，请输入服务器名称、数据库、用户名和密码。 可以单击“测试连接”来验证连接信息是否正确。  完成后，单击“创建”。 

    ![门户](media/lab-data-flow-data-share/configure7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>创建 Azure Data Lake Storage Gen2 链接服务

1. 本实验所需的最后一个链接服务是 Azure Data Lake Storage Gen2。  在“连接”选项卡中，单击“新建”。  选择“Azure Data Lake Storage Gen2”磁贴，然后单击“继续”。 

    ![门户](media/lab-data-flow-data-share/configure8.png)
1. 在链接服务配置窗格中，输入“ADLSGen2”作为链接服务名称。 如果使用的是“帐户密钥”身份验证，请从“存储帐户名称”下拉列表中选择你的 ADLS Gen2 存储帐户。  可以单击“测试连接”来验证连接信息是否正确。  完成后，单击“创建”。 

    ![门户](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>启用数据流调试模式

在“使用映射数据流转换数据”部分，你将生成映射数据流。  在生成映射数据流之前，最佳做法是启用调试模式，以便能够在活动的 Spark 群集上快速测试转换逻辑。

若要启用调试，请在工厂顶部栏中单击“数据流调试”滑块。  弹出确认对话框时，请单击“确定”。 启动群集大约需要 5-7 分钟。 当初始化正在进行时，请继续执行“使用复制活动将数据从 Azure SQL 数据库引入 ADLS Gen2”。 

![门户](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>使用复制活动引入数据

在本部分，你将创建一个包含复制活动的管道，该活动将一个表从 Azure SQL 数据库引入 ADLS Gen2 存储帐户。 你将了解如何通过 ADF UX 添加管道、配置数据集和调试管道。 本部分使用的配置模式适用于从关系数据存储复制到基于文件的数据存储。

在 Azure 数据工厂中，管道是共同执行某项任务的活动的逻辑分组。 活动定义要对数据执行的操作。 数据集指向要在链接服务中使用的数据。

### <a name="create-a-pipeline-with-a-copy-activity"></a>创建包含复制活动的管道

1. 在工厂资源窗格中，单击加号图标打开“新建资源”菜单。 选择“管道”。 

    ![门户](media/lab-data-flow-data-share/copy1.png)
1. 在管道画布的“常规”选项卡中，为管道指定描述性的名称，例如“IngestAndTransformTaxiData”。 

    ![门户](media/lab-data-flow-data-share/copy2.png)
1. 在管道画布的“活动”窗格中，打开“移动和转换”可折叠部分，然后将“复制数据”活动拖到画布上。   为复制活动指定描述性的名称，例如“IngestIntoADLS”。

    ![门户](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>配置 Azure SQL 数据库源数据集

1. 单击复制活动的“源”选项卡。  若要创建新数据集，请单击“新建”。  源是位于前面配置的链接服务“SQLDB”中的表“dbo.TripData”。

    ![门户](media/lab-data-flow-data-share/copy4.png)
1. 搜索“Azure SQL 数据库”并单击“继续”。 

    ![门户](media/lab-data-flow-data-share/copy5.png)
1. 将数据集命名为“TripData”。 选择“SQLDB”作为链接服务。 从表名称下拉列表中选择表名称“dbo.TripData”。 导入架构“从连接/存储”。  完成后，单击“确定”。

    ![门户](media/lab-data-flow-data-share/copy6.png)

现已成功创建了源数据集。 在源设置中，请确保已在“使用查询”字段中选择了默认值“表”。 

### <a name="configure-adls-gen-2-sink-dataset"></a>配置 ADLS Gen2 接收器数据集

1. 单击复制活动的“接收器”选项卡。  若要创建新数据集，请单击“新建”。 

    ![门户](media/lab-data-flow-data-share/copy7.png)
1. 搜索 **Azure Data Lake Storage Gen2**，然后单击“继续”。

    ![门户](media/lab-data-flow-data-share/copy8.png)
1. 在“选择格式”窗格中选择“DelimitedText”，因为将要写入到 csv 文件。  单击“继续”。

    ![门户](media/lab-data-flow-data-share/copy9.png)
1. 将接收器数据集命名为“TripDataCSV”。 选择“ADLSGen2”作为链接服务。 输入要将 csv 文件写入到的位置。 例如，可将数据写入容器 `staging-container` 中的文件 `trip-data.csv`。 将“第一行用作标头”设置为 true，因为你希望输出数据包含标头。  由于目标中尚不存在任何文件，因此请将“导入架构”设置为 **None**。  完成后，单击“确定”。

    ![门户](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>使用管道调试运行测试复制活动

1. 若要验证复制活动是否正常工作，请单击管道画布顶部的“调试”以执行调试运行。  在将管道发布到数据工厂服务之前，可以使用调试运行对其进行端到端的测试，或者在某个断点位置之前对其进行测试。

    ![门户](media/lab-data-flow-data-share/copy11.png)
1. 若要监视调试运行，请转到管道画布的“输出”选项卡。  监视屏幕每隔 20 秒自动刷新，或者在手动单击刷新按钮时刷新。 复制活动提供一个特殊的监视视图，可通过单击“操作”列中的眼镜图标来访问该视图。 

    ![门户](media/lab-data-flow-data-share/copy12.png)
1. 复制监视视图提供活动的执行详细信息和性能特征。 可以查看读取/写入的数据量、读取/写入的行数、读取/写入的文件数和吞吐量等信息。 如果正确配置了所有设置，应会看到已将 49,999 行写入 ADLS 接收器中的一个文件。

    ![门户](media/lab-data-flow-data-share/copy13.png)
1. 在转到下一部分之前，我们建议通过单击工厂顶部栏中的“全部发布”，来发布对数据工厂服务所做的更改。  Azure 数据工厂支持完整的 Git 集成，不过，本实验不探讨此方案。 使用 Git 集成可以实现版本控制、在存储库中反复保存，以及针对数据工厂展开协作。 有关详细信息，请参阅 [Azure 数据工厂中的源代码管理](https://docs.microsoft.com/azure/data-factory/source-control#troubleshooting-git-integration)。

    ![门户](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>使用映射数据流转换数据

将数据成功复制到 Azure Data Lake Storage 后，接下来可将该数据联接并聚合到数据仓库中。 我们将使用映射数据流 - 采用可视化设计的 Azure 数据工厂转换服务。 映射数据流可让用户开发无代码的转换逻辑，并在 ADF 服务管理的 Spark 群集上执行这些逻辑。

在此步骤中创建的数据流基于四个关键列，将上一部分中创建的“TripDataCSV”数据集内部联接到“SQLDB”中存储的表“dbo.TripFares”。 然后，基于列 `payment_type` 聚合数据，以计算某些字段的平均值；这些数据将写入 Azure Synapse Analytics 表。

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>将数据流活动添加到管道

1. 在管道画布的“活动”窗格中，打开“移动和转换”可折叠部分，然后将“数据流”活动拖到画布上。  

    ![门户](media/lab-data-flow-data-share/dataflow1.png)
1. 在打开的边侧窗格中，依次选择“创建新数据流”、“映射数据流”。   单击“确定”。 

    ![门户](media/lab-data-flow-data-share/dataflow2.png)
1. 你将定向到数据流画布，可在其中生成转换逻辑。 在“常规”选项卡中，将数据流命名为“JoinAndAggregateData”。

    ![门户](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>配置行程数据 csv 源

1. 要做的第一件事是配置两个源转换。 第一个源将指向“TripDataCSV”DelimitedText 数据集。 若要添加源转换，请在画布中单击“添加源”框。 

    ![门户](media/lab-data-flow-data-share/dataflow4.png)
1. 将源命名为“TripDataCSV”，从源下拉列表中选择“TripDataCSV”数据集。 如果你记得的话，最初在创建此数据集时，你并未导入架构，因为此数据集不包含任何数据。 由于现在存在 `trip-data.csv`，因此请单击“编辑”转到数据集设置选项卡。 

    ![门户](media/lab-data-flow-data-share/dataflow5.png)
1. 转到“架构”选项卡，单击“导入架构”。   选择“从连接/存储”，以直接从文件存储导入。  此时应会显示 14 个字符串类型的列。

    ![门户](media/lab-data-flow-data-share/dataflow6.png)
1. 返回到数据流“JoinAndAggregateData”。 如果调试群集已启动（由调试滑块旁边的绿色圆圈指示），则你可以在“数据预览”选项卡中获取数据的快照。  单击“刷新”以提取数据预览。 

    ![门户](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> 数据预览不会写入数据。

### <a name="configure-your-trip-fares-sql-db-source"></a>配置行程费用 SQL 数据库源

1. 要添加的第二个源将指向 SQL 数据库表“dbo.TripFares”。 在“TripDataCSV”源下，有另一个“添加源”框。  单击此框以添加新的源转换。

    ![门户](media/lab-data-flow-data-share/dataflow8.png)
1. 将此源命名为“TripFaresSQL”。 单击源数据集字段旁边的“新建”，以创建新的 SQL 数据库数据集。 

    ![门户](media/lab-data-flow-data-share/dataflow9.png)
1. 选择“Azure SQL 数据库”磁贴，然后单击“继续”。  注意：*你可能注意到，数据工厂中的许多连接器在映射数据流中不受支持。若要从其中的某个源转换数据，请使用复制活动将数据引入受支持的源*。

    ![门户](media/lab-data-flow-data-share/dataflow10.png)
1. 将数据集命名为“TripFares”。 选择“SQLDB”作为链接服务。 从表名称下拉列表中选择表名称“dbo.TripFares”。 导入架构“从连接/存储”。  完成后，单击“确定”。

    ![门户](media/lab-data-flow-data-share/dataflow11.png)
1. 若要验证数据，请在“数据预览”选项卡中提取数据预览。 

    ![门户](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>内部联接 TripDataCSV 和 TripFaresSQL

1. 若要添加新转换，请单击“TripDataCSV”右下角的加号图标。 在“多个输入/输出”下，选择“联接”。  

    ![门户](media/lab-data-flow-data-share/join1.png)
1. 将联接转换命名为“InnerJoinWithTripFares”。 从右侧的流下拉列表中选择“TripFaresSQL”。 选择“内部”作为联接类型。  若要详细了解映射数据流中的不同联接类型，请参阅[联接类型](https://docs.microsoft.com/azure/data-factory/data-flow-join#join-types)。

    通过“联接条件”下拉列表选择要从每个流匹配的列。  若要添加更多联接条件，请单击现有条件旁边的加号图标。 默认情况下，所有联接条件将与 AND 运算符相组合，这意味着，必须满足所有条件才视为匹配。 在本实验中，我们想要匹配列 `medallion`、`hack_license`、`vendor_id` 和 `pickup_datetime`

    ![门户](media/lab-data-flow-data-share/join2.png)
1. 使用数据预览验证是否已成功将 25 列联接到一起。

    ![门户](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>按 payment_type 聚合

1. 完成联接转换后，单击“InnerJoinWithTripFares”旁边的加号图标添加聚合转换。 选择“架构修饰符”下的“聚合”。  

    ![门户](media/lab-data-flow-data-share/agg1.png)
1. 将聚合转换命名为“AggregateByPaymentType”。 选择 `payment_type` 作为分组依据列。

    ![门户](media/lab-data-flow-data-share/agg2.png)
1. 转到“聚合”选项卡。  在此处指定两个聚合：
    * 按付款类型分组的平均费用
    * 按付款类型分组的总行程距离

    首先创建平均费用表达式。 在标有“添加或选择列”的文本框中，输入“average_fare”。 

    ![门户](media/lab-data-flow-data-share/agg3.png)
1. 若要输入聚合表达式，请单击标有“输入表达式”的蓝色框。  此时会打开数据流表达式生成器，在此工具中可以使用输入架构、内置函数和操作以及用户定义的参数来直观创建数据流表达式。 有关表达式生成器的功能的详细信息，请参阅[表达式生成器文档](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)。

    若要获取平均费用，请使用 `avg()` 聚合函数来聚合 `total_amount` 列，然后可使用 `toInteger()` 将此列强制转换为整数。 在数据流表达式语言中，此表达式定义为 `avg(toInteger(total_amount))`。 完成后，单击“保存并完成”。 

    ![门户](media/lab-data-flow-data-share/agg4.png)
1. 若要添加更多聚合表达式，请单击 `average_fare` 旁边的加号图标。 选择“添加列”。 

    ![门户](media/lab-data-flow-data-share/agg5.png)
1. 在标有“添加或选择列”的文本框中，输入“total_trip_distance”。  在最后一个步骤中，打开表达式生成器以输入表达式。

    若要获取总行程距离，请使用 `sum()` 聚合函数来聚合 `trip_distance` 列，然后可使用 `toInteger()` 将此列强制转换为整数。 在数据流表达式语言中，此表达式定义为 `sum(toInteger(trip_distance))`。 完成后，单击“保存并完成”。 

    ![门户](media/lab-data-flow-data-share/agg6.png)
1. 在“数据预览”选项卡中测试转换逻辑。  可以看到，行数和列数相比前面少了很多。 在此下游延续转换中，仅定义了三个分组依据和聚合列。 由于示例中只有五个付款类型组，因此只输出了五行。

    ![门户](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>配置 Azure Synapse Analytics 接收器

1. 完成转换逻辑后，接下来可在 Azure Synapse Analytics 表中接收数据。 在“目标”部分下添加接收器转换。 

    ![门户](media/lab-data-flow-data-share/sink1.png)
1. 将接收器命名为“SQLDWSink”。 单击接收器数据集字段旁边的“新建”，以创建新的 Azure Synapse Analytics 数据集。 

    ![门户](media/lab-data-flow-data-share/sink2.png)

1. 选择“Azure Synapse Analytics (前称为 SQL 数据仓库)”磁贴，然后单击“继续”。 

    ![门户](media/lab-data-flow-data-share/sink3.png)
1. 将数据集命名为“AggregatedTaxiData”。 选择“SQLDW”作为链接服务。 选择“创建新表”，并将新表命名为 dbo.AggregateTaxiData。  完成后，单击“确定”

    ![门户](media/lab-data-flow-data-share/sink4.png)
1. 转到接收器的“设置”选项卡。  由于我们要创建新表，因此需要在表操作下选择“重新创建表”。  取消选择“启用暂存”，以便在逐行插入和批量插入之间切换。 

    ![门户](media/lab-data-flow-data-share/sink5.png)

现已成功创建数据流。 接下来，可以在管道活动中运行该数据流。

### <a name="debug-your-pipeline-end-to-end"></a>端到端调试管道

1. 返回 **IngestAndTransformData** 管道的选项卡。 请注意“IngestIntoADLS”复制活动中的绿色框。 将此框拖到“JoinAndAggregateData”数据流活动。 这会创建“on success”条件，以便仅在复制成功时才运行该数据流活动。

    ![门户](media/lab-data-flow-data-share/pipeline1.png)
1. 与对复制活动所做的那样，单击“调试”以执行调试运行。  对于调试运行，数据流活动将使用活动的调试群集，而不是启动新群集。 执行此管道所需的时间略微超过一分钟。

    ![门户](media/lab-data-flow-data-share/pipeline2.png)
1. 与复制活动一样，数据流提供一个特殊的监视视图，可以在完成活动后单击眼镜图标来访问此视图。

    ![门户](media/lab-data-flow-data-share/pipeline3.png)
1. 在监视视图中，可以看到一个简化的数据流图形，以及每个执行阶段花费的执行时间和处理的行数。 如果正常完成了操作，则此活动中应已将 49,999 行聚合成 5 行。

    ![门户](media/lab-data-flow-data-share/pipeline4.png)
1. 可以单击某个转换获取有关其执行的更多详细信息，例如分区信息以及新的/已更新的/已删除的列数。

    ![门户](media/lab-data-flow-data-share/pipeline5.png)

现已完成本实验的数据工厂部分。 若要使用触发器来操作化资源，请发布资源。 你已成功运行一个使用复制活动将数据从 Azure SQL 数据库引入到 Azure Data Lake Storage  的管道，然后将该数据聚合到了 Azure Synapse Analytics。 可以通过查看 SQL Server 本身来验证数据是否已成功写入。

## <a name="share-data-using-azure-data-share"></a>使用 Azure Data Share 共享数据

本部分介绍如何使用 Azure 门户设置新的数据共享。 这涉及到创建一个新的数据共享，其中包含来自 Azure Data Lake Store Gen2 和 Azure SQL 数据仓库的数据集。 然后，将配置一个快照计划，以便为数据使用者提供一个选项来自动刷新与他们共享的数据。 然后，邀请收件人加入你的数据共享。 

创建数据共享后，可以切换身份，成为数据使用者。  你将以数据使用者的身份演练以下流程：接受数据共享邀请，配置要从何处接收数据，并将数据集映射到不同的存储位置。 然后触发快照，以将共享的数据复制到指定的目标。 

### <a name="sharing-data-data-provider-flow"></a>共享数据（数据提供者流）

1. 在 Microsoft Edge 或 Google Chrome 中打开 Azure 门户。

1. 使用页面顶部的搜索栏搜索“数据共享” 

    ![门户](media/lab-data-flow-data-share/portal-ads.png)

1. 选择名称中包含“Provider”的数据共享帐户。 例如 **DataProvider0102**。 

1. 选择“开始共享数据”。 

    ![开始共享](media/lab-data-flow-data-share/ads-start-sharing.png)

1. 选择“+创建”开始配置新的数据共享。  

1. 在“共享名”下，指定所需的名称。  这是数据使用者会看到的共享名，因此请确保指定一个描述性的名称，例如 TaxiData。

1. 在“说明”下，输入用于描述数据共享内容的句子。  该数据共享将包含全球出租车行程数据，这些数据存储在许多存储（包括 Azure SQL 数据仓库和 Azure Data Lake Store）中。 

1. 在“使用条款”下，指定你希望数据使用者遵守的一系列条款。  示例包括“请勿在你的组织外部分发此数据”或“参考法律协议”。 

    ![共享详细信息](media/lab-data-flow-data-share/ads-details.png)

1. 选择“继续”。  

1. 选择“添加数据集”  

    ![添加数据集](media/lab-data-flow-data-share/add-dataset.png)

1. 选择“Azure SQL 数据仓库”，以从 ADF 转换所在的 Azure SQL 数据仓库中选择一个表。 

    ![添加数据集](media/lab-data-flow-data-share/add-dataset-sql.png)

> [!NOTE]
> Azure SQL 数据仓库现已在称作 Azure Synapse Analytics

1. 在继续操作之前，我们会向你提供一个要运行的脚本。 提供的脚本将在 SQL 数据库中创建一个用户，以允许 Azure Data Share MSI 代表该用户完成身份验证。 

> [!IMPORTANT]
> 运行该脚本之前，必须将自己设置为 SQL Server 的 Active Directory 管理员。 

1. 打开新的标签页并导航到 Azure 门户。 复制提供的脚本，以便在要从中共享数据的数据库中创建一个用户。 为此，请在 Query Explorer（预览版）中使用 AAD 身份验证登录到 EDW 数据库。 

    需要修改该脚本，将创建的用户包含在方括号内。 例如：
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. 切换回到 Azure Data Share，你已在其中将数据集添加到数据共享。 

1. 为 SQL 数据仓库选择“EDW”，为表选择“AggregatedTaxiData”。   

1. 选择“添加数据集” 

    现已添加一个包含在数据集中的 SQL 表。 接下来，我们将从 Azure Data Lake Store 添加更多的数据集。 

1. 依次选择“添加数据集”、“Azure Data Lake Store Gen2”  

    ![添加数据集](media/lab-data-flow-data-share/add-dataset-adls.png)

1. 选择“下一步” 

1. 展开“wwtaxidata”。  展开“波士顿出租车数据”。  请注意，最低可以共享到文件级别。 

1. 选择“波士顿出租车数据”文件夹，将整个文件夹添加到数据共享。  

1. 选择“添加数据集” 

1. 查看已添加的数据集。 应该已将 SQL 表和 ADLSGen2 文件夹添加到数据共享。 

1. 选择“继续” 

1. 在此屏幕中，可将收件人添加到数据共享。 添加的收件人将会收到加入数据共享的邀请。 对于本实验，必须添加 2 个电子邮件地址：

    1. 你所在的 Azure 订阅的电子邮件地址。 

        ![添加收件人](media/lab-data-flow-data-share/add-recipients.png)

    1. 添加名为 *janedoe@fabrikam.com* 的虚构数据使用者。

1. 在此屏幕中，可为数据使用者配置快照设置。 这样，他们就可以根据定义的时间间隔接收数据的定期更新。 

1. 选中“快照计划”，并使用“重复周期”下拉列表配置每小时数据刷新。    

1. 选择“创建”  。

    现已创建一个有效的数据共享。 让我们了解一下，当数据提供者在创建数据共享时可以看到哪些信息。 

1. 选择创建的数据共享，其标题为“DataProvider”。  可以通过选择“数据共享”中的“发送的共享”导航到该数据共享。   

1. 单击“快照计划”。 如果需要，可以禁用快照计划。 

1. 接下来，选择“数据集”选项卡。  创建此数据共享后，可将更多的数据集添加到其中。 

1. 选择“共享订阅”选项卡。  暂时不存在任何共享订阅，因为数据使用者尚未接受邀请。

1. 导航到“邀请”选项卡。  在此处可以看到等待接受的邀请列表。 

    ![等待接受的邀请](media/lab-data-flow-data-share/pending-invites.png)

1. 选择发送给 *janedoe@fabrikam.com* 的邀请。 选择“删除”。 如果收件人尚未接受该邀请，他们不再可以接受。 

1. 选择 **“历史记录”** 选项卡。暂时未显示任何内容，因为数据使用者尚未接受邀请并触发快照。 

### <a name="receiving-data-data-consumer-flow"></a>接收数据（数据使用者流）

评审数据共享后，接下来可以切换上下文，并充当数据使用者的身份。 

现在，你的收件箱中应已收到 Microsoft Azure 发来的 Azure Data Share 邀请。 启动 Outlook Web Access (outlook.com)，使用为你的 Azure 订阅提供的凭据登录。

在收到的电子邮件中，单击“查看邀请 >”。 从现在起，你将模拟数据使用者来体验接受数据提供者发出的加入其数据共享的邀请。 

![电子邮件邀请](media/lab-data-flow-data-share/email-invite.png)

系统可能会提示选择订阅。 请务必选择在本实验中使用的订阅。 

1. 单击标题为“DataProvider”的邀请。  

1. 在此“邀请”屏幕中，可以看到前面以数据提供者身份配置的数据共享的各项详细信息。 查看详细信息并接受使用条款（如果已提供）。

1. 选择已经为本实验提供的订阅和资源组。 

1. 对于“数据共享帐户”，请选择“DataConsumer”。   也可以创建新的数据共享帐户。 

1. 在“收到的共享名”旁边可以看到，默认共享名是数据提供者指定的名称。  为共享指定一个易记名称用于描述要接收的数据，例如 **TaxiDataShare**。

    ![接受邀请](media/lab-data-flow-data-share/consumer-accept.png)

1. 可以选择“立即接受并配置”或“稍后接受并配置”。   如果选择“立即接受并配置”，需要指定要将所有数据复制到的存储帐户。 如果选择“稍后接受并配置”，则会取消映射共享中的数据集，因此需要手动映射这些数据集。 稍后我们将选择此选项。 

1. 选择“稍后接受并配置”。  

    配置此选项时，将创建一个共享订阅，但数据没有复制目标，因为尚未映射任何目标。 

    接下来，我们为数据共享配置数据集映射。 

1. 选择“收到的共享”（在步骤 5 中指定的名称）。

    “触发快照”已灰显，但共享处于“活动”状态。  

1. 选择“数据集”选项卡。  请注意，每个数据集的状态为“未映射”，这意味着，数据没有可复制到的目标。 

    ![未映射的数据集](media/lab-data-flow-data-share/unmapped.png)

1. 依次选择“SQL 数据仓库表”、“+ 映射到目标”。 

1. 在屏幕的右侧，选择“目标数据类型”下拉列表。  

    可将 SQL 数据映射到各种数据存储。 在本例中，我们将映射到 Azure SQL 数据库。

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    （可选）选择“Azure Data Lake Store Gen2”作为目标数据类型。  
    
    （可选）选择前面一直在使用的订阅、资源组和存储帐户。 
    
    （可选）可以选择将 csv 或 parquet 格式的数据接收到 Data Lake 中。 

1. 在“目标数据类型”旁边，选择“Azure SQL 数据库”。  

1. 选择前面一直在使用的订阅、资源组和存储帐户。 

    ![映射到 SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. 在继续操作之前，需要通过运行提供的脚本在 SQL Server 中创建一个新用户。 首先，将提供的脚本复制到剪贴板。 

1. 打开新的 Azure 门户选项卡。请不要关闭现有的选项卡，因为稍后需要返回到其中。 

1. 在打开的新选项卡中，导航到“SQL 数据库”。 

1. 选择 SQL 数据库（订阅中应该只有一个 SQL 数据库）。 请注意不要选择 SQL 数据仓库。 

1. 选择“查询编辑器(预览版)” 

1. 使用 AAD 身份验证登录到查询编辑器。 

1. 运行数据共享中提供的查询（在步骤 14 中已复制到剪贴板）。 

    此命令可让 Azure Data Share 服务使用 Azure 服务的托管标识对 SQL Server 进行身份验证，以便能够将数据复制到其中。 

1. 返回到原始选项卡，选择“映射到目标”。 

1. 接下来，选择数据集中包含的 Azure Data Lake Gen2 文件夹，并将其映射到 Azure Blob 存储帐户。 

    ![存储](media/lab-data-flow-data-share/storage-map.png)

    映射所有数据集后，接下来可以开始从数据提供者接收数据。 

    ![已映射](media/lab-data-flow-data-share/all-mapped.png)
    
1. 选择“详细信息”。  

    可以看到，“触发快照”不再灰显，因为数据共享现在包含可复制到的目标。 

1. 选择“触发快照”->“完全复制”。 

    ![触发器](media/lab-data-flow-data-share/trigger-full.png)

    随即开始将数据复制到新的数据共享帐户。 在现实方案中，此数据将来自第三方。 

    复制数据大约需要 3-5 分钟。 可以单击“历史记录”选项卡来监视进度。  

    在等待期间，请导航到原始数据共享 (DataProvider)，并查看“共享订阅”的状态和“历史记录”选项卡。   可以看到，现已有一个活动的订阅；作为数据提供者，你还可以监视数据使用者何时开始接收了与其共享的数据。 

1. 导航回到数据使用者的数据共享。 触发器状态变为成功后，导航到目标 SQL 数据库和 Data Lake，以查看数据是否已进入相应的存储。 

祝贺你完成本实验！


