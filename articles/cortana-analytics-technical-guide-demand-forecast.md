---
title: "能源需求预测技术指南 | Microsoft Docs"
description: "Microsoft Cortana Intelligence 中用于预测能源需求的解决方案模板技术指南。"
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: ed2a17fd735c1b0e67cbf5d08450d36620d4c857
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>用于能源需求预测的 Cortana Intelligence 解决方案模板技术指南
## <a name="overview"></a>**概述**
解决方案模板可加速在 Cortana Intelligence Suite 上构建 E2E 演示的过程。 已部署的模板使用所需的 Cortana Intelligence 组件预配订阅，并在两者之间建立关系。 它还在数据管道中植入从数据模拟应用程序生成的示例数据。 从提供的链接下载数据模拟器并将它安装在本地计算机上；有关使用模拟器的说明，请参阅 readme.txt 文件。 模拟器生成的数据将生成数据管道，并开始生成机器学习预测，然后可在 Power BI 仪表板上将其可视化。

可在[此处](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)找到解决方案模板

部署过程会逐步引导用户完成设置解决方案凭据的多个步骤。 请务必记下这些凭据，例如在部署期间提供的解决方案名称、用户名和密码。

本文档旨在说明参考体系结构以及随此解决方案模板预配在订阅中的不同组件。 本文档还将演示如何使用自己的实际数据来替换示例数据，以便看到自己的数据的见解/预测。 此外，本文档将说明如果想要以自己的数据自定义解决方案，需要修改的解决方案模板部分。 最后提供有关构建此解决方案模板的 Power BI 仪表板的说明。

## <a name="details"></a>**详细信息**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>体系结构说明
部署解决方案时，会激活 Cortana Analytics Suite 中的各种 Azure 服务（例如事件中心、流分析、HDInsight、数据工厂、机器学习，等等）。 体系结构图全面展示了如何从端到端构造能源需求预测解决方案模板。 可以调查这些服务，单击随解决方案部署创建的解决方案模板图表即可。 以下部分将对每个片段进行说明。

