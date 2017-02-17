---
title: "深入了解预测车辆的运行状况和行驶习惯 - Azure | Microsoft Docs"
description: "通过 Cortana Intelligence 的功能获得对车辆运行状况和驾驶习惯的实时和预测性深入了解。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 898b93ad95ab812b8b6a538048a86a8cc8951101


---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>车辆遥测分析解决方案操作手册：深入探讨解决方案
此**菜单**链接至此操作手册的该部分： 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

本节深入研究了解决方案体系结构中描述的每个阶段，包括用于自定义的说明和指针。 

## <a name="data-sources"></a>数据源
该解决方案使用两个不同的数据源：

* **模拟车辆信号和诊断数据集**和 
* **车辆目录**

车辆远程信息处理模拟器是此解决方案的一部分。 它在给定的时间点根据车辆的状态以及驾驶模式发出对应的诊断信息和信号。 单击 [Vehicle Telematics Simulator](http://go.microsoft.com/fwlink/?LinkId=717075)（车辆远程信息处理模拟器）为基于自身要求的自定义下载 **Vehicle Telematics Simulator Visual Studio Solution**车辆远程信息处理模拟器 Visual Studio 解决方案。 车辆目录包含具有 VIN 到模型映射的引用数据集。

![车辆远程信息处理模拟器](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)

*图 1 - 车辆远程信息处理模拟器*

这是一个 JSON 格式的数据集，它包含以下架构。

| 列 | 说明 | 值 |
| --- | --- | --- |
| VIN |随机生成的车辆标识号 |这是从 10,000 个随机生成的车辆标识号的主列表中获得的。 |
| 外部温度 |车辆行驶的外部温度 |从 0 - 100 随机生成编号 |
| 引擎温度 |车辆的引擎温度 |从 0 - 500 随机生成编号 |
| Speed |车辆正在行驶时的引擎转速 |从 0 - 100 随机生成编号 |
| 燃料 |车辆的燃料油位 |从 0 - 100 随机生成编号（指示燃料油位百分比） |
| EngineOil |车辆的引擎燃料油位 |从 0 - 100 随机生成编号（指示引擎燃料油位百分比） |
| 轮胎压力 |车辆的轮胎压力 |从 0 - 50 随机生成编号（指示轮胎压力级别百分比） |
| 里程表 |车辆的里程表读数 |从 0 - 200000 随机生成编号 |
| Accelerator_pedal_position |车辆的加速踏板位置 |从 0 - 100 随机生成编号（指示加速器级别百分比） |
| Parking_brake_status |指示车辆是否停放 |是或否 |
| Headlamp_status |指示头灯是否开启 |是或否 |
| Brake_pedal_status |指示是否踩下刹车踏板 |是或否 |
| Transmission_gear_position |车辆的变速齿轮档位 |状态：第一、第二、第三、第四、第五、第六、第七、第八档 |
| Ignition_status |指示车辆是正在驾驶中还是已停止 |是或否 |
| Windshield_wiper_status |指示是否启用了挡风玻璃雨刮器 |是或否 |
| ABS |指示 ABS 是否已啮合 |是或否 |
| Timestamp |创建数据点时的时间戳 |日期 |
| 城市 |车辆的位置 |此解决方案中的四座城市：贝尔维尤、雷德蒙德、瑟马米什、西雅图 |

车辆模型引用数据集包括到模型映射的 VIN。 

| VIN | 模型 |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |轿车 |
| 8J0U8XCPRGW4Z3NQE |混合 |
| WORG68Z2PLTNZDBI7 |家庭轿车 |
| JTHMYHQTEPP4WBMRN |轿车 |
| W9FTHG27LZN1YWO0Y |混合 |
| MHTP9N792PHK08WJM |家庭轿车 |
| EI4QXI2AXVQQING4I |轿车 |
| 5KKR2VB4WHQH97PF8 |混合 |
| W9NSZ423XZHAONYXB |家庭轿车 |
| 26WJSGHX4MA5ROHNL |敞篷车 |
| GHLUB6ONKMOSI7E77 |旅行车 |
| 9C2RHVRVLMEJDBXLP |小型车 |
| BRNHVMZOUJ6EOCP32 |小型 SUV |
| VCYVW0WUZNBTM594J |跑车 |
| HNVCE6YFZSA5M82NY |中型 SUV |
| 4R30FOR7NUOBL05GJ |旅行车 |
| WYNIIY42VKV6OQS1J |大型 SUV |
| 8Y5QKG27QET1RBK7I |大型 SUV |
| DF6OX2WSRA6511BVG |双门跑车 |
| Z2EOZWZBXAEW3E60T |轿车 |
| M4TV6IEALD5QDS3IR |混合 |
| VHRA1Y2TGTA84F00H |家庭轿车 |
| R0JAUHT1L1R3BIKI0 |轿车 |
| 9230C202Z60XX84AU |混合 |
| T8DNDN5UDCWL7M72H |家庭轿车 |
| 4WPYRUZII5YV7YA42 |轿车 |
| D1ZVY26UV2BFGHZNO |混合 |
| XUF99EW9OIQOMV7Q7 |家庭轿车 |
| 8OMCL3LGI7XNCC21U |敞篷车 |
| ……. | |

### <a name="to-generate-simulated-data"></a>若要生成模拟数据
1. 要下载数据模拟器包，请单击车辆远程信息处理模拟器节点右上方的箭头。 在本地计算机上保存并解压缩文件。 ![车辆遥测分析解决方案蓝图](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telemetry-blueprint.png) *图 2 - 车辆遥测分析解决方案蓝图*
2. 在本地计算机上，转到解压缩车辆远程信息处理模拟器包的文件夹。 ![车辆远程信息处理模拟器](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telematics-simulator-folder.png) *图 3 - 车辆远程信息处理模拟器*
3. 执行应用程序 **CarEventGenerator.exe**。

### <a name="references"></a>参考
[Vehicle Telematics Simulator Visual Studio Solution](http://go.microsoft.com/fwlink/?LinkId=717075)（车辆远程信息处理模拟器 Visual Studio 解决方案） 

[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)

[Azure 数据工厂](https://azure.microsoft.com/documentation/learning-paths/data-factory/)

## <a name="ingestion"></a>引入
Azure 事件中心、流分析和数据工厂的组合可用于获取车辆信号、诊断事件以及实时和批处理分析。 所有这些组件都将作为解决方案部署的一部分进行创建和配置。 

### <a name="real-time-analysis"></a>实时分析
使用事件中心 SDK 将车辆远程信息处理模拟器生成的事件发布到事件中心。 流分析作业从事件中心引入这些事件，并实时处理数据以分析车辆运行状况。 

![事件中心仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

*图 4 - 事件中心仪表板*

![处理数据的流分析作业](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 

*图 5 - 处理数据的流分析作业*

流分析作业；

* 从事件中心引入数据 
* 执行与引用数据的连接以将车辆 VIN 映射到相应的模型 
* 将其保留在 Azure Blob 存储中以进行丰富的批处理分析。 

使用下列流分析查询将数据保留到 Azure Blob 存储中。 

![用于数据引入的流分析作业查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*图 6 - 用于数据引入的流分析作业查询*

### <a name="batch-analysis"></a>批处理分析
我们还生成额外的模拟车辆信号卷和诊断数据集，用于更丰富的批处理分析。 这是确保批处理的良好代表数据卷所必需的。 为此，我们在 Azure 数据工厂工作流中使用名为“PrepareSampleDataPipeline”的管道来生成一年的模拟车辆信号和诊断数据集。 为基于自身要求的自定义单击 [Data Factory custom activity](http://go.microsoft.com/fwlink/?LinkId=717077)（数据工厂自定义活动），下载数据工厂自定义 DotNet 活动 Visual Studio 解决方案。 

![为批处理工作流准备示例数据](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*图 7 - 为批处理工作流准备示例数据*

管道由自定义 ADF .Net Activity 活动组成，如下所示：

![PrepareSampleDataPipeline 活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

*图 8 - PrepareSampleDataPipeline*

一旦管道成功执行，并且“RawCarEventsTable”数据集被标记为“就绪”，就会生成一年的模拟车辆信号和诊断数据。 可在“connectedcar”容器下的存储帐户中查看以下文件夹和文件：

![PrepareSampleDataPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*图 9 - PrepareSampleDataPipeline 输出*

### <a name="references"></a>参考
[用于流引入的 Azure 事件中心 SDK](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Azure 数据工厂数据移动功能](../data-factory/data-factory-data-movement-activities.md)
[Azure 数据工厂 DotNet 活动](../data-factory/data-factory-use-custom-activities.md)

[Azure Data Factory DotNet activity visual studio solution for preparing sample data](http://go.microsoft.com/fwlink/?LinkId=717077)（用于准备示例数据的 Azure 数据工厂 DotNet 活动 Visual Studio 解决方案） 

## <a name="partition-the-dataset"></a>对数据集进行分区
原始半结构化车辆信号和诊断数据集在数据准备步骤中被分区为年/月格式。 当第一个帐户填充满时，此分区通过启用从一个 blob 帐户到下一个 blob 帐户的故障转移，从而提高了查询和可缩放的长期存储的效率。 

>[!NOTE] 
>解决方案中的此步骤仅适用于批处理。

输入和输出数据的数据管理：

* **输出数据**（标记为 PartitionedCarEventsTable）将在客户的“Data Lake”中以基础/“最原始”数据的形式保存很长一段时间。 
* 到此管道的**输入数据**通常将被丢弃，因为输出数据对输入具有完全保真度 - 它只是被更好地存储（分区）以供后续使用。

![对汽车事件工作流分区](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)

*图 10 - 对汽车事件工作流分区*

在“PartitionCarEventsPipeline”中使用 Hive HDInsight 活动对原始数据进行分区。 用年/月对步骤 1 中生成的一年的示例数据进行分区。 使用分区来生成一年中每个月（总计 12 个分区）的车辆信号和诊断数据。 

![PartitionCarEventsPipeline 活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)

*图 11 - PartitionCarEventsPipeline*

***PartitionConnectedCarEvents Hive 脚本***

用名为“partition car events.hql”的下列 Hive 脚本进行分区，它位于所下载的 zip 的“\ demo \ sap \ connected car \ scripts”文件夹中。 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

一旦管道成功执行，你将在“connectedcar”容器下的存储帐户中看到以下已生成的分区。

![已分区的输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

*图 12 - 已分区的输出*

数据已优化，更易于管理，并为未来处理做好了准备，以便获得丰富的批处理深入了解。 

## <a name="data-analysis"></a>数据分析
在本节中，你将了解如何结合 Azure 流分析、Azure 机器学习、Azure 数据工厂和 Azure HDInsight 来丰富关于车辆运行状况和驾驶习惯的高级分析。 此处有三个小节：

1. **机器学习**：此小节包含我们在此解决方案中，用于预测需要服务维护的车辆和由于安全问题而需要召回的车辆所使用的异常检测试验的信息。
2. **实时分析**：此小节包含有关使用流分析查询语言的实时分析的信息，以及使用自定义应用程序实时操作机器学习试验。
3. **批处理分析**：此小节包含有关使用 Azure HDInsight 转换和处理批处理数据，以及通过 Azure 数据工厂来操作 Azure 机器学习的信息。

### <a name="machine-learning"></a>机器学习
我们的目标是根据特定运行状况统计来预测需维护或召回的车辆。 我们做出如下假设

* 如果以下三个条件之一为真，则车辆需要**服务维护**：
  
  * 轮胎压力低
  * 引擎燃料油位低
  * 引擎温度高
* 如果以下条件之一为真，那么车辆可能具有**安全问题**并需要**召回**：
  
  * 引擎温度高，但室外温度低
  * 引擎温度低，但室外温度低高

基于先前的要求，我们创建了两个独立模型来检测异常，一个用于车辆维护检测，一个用于车辆召回检测。 在这两个模型中，使用内置主体组件分析 (PCA) 算法来检测异常。 

**维护检测模型**

如果三个指标（轮胎压力、引擎燃料或引擎温度）中的一个满足其对应的条件，维护检测模型将报告异常。 因此，我们只需要在构建模型时考虑这三个变量。 在我们的 Azure 机器学习试验中，我们首先使用**在数据集中选择列**模块来提取这三个变量。 接下来，我们使用基于 PCA 的异常检测模块来构建异常检测模型。 

主体组件分析 (PCA) 是一种已建立的机器学习技术，可应用于特性选择、分类和异常检测。 PCA 将一组包含可能相关的变量的事例转换为一组被称为主体组件的值。 基于 PCA 的建模的关键理念是将数据投影到较低维的空间，以便更容易地标识特性和异常。

对于检测模型的每个新输入，异常探测器首先计算其在特征向量上的投影，然后计算规范化重构错误。 此标准化错误是异常情况得分。 错误级别越高，实例越异常。 

在维护检测问题中，每个记录可以被认为是由轮胎压力、引擎燃料和引擎温度坐标定义的三维空间中的点。 为了捕获这些异常情况，我们可以使用 PCA 将三维空间中的原始数据投影到二维空间上。 因此，我们将 PCA 中要使用的组件的参数编号设置为 2。 在应用基于 PCA 的异常情况检测中，此参数扮演着重要的角色。 在使用 PCA 投影数据之后，我们可以更轻松地标识这些异常情况。

**召回异常情况检测模型**在召回异常情况检测模型中，我们以类似的方式使用数据集中的选择列和基于 PCA 的异常情况检测模块。 具体而言，首先使用“数据集中的选择列”模块提取三个变量 - 引擎温度、外部温度和速度。 速度变量也包括在内，因为引擎温度通常与速度有关。 接下来使用基于 PCA 的异常情况检测模块，将来自三维空间的数据投影到二维空间上。 召回条件已满足，因此当引擎温度和外部温度高度负相关时，需要召回车辆。 使用基于 PCA 的异常情况检测算法，我们可以捕获在执行 PCA 后的异常情况。 

在训练任一模型时我们需要使用正常数据，作为输入数据，它无需维护或召回以训练基于 PCA 的异常情况监测模型。 在评分实验中，我们使用经过训练的异常情况检测模型来检测车辆是否需要维护或召回。 

### <a name="real-time-analysis"></a>实时分析
以下流分析 SQL 查询用于获取所有重要车辆参数的平均值，例如车辆速度、燃料油位、引擎温度、里程表读数、轮胎压力、引擎燃料油位等。 平均值用于检测异常情况、发出警报并确定在特定区域操作的车辆的整体运行状况，然后将其与人口统计相关联。 

![用于实时处理的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

*图 13 - 用于实时处理的流分析查询*

所有平均值都是通过 3 秒钟的 TumblingWindow 计算的。 在这种情况下使用 TubmlingWindow，因为需要非重叠且连续的时间间隔。 

要了解有关 Azure 流分析中所有“窗口化”功能的详细信息，请单击 [窗口化（Azure 流分析）](https://msdn.microsoft.com/library/azure/dn835019.aspx)。

**实时预测**

一个应用程序作为解决方案的一部分实时地操作机器学习。 这个名为“RealTimeDashboardApp”的应用程序是作为解决方案部署的一部分被创建和配置的。 应用程序执行以下操作：

1. 监听事件中心实例，其中流分析会持续发布模式中的事件。 ![用于发布数据的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) *图 14 - 用于将数据发布至输出事件中心实例的流分析查询* 
2. 对于此应用程序收到的每个事件： 
   
   * 使用机器学习请求 - 响应评分 (RRS) 终结点来处理数据。 作为部署的一部分，RRS终结点自动被发布。
   * 使用推送 API 将 RRS 输出发布到 Power BI 数据集。

此模式也适用于你希望将业务线 (LoB) 应用程序与实时分析流集成的场景，如警报、通知和消息传递。

单击 [RealtimeDashboardApp 下载](http://go.microsoft.com/fwlink/?LinkId=717078)以下载用于自定义的 RealtimeDashboardApp Visual Studio 解决方案。 

**若要执行实时仪表板应用程序**

1. 单击图示视图上的 Power BI 节点，然后单击属性窗格上的“下载实时仪表板应用程序”链接。 ![Power BI 仪表板设置说明](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-powerbi-dashboard-setup.png) *图 15 - Power BI 仪表板设置说明*
2. 解压缩并本地保存 ![RealtimeDashboardApp 文件夹](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) *图 16 – RealtimeDashboardApp 文件夹*  
3. 执行应用程序 RealtimeDashboardApp.exe
4. 提供有效的 Power BI 凭据，登录并单击“接受” ![实时仪表板应用登录到 Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![实时仪表板应用完成 Power BI 登录](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*图 17 - RealtimeDashboardApp：登录到 Power BI*

>[!NOTE] 
>如果要刷新 Power BI 数据集，请使用“flushdata”参数执行RealtimeDashboardApp： 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>批处理分析
此处的目标是展示 Contoso Motors 如何通过 Azure 计算能力利用大数据来获得关于驾驶模式、使用行为和车辆运行状况的丰富深入了解。 这使我们可以：

* 通过提供关于驾驶习惯和燃料高效驾驶行为的深入了解，改善客户体验并降低费用
* 主动了解客户及其驾驶模式，从而管理业务决策，并提供最佳的产品和服务

在此解决方案中，我们将下列度量设为目标：

1. **过激驾驶行为**：标识模型、位置、驾驶条件和一年时间中的趋势，以获得对过激驾驶模式的深入了解。 Contoso Motors 可以将这些深入了解用于市场营销活动，推动新的个性化功能和基于使用的保险。
2. **高效的驾驶行为**：标识模型、位置、驾驶条件和一年时间中的趋势，以获得对燃料高效驾驶模式的深入了解。 Contoso Motors 可将这些深入了解用于市场营销活动，推动新功能并主动向驾驶员报告，以获得成本效益和环保的驾驶习惯。 
3. **召回模型**：通过操作异常情况检测机器学习实验来标识需要召回的模型

让我们来看看每个度量的详细信息，

**过激驾驶模式**

使用 Hive 在名为“AggresiveDrivingPatternPipeline”的管道中处理分区的车辆信号和诊断数据，以确定表现出主动驾驶模式的模型、位置、车辆、驾驶条件和其他参数。

![过激驾驶模式工作流](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 
*图 18 - 过激驾驶模式工作流*


***过激驾驶模式 Hive 查询***

用于分析过激驾驶条件模式的名为“aggressive driving.hql”的 Hive 脚本位于下载的 zip 的“\demo\src\connectedcar\scripts”文件夹中。 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


它通过车辆的变速齿轮档位、制动踏板状态和速度的组合，基于高速情况下的制动模式来检测鲁莽/过激驾驶行为。 

一旦管道成功执行，你将在“connectedcar”容器下的存储帐户中看到以下已生成的分区。

![AggressiveDrivingPatternPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 

*图 19 - AggressiveDrivingPatternPipeline 输出*

**燃料高效驾驶模式**

在名为“FuelEfficientDrivingPatternPipeline”的管道中处理分区的车辆信号和诊断数据。 用 Hive 来确定显示燃料高效驾驶模式的模型、位置、车辆、驾驶条件和其他属性。

![燃料高效驾驶模式](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*图 20 - 燃料高效驾驶模式工作流*

***燃料高效驾驶模式 Hive 查询***

用于分析过激驾驶条件模式的名为“fuel efficient driving.hql”的 Hive 脚本位于下载的zip的“\demo\src\connectedcar\scripts”文件夹中。 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


它通过车辆的变速齿轮档位、制动踏板状态，速度和加速器踏板位置的组合，基于加速、刹车和速度模式来检测燃油高效驾驶行为。 

一旦管道成功执行，你将在“connectedcar”容器下的存储帐户中看到以下已生成的分区。

![FuelEfficientDrivingPatternPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*图 21 - FuelEfficientDrivingPatternPipeline 输出*

**召回预测**

作为解决方案部署的一部分，机器学习实验作为 Web 服务来预配和发布。 批处理评分终点在此工作流中被使用，它被注册为与数据工厂相链接的服务，并通过使用数据工厂批处理评分活动进行操作。

![机器学习终结点](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

*图 22 - 在数据工厂中注册为链接服务的机器学习终结点*

在 DetectAnomalyPipeline 中使用被注册的链接的服务对使用异常情况检测模型的数据进行评分。 

![数据工厂中的机器学习批处理评分活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png) 

*图 23 - 数据工厂中的 Azure 机器学习批处理评分活动* 

对于数据准备，在此管道中要执行几个步骤，以便它可以通过批处理评分 Web 服务被操作。 

![用于预测需要召回的车辆的 DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png) 

*图 24 - 用于预测需要召回的车辆的 DetectAnomalyPipeline* 

***异常情况检测 Hive 查询***

一旦评分完成，使用 HDInsight 活动来处理和聚合被模型分类为异常情况（概率分数为 0.60 或更高）的数据。

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


一旦管道成功执行，你将在“connectedcar”容器下的存储帐户中看到以下已生成的分区。

![DetectAnomalyPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*图 25 - DetectAnomalyPipeline 输出*

## <a name="publish"></a>发布

### <a name="real-time-analysis"></a>实时分析
流分析作业中的查询之一将事件发布到输出事件中心实例。 

![流分析作业发布到输出事件中心实例](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*图 26 - 流分析作业发布到输出事件中心实例*

![发布到输出事件中心实例的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*图 27 - 发布到输出事件中心实例的流分析查询*

此事件的流由解决方案中包含的 RealTimeDashboardApp 消耗。 此应用程序利用机器学习请求 - 响应 Web 服务进行实时评分，并将结果数据发布到 Power BI 数据集以供使用。 

### <a name="batch-analysis"></a>批处理分析
批处理和实时处理的结果被发布到 Azure SQL数据库表以供使用。 Azure SQL Server、数据库和表作为设置脚本的一部分自动创建。 

![批处理结果复制到数据市场工作流](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*图 28 - 批处理结果复制到数据市场工作流*

![流分析作业发布到数据市场](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*图 29 - 流分析作业发布到数据市场*

![流分析作业中的数据市场设置](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*图 30 - 流分析作业中的数据市场设置*

## <a name="consume"></a>使用
Power BI 为此解决方案提供了丰富的仪表板，用于实时数据和预测分析可视化。 

单击此处获取有关设置 Power BI 报表和仪表板的详细说明。 最终仪表板类似于：

![Power BI 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

*图 31 - Power BI 仪表板*

## <a name="summary"></a>摘要
本文档包含对车辆遥测分析解决方案的详细深入分析。 这展示了一种 lambda 体系结构模式，用于实时和批处理分析，包括预测和操作。 此模式适用于需要热路径（实时）和冷路径（批处理）分析的各种的示例。 




<!--HONumber=Jan17_HO4-->