## <a name="data-source-and-ingestion"></a>**数据源和引入**
### <a name="synthetic-data-source"></a>综合数据源
对于此模板，使用的数据源是从桌面应用程序生成的，将下载应用程序并于部署成功后在本地运行。 在解决方案模板图表上选择名为“能源预测数据模拟器”的第一个节点时，可以在属性列中找到下载及安装此应用程序的说明。 此应用程序会在解决方案流的余下部分使用的数据点或事件送入 [Azure 事件中心](#azure-event-hub)服务。

仅当它在计算机上运行时，事件生成应用程序才填充 Azure 事件中心。

### <a name="azure-event-hub"></a>Azure 事件中心
[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务是所述综合数据源提供的输入接收者。

## <a name="data-preparation-and-analysis"></a>**数据准备和分析**
### <a name="azure-stream-analytics"></a>Azure 流分析
使用 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)服务可为来自 [Azure 事件中心](#azure-event-hub)服务的输入流提供近乎实时的分析，将结果发布到 [Power BI](https://powerbi.microsoft.com) 仪表板，在 [Azure 存储](https://azure.microsoft.com/services/storage/)服务中存档所有原始传入事件，供 [Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)服务做后续处理。

### <a name="hdinsight-custom-aggregation"></a>HDInsight 自定义聚合
Azure HDInsight 服务用于运行 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本（由 Azure 数据工厂协调），提供使用 Azure 流分析服务存档的原始事件聚合。

### <a name="azure-machine-learning"></a>Azure 机器学习
使用 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)服务（由 Azure 数据工厂协调）预测特定区域（提供收到的输入数据）的将来用电量。

## <a name="data-publishing"></a>**数据发布**
### <a name="azure-sql-database-service"></a>Azure SQL 数据库服务
[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)服务用于存储（由 Azure 数据工厂管理）将 [Power BI](https://powerbi.microsoft.com) 仪表板使用的 Azure 机器学习服务收到的预测数据。

## <a name="data-consumption"></a>**数据使用**
### <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com) 服务用于显示仪表板，其中包含 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)服务提供的聚合，以及 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)中存储的、使用 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)服务生成的需求预测结果。 有关如何构建此解决方案模板的 Power BI 仪表板的说明，请参阅以下部分。

## <a name="how-to-bring-in-your-own-data"></a>**如何输入自己的数据**
本部分说明如何将自己的数据输入 Azure，以及对于放入此体系结构的数据，需要更改哪些方面。

放入的任何数据集不太可能符合用于此解决方案模板的数据集。 了解数据与需求对于如何修改此模板以配合自己的数据非常重要。 如果不太熟悉 Azure 机器学习服务，可以使用[如何创建第一个试验](machine-learning/studio/create-experiment.md)中的示例来获取简介。

以下部分介绍引入新数据集需要修改的模板部分。

### <a name="azure-event-hub"></a>Azure 事件中心
[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)服务支持丰富的格式，可以使用 CSV 或 JSON 格式将数据发布到中心。 Azure 事件中心对数据不做任何特殊处理，但用户必须了解提供的数据。

本文档不会介绍如何引入数据，但可以使用[事件中心 API](event-hubs/event-hubs-programming-guide.md)，轻松将事件或数据发送到 Azure 事件中心。

### <a name="azure-stream-analytics"></a>Azure 流分析
[Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)服务从数据流读取数据并将数据输出到任意数量的源，提供近乎实时分析。

对于能源需求预测的解决方案模板，Azure 流分析查询包含两个子查询，每个均从 Azure 事件中心服务使用事件作为输入，并且输出到两个不同的位置。 这些输出包括一个 Power BI 数据集和一个 Azure 存储位置。

可通过以下方式找到 [Azure 流分析](https://azure.microsoft.com/services/stream-analytics/)查询：

* 登录到 [Azure 门户](https://manage.windowsazure.com/)
* 找到部署解决方案时生成的流分析作业 ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png)。 一个作业用于将数据推送到 Blob 存储（例如 mytest1streaming432822asablob），另一种作业用于将数据推送到 Power BI（例如 mytest1streaming432822asapbi）。
* 选择

  * “输入”可查看查询输入
  * “查询”可查看查询本身
  * “输出”可查看不同的输出

有关 Azure 流分析查询构造的信息，请参阅 MSDN 上的 [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)（流分析查询参考）。

在此解决方案中，将数据集（具有近乎实时的传入数据流相关分析信息）输出到 Power BI 仪表板的 Azure 流分析作业在此解决方案模板中提供。 由于对传入数据格式有隐含的了解，需要根据数据格式更改这些查询。

其他 Azure 流分析作业将所有[事件中心](https://azure.microsoft.com/services/event-hubs/)事件输出到 [Azure 存储](https://azure.microsoft.com/services/storage/)，由于将完整的事件信息流到存储，因此无论数据格式为何都无需进行修改。

### <a name="azure-data-factory"></a>Azure 数据工厂
[Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)服务协调数据的移动和处理。 在能源需求预测解决方案模板中，数据工厂由 12 个[管道](data-factory/concepts-pipelines-activities.md)组成，使用不同的技术移动和处理数据。

  可以打开随解决方案部署创建的解决方案模板图示底部的数据工厂节点来访问数据工厂。 在 Azure 门户中查看数据工厂。 如果在数据集下看到错误，可将其忽略，这些错误的原因是启动数据生成器之前已部署数据工厂。 这些错误不会防碍数据工厂的正常运行。

本部分介绍 [Azure 数据工厂](https://azure.microsoft.com/documentation/services/data-factory/)中包含的必要[管道](data-factory/concepts-pipelines-activities.md)和[活动](data-factory/concepts-pipelines-activities.md)。 下图是解决方案的图示视图：

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

此工厂的五个管道包含 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本，用于分区及聚合数据。 可以看到，脚本位在安装期间创建的 [Azure 存储](https://azure.microsoft.com/services/storage/)帐户中。 其位置为：demandforecasting\\\\script\\\\hive\\\\（或 https://[解决方案名称].blob.core.windows.net/demandforecasting）。

类似于 [Azure 流分析查询](#azure-stream-analytics-1)，[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本的对传入的数据格式有隐含了解，需要根据数据格式和[特征设计](machine-learning/team-data-science-process/create-features.md)要求更改这些查询。

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
此[管道](data-factory/concepts-pipelines-activities.md)包含一个活动 - 使用 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) 的 [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) 活动，它运行 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本，通过 Azure 流分析作业将变电站级别每隔 10 秒流式传输的需求数据聚合成每小时区域级别，然后放入 [Azure 存储](https://azure.microsoft.com/services/storage/)中。

此分区任务的 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本为 ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
此[管道](data-factory/concepts-pipelines-activities.md)包含两个活动：

* 使用 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) 的 [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) 活动，它运行 Hive 脚本，在 Azure 流分析作业期间将变电站级别的每小时历史需求数据聚合成每小时区域级别，然后放入 Azure 存储中
* [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动，将聚合数据从 Azure 存储 Blob 移到解决方案模板安装期间预配的 Azure SQL 数据库中。

此任务的 [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) 脚本为 ***AggregateDemandHistoryRegion.hql***。

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
这些[管道](data-factory/concepts-pipelines-activities.md)包含多个活动，其最终结果为来自与此解决方案模板关联的 Azure 机器学习试验评分的预测。 这些管道几乎完全相同，差别在于它们各自只处理不同的区域（由传入 ADF 管道的不同 RegionID 完成）以及每个区域的 hive 脚本。  
此管道包含的活动为：

* 使用 [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) 的[HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) 活动运行 Hive 脚本来执行 Azure 机器学习试验所需的聚合及特征设计。 此任务的 Hive 脚本为相应的 ***PrepareMLInputRegionX.hql***。
* [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动将来自 [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) 活动的结果移到可供 [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 活动访问的单个 Azure 存储 Blob。
* 调用 Azure 机器学习试验的 [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) 活动会导致将结果放入单个 Azure 存储 Blob。

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
此[管道](data-factory/concepts-pipelines-activities.md)包含单个活动 - [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动，将 Azure 机器学习试验的结果从相应 ***MLScoringRegionXPipeline*** 移到随解决方案模板安装一起预配的 Azure SQL 数据库。

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
此[管道](data-factory/concepts-pipelines-activities.md)包含单个活动 - [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动，将聚合的持续需求数据从 ***LoadHistoryDemandDataPipeline*** 移到随解决方案模板安装一起预配的 Azure SQL 数据库。

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline、CopySubstationDataPipeline、CopyTopologyDataPipeline*
此[管道](data-factory/concepts-pipelines-activities.md)包含单个活动 - [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) 活动，将安装解决方案模板时上传到 Azure 存储 Blob 的区域/变电站/拓扑参考数据移到安装解决方案模板过程中预配的 Azure SQL 数据库。

### <a name="azure-machine-learning"></a>Azure 机器学习
用于此解决方案模板的 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)试验提供区域需求预测。 该试验因使用的数据集而有所不同，需要专门针对引入的数据进行修改或替换。

## <a name="monitor-progress"></a>**监视进度**
启动数据生成器后，管道将开始合成，解决方案的不同组件遵循数据工厂发出的命令开始操作。 可通过两种方式监视管道。

1. 检查 Azure Blob 存储中的数据。

    某个流分析作业会将原始传入数据写入 Blob 存储。 如果在成功部署解决方案的屏幕中单击解决方案的“Azure Blob 存储”组件，然后在右窗格中单击“打开”，会转到 [Azure 门户](https://portal.azure.com)。 进入门户后，单击“Blob”。 在随后出现的面板中，可以看到容器列表。 单击“energysadata”。 在随后出现的面板中，可以看到“demandongoing”文件夹。 在 rawdata 文件夹内，可以看到具有 date=2016-01-28 等名称的文件夹。如果看到这些文件夹，则表示原始数据已成功在计算机上生成并存储在 Blob 存储中。 应会在这些文件夹中看到具有有限 MB 大小的文件。
2. 检查 Azure SQL 数据库中的数据。

    管道的最后一个步骤是将数据（例如机器学习的预测数据）写入 SQL 数据库。 最多可能需要两个小时，数据才会出现在 SQL 数据库中。 监视 SQL 数据库中有多少数据的方法之一是使用 [Azure 门户](https://manage.windowsazure.com/)。 在左侧面板中找到“SQL 数据库![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png)”并单击它。 然后找到自己的数据库（例如 demo123456db）并单击它。 在下一页的“连接到数据库”部分下，单击“对 SQL 数据库运行 Transact-SQL 查询”。

    随后，可以单击“新建查询”，并查询行数（例如，"select count(*) from DemandRealHourly"。随着数据库成长，表中的行数也应会增加）。
3. 检查 Power BI 仪表板中的数据。

    可以设置 Power BI 的热路径仪表板，监视原始传入数据。 请遵循“Power BI 仪表板”部分中的说明。

## <a name="power-bi-dashboard"></a>**Power BI 仪表板**
### <a name="overview"></a>概述
本部分介绍如何设置 Power BI 仪表板，可视化来自 Azure 流分析（热路径）的实时数据，以及来自 Azure 机器学习（冷路径）的预测结果。

### <a name="setup-hot-path-dashboard"></a>设置热路径仪表板
以下步骤逐步说明如何可视化部署解决方案时生成的流分析作业的实时数据输出。 若要执行以下步骤，需要一个 [Power BI online](http://www.powerbi.com/) 帐户。 如果没有帐户，可以[创建一个](https://powerbi.microsoft.com/pricing)。

1. 在 Azure 流分析 (ASA) 中添加 Power BI 输出。

   * 需要根据 [Azure 流分析和 Power BI：用于实时查看流数据的实时分析仪表板](stream-analytics/stream-analytics-power-bi-dashboard.md)中的说明，将 Azure 流分析作业的输出设置为 Power BI 仪表板。
   * 在 [Azure 门户](https://manage.windowsazure.com)中找到流分析作业。 作业名称应该是：解决方案名称+“streamingjob”+随机数字+“asapbi”（例如 demostreamingjob123456asapbi）。
   * 添加 ASA 作业的 PowerBI 输出。 将“输出别名”设置为“PBIoutput”。 将“数据集名称”和“表名称”设置为“EnergyStreamData”。 添加输出后，单击页面底部的“启动”启动流分析作业。 此时应会出现确认消息（例如，“已成功启动流分析作业 myteststreamingjob12345asablob”）。
2. 登录到 [Power BI online](http://www.powerbi.com)

   * 在“我的工作区”的左侧面板“数据集”部分中，应会看到新数据集出现在 Power BI 的左侧面板上。 这是在上一步骤中从 Azure 流分析推送的流数据。
   * 确保“可视化效果”窗格已打开，并显示在屏幕的右侧。
3. 创建“按时间戳列出的需求”磁贴：

   * 在左侧面板“数据集”部分中单击数据集“EnergyStreamData”。
   * 单击“折线图”图标 ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png)。
   * 在“字段”面板中单击“EnergyStreamData”。
   * 单击“时间戳”，确保它显示在“轴”下面。 单击“加载”，确保它显示在“值”下面。
   * 单击顶部的“保存”，将报告命名为“EnergyStreamDataReport”。 名为“EnergyStreamDataReport”的报告随即显示在左侧“导航”窗格的“报告”部分中。
   * 单击此折线图右上角的“固定视觉对象”![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) 图标，“固定到仪表板”窗口随即出现，可在其中选择仪表板。 选择“EnergyStreamDataReport”，并单击“固定”。
   * 在仪表板上将鼠标悬停在此磁贴中，单击右上角的“编辑”图标，将其标题更改为“按时间戳列出的需求”
4. 根据相应的数据集创建其他仪表板磁贴。 最终的仪表板视图：![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>设置冷路径仪表板
在冷路径数据管道中，基本目标是获取每个区域的需求预测。 Power BI 将连接到充当其数据源、用于存储预测结果的 Azure SQL 数据库。

> [!NOTE]
> 1) 为仪表板收集足够多的预测结果需要花费几个小时。 建议在启动数据生成器 2 到 3 个小时后再开始执行此过程。 2) 此步骤的先决条件是下载并安装免费软件 [Power BI desktop](https://powerbi.microsoft.com/desktop)。
>
>

1. 获取数据库凭据。

   在继续下一步之前，需要获取**数据库服务器名称、数据库名称、用户名和密码**。 下面提供了有关如何查找这些信息的指导。

   * 解决方案模板图示上的“Azure SQL 数据库”变为绿色后，请单击它，并单击“打开”。 随后会转到 Azure 门户，数据库信息页同时打开。
   * 在该页上，找到“数据库”部分。 其中列出了已创建的数据库。 数据库名称应该是**“解决方案名称 + 随机数字 +‘db’”**（例如“mytest12345db”）。
   * 单击数据库，然后可以在新的弹出面板的顶部找到数据库服务器名称。 数据库服务器名称应是 `"Your Solution Name + Random Number + 'database.windows.net,1433'"`（例如“mytest12345.database.windows.net,1433”）。
   * 数据库**用户名**和**密码**与部署解决方案期间记下的用户名和密码相同。
2. 更新冷路径 Power BI 文件的数据源

   * 确保已安装最新版本的 [Power BI desktop](https://powerbi.microsoft.com/desktop)。
   * 在下载的“DemandForecastingDataGeneratorv1.0”文件夹中，双击“Power BI Template\DemandForecastPowerBI.pbix”文件。 初始的可视化效果基于虚构数据。 **注意：**如果看到错误消息，请确保已安装最新版本的 Power BI Desktop。

     打开该文件后，请在顶部单击“编辑查询”。 在弹出窗口中，双击右侧面板上的“源”。
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * 在弹出窗口中，将“服务器”和“数据库”替换为自己的服务器和数据库名称，并单击“确定”。 对于服务器名称，请确保指定端口 1433 (**YourSolutionName.database.windows.net, 1433**)。 忽略屏幕上出现的警告消息。
   * 下一个弹出窗口的左侧窗格中出现了两个选项（“Windows”和“数据库”）。 单击“数据库”，填充“用户名”和“密码”（这是首次部署解决方案和创建 Azure SQL 数据库时输入的用户名与密码）。 在“选择要将这些设置应用到的级别”中，选中数据库级别选项。 然后单击“连接”。
   * 返回上一页后，请关闭窗口。 此时会弹出一条消息 - 请单击“应用”。 最后，单击“保存”保存更改。 Power BI 文件现在已与服务器建立连接。 如果可视化效果是空的，请务必单击图例右上角的橡皮擦图标清除可视化效果中的选择内容，这样即可查看所有数据。 使用刷新按钮显示可视化效果中的新数据。 最初，只会在可视化效果中看到种子数据，因为数据工厂计划为每隔 3 小时刷新一次。 3 小时后，刷新数据时，可以看到新预测数据反映在可视化效果中。
3. （可选）将冷路径仪表板发布到 [Power BI online](http://www.powerbi.com/)。 请注意，此步骤需要 Power BI 帐户（或 Office 365 帐户）。

   * 单击“发布”，几秒钟后会出现一个窗口，显示“发布到 Power BI 成功!”和 和一个绿色的复选标记。 单击下面的“在 Power BI 中打开 demoprediction.pbix”链接。 若要查找详细说明，请参阅[从 Power BI Desktop 发布](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop)。
   * 若要创建新仪表板，请在左侧窗格中单击“仪表板”部分旁边的 **+** 号。 为此新仪表板输入名称“需求预测演示”。
   * 打开报告后，请单击 ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png)，将所有可视化效果固定到仪表板。 要查找详细说明，请参阅[从报表将磁贴固定到 Power BI 仪表板](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report)。
     转到仪表板页并调整可视化效果的大小和位置并编辑其标题。 若要查找有关如何编辑磁贴的详细说明，请参阅[编辑磁贴 - 重设大小、移动、重命名、固定、删除、添加超链接](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename)。 下面是固定了一些冷路径可视化效果的仪表板示例。

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. （可选）计划数据源的刷新。

   * 要计划数据刷新，请将鼠标悬停在 **EnergyBPI-Final** 数据集上，单击 ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png)，然后选择“计划刷新”。
     **注意：**如果看到警告消息，请单击“编辑凭据”，确保数据库凭据与步骤 1 中所述相同。

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * 展开“计划刷新”部分。 打开“保持数据最新”。
   * 根据需要计划刷新。 若要查找详细信息，请参阅 [Data refresh in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)（Power BI 中的数据刷新）。

## <a name="how-to-delete-your-solution"></a>**如何删除解决方案**
在不经常使用解决方案的情况下，请务必停止数据生成器，因为运行数据生成器会产生较高的费用。 如果不使用解决方案，请将其删除。 删除解决方案会删除部署该解决方案时在订阅中预配的所有组件。 要删除解决方案，请在解决方案模板左侧面板中单击该解决方案的名称，并单击“删除”。

## <a name="cost-estimation-tools"></a>**成本估算工具**
以下两个工具可帮助进一步了解在订阅中运行能源需求预测解决方案模板产生的总成本：

* [Microsoft Azure Cost Estimator Tool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure Cost Estimator Tool (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**致谢**
本文由 Microsoft 数据科研人员 Yijing Chen 与软件工程师 Qiu Min 共同撰写。
