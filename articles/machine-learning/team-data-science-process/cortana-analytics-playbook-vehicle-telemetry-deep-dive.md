---
title: "深入了解如何预测车辆的运行状况和驾驶习惯 - Azure | Microsoft 文档"
description: "通过 Cortana Intelligence 的功能获得对车辆运行状况和驾驶习惯的实时和预测性深入了解。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev
ms.openlocfilehash: a21316ef6ab05918f07a09243b5ce04950ecd9dc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>车辆遥测分析解决方案操作手册：深入探讨解决方案
这个菜单链接至此操作手册的部分： 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

本文将深入探究解决方案体系结构中所描述的各个阶段。 其中包含说明和用于自定义的指针。 

## <a name="data-sources"></a>数据源
该解决方案使用两个不同的数据源：

* 模拟车辆信号和诊断数据集
* 车辆目录

车辆远程信息处理模拟器是此解决方案的一部分，如以下屏幕截图所示。 它可在给定的时间点根据车辆的状态以及驾驶模式发出对应的诊断信息和信号。 若要下载“车辆远程信息处理模拟器 Visual Studio 解决方案”，以基于自身要求进行自定义，请转到[车辆远程信息处理模拟器](http://go.microsoft.com/fwlink/?LinkId=717075)网页。 车辆目录包含将车辆识别号 (VIN) 映射到模型的参考数据集。

![车辆远程信息处理模拟器](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


这是 JSON 格式的数据集，它包含以下架构。

| 列 | 说明 | 值 |
| --- | --- | --- |
| VIN |随机生成的 VIN |从 10,000 个随机生成的 VIN 主列表中获取 |
| 外部温度 |车辆行驶的外部温度 |随机生成的介于 0 - 100 的数字 |
| 引擎温度 |车辆的引擎温度 |随机生成的介于 0 - 500 的数字 |
| Speed |车辆正在行驶时的引擎转速 |随机生成的介于 0 - 100 的数字 |
| 燃料 |车辆的燃料油位 |随机生成的介于 0 - 100 的数字（指示燃油油位百分比） |
| EngineOil |车辆的引擎燃料油位 |随机生成的介于 0 - 100 的数字（指示机油油位百分比） |
| 轮胎压力 |车辆的轮胎压力 |随机生成的介于 0 - 50 的数字（指示轮胎压力级别百分比） |
| 里程表 |车辆的里程表读数 |随机生成的介于 0 - 200,000 的数字 |
| Accelerator_pedal_position |车辆的加速踏板位置 |随机生成的介于 0 - 100 的数字（指示加速器级别百分比） |
| Parking_brake_status |指示车辆是否停放 |是或否 |
| Headlamp_status |指示前大灯是否开启 |是或否 |
| Brake_pedal_status |指示是否踩下刹车踏板 |是或否 |
| Transmission_gear_position |车辆的变速齿轮档位 |状态：第一、第二、第三、第四、第五、第六、第七、第八档 |
| Ignition_status |指示车辆是正在驾驶中还是已停止 |是或否 |
| Windshield_wiper_status |指示是否开启挡风玻璃雨刮器 |是或否 |
| ABS |指示 ABS 是否已啮合 |是或否 |
| Timestamp |创建数据点时的时间戳 |日期 |
| 城市 |车辆的位置 |此解决方案中的四座城市：贝尔维尤、雷德蒙德、瑟马米什、西雅图 |

车辆模型参考数据集将 VIN 映射到模型。 

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

## <a name="ingestion"></a>引入
Azure 事件中心、Azure 流分析和 Azure 数据工厂的组合可用于获取车辆信号、诊断事件以及实时和批处理分析。 所有这些组件都将作为解决方案部署的一部分进行创建和配置。 

### <a name="real-time-analysis"></a>实时分析
车辆远程信息处理模拟器生成的事件将通过事件中心 SDK 发布到事件中心。  

![事件中心仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

流分析作业从事件中心引入这些事件，并实时处理数据以分析车辆运行状况。

![处理数据的流分析作业](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


流分析作业：

* 从事件中心引入数据。
* 使用参考数据执行一个连接，以将车辆 VIN 映射到相应的模型。 
* 将其保留在 Azure Blob 存储中以进行全面的批处理分析。 

使用下列流分析查询将数据保留到 Blob 存储中： 

![用于数据引入的流分析作业查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>批处理分析
此外，还将生成额外的模拟车辆信号卷和诊断数据集，以用于更全面的批处理分析。 此额外卷是确保用于批处理的良好代表数据卷所必需的。 为此，我们在数据工厂工作流中使用 PrepareSampleDataPipeline 来生成相当于一年的模拟车辆信号和诊断数据集。 若要下载“数据工厂自定义 .NET 活动 Visual Studio 解决方案”，以基于自身要求进行自定义，请转到[数据工厂自定义活动](http://go.microsoft.com/fwlink/?LinkId=717077)网页。 

此工作流显示准备进行批处理的示例数据。

![为批处理工作流准备的示例数据](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


管道由自定义数据工厂 .Net 活动组成。

![PrepareSampleDataPipeline 活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

在管道成功执行，并且 RawCarEventsTable 数据集被标记为“就绪”后，就会生成相当于一年的模拟车辆信号和诊断数据。 可在 connectedcar 容器下的存储帐户中查看以下文件夹和文件：

![PrepareSampleDataPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>数据集分区
在数据准备步骤中，原始半结构化的车辆信号和诊断数据集被分区为年/月格式。 此分区通过启用故障转移来提高查询和可缩放的长期存储的效率。 例如，当第一个 blob 帐户填满时，它会故障转移到下一个帐户。 

>[!NOTE] 
>解决方案中的此步骤仅适用于批处理。

输入和输出数据管理：

* 输出数据（标记为 PartitionedCarEventsTable）将在客户的“Data Lake”中以基础/“最原始”数据的形式保存很长一段时间。 
* 此管道中的输入数据通常会被弃用，因为输出数据对输入具有完全保真度。 它会更好地对数据进行存储（分区）以供后续使用。

对汽车事件工作流分区。

![对汽车事件工作流分区](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


原始数据使用 PartitionCarEventsPipeline 中的 Hive Azure HDInsight 活动进行分区，如以下屏幕截图所示。 按年/月对在数据准备步骤中生成的一年的示例数据进行分区。 使用分区来生成一年中每个月（总计 12 个分区）的车辆信号和诊断数据。 

![PartitionCarEventsPipeline 活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


PartitionConnectedCarEvents Hive 脚本

Hive 脚本 partitioncarevents.hql 用于分区。 它位于下载的 zip 文件的 \demo\src\connectedcar\scripts 文件夹中。 
    
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

管道成功执行后，会在“connectedcar”容器下的存储帐户中看到以下已生成的分区：

![已分区的输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

数据现已优化，更易于管理，并为进一步处理以获得全面的批处理见解做好了准备。 

## <a name="data-analysis"></a>数据分析
在本部分中，将了解如何结合流分析、Azure 机器学习、数据工厂和 HDInsight 来丰富关于车辆运行状况和驾驶习惯的高级分析。

### <a name="machine-learning"></a>机器学习
我们的目标是基于以下假设，根据特定运行状况统计数据来预测需维护或召回的车辆：

* 如果以下三个条件中其中之一为真，则车辆将需要维修保养：
  
  * 轮胎压力低。
  * 引擎机油油位低。
  * 引擎温度高。

* 如果以下其中一个条件为真，则车辆可能具有安全问题并需要召回：
  
  * 引擎温度高，但室外温度低。
  * 引擎温度低，但室外温度高。

根据之前的要求，两个单独的模型均检测到异常。 一个模型用于车辆维护检测，一个模型用于车辆召回检测。 在这两个模型中，使用内置主体组件分析 (PCA) 算法来检测异常。 

#### <a name="maintenance-detection-model"></a>**维护检测模型**

如果三个指标（轮胎压力、机油或引擎温度）中的一个满足其对应的条件，则维护检测模型将报告异常。 因此，只需要在构建模型时考虑这三个变量即可。 在机器学习试验中，首先使用“在数据集中选择列”模块来提取这三个变量。 接下来，使用基于 PCA 的异常情况检测模块来构建异常情况检测模型。 

PCA 是机器学习中一项成熟的技术，可应用于特征选择、分类和异常情况检测。 PCA 将一组包含可能相关变量的事例转换为一组被称为主体组件的值。 基于 PCA 建模的关键理念是将数据投影到较低维度的空间，以便更容易地标识特征和异常。

对于检测模型的每个新输入，异常探测器首先会计算其在特征向量上的投影。 然后再计算规范化的重构错误。 此规范化错误为异常分数：错误分数越高，实例越异常。 

在维护检测问题中，每个记录都被认为是由轮胎压力、机油和引擎温度坐标定义的三维空间中的点。 若要捕获这些异常情况，可以使用 PCA 将三维空间中的原始数据投影到二维空间上。 因此，我们将 PCA 中要使用的组件参数编号设置为 2。 在应用基于 PCA 的异常情况检测中，此参数扮演着重要的角色。 使用 PCA 投影数据后，可以更加轻松地标识这些异常。

#### <a name="recall-anomaly-detection-model"></a>召回异常情况检测模型

在召回异常情况检测模型中，我们以类似的方式使用“选择数据集中的列”和基于 PCA 的异常情况检测模块。 具体而言，首先使用“选择数据集中的列”模块提取三个变量 - 引擎温度、外部温度和转速。 转度变量也包括在内，因为引擎温度通常与转速有关。 接下来，使用基于 PCA 的异常情况检测模块，将来自三维空间的数据投影到二维空间上。 满足召回条件。 当引擎温度和外部温度高度负相关时，车辆需要召回。 执行 PCA 后，可以使用基于 PCA 的异常情况检测算法来捕获异常情况。 

在对模型进行训练时，正常数据作为输入数据用于训练基于 PCA 的异常情况检测模型。 （正常数据不需要维护或召回。）在评分实验中，我们使用经过训练的异常情况检测模型来检测车辆是否需要维护或召回。 

### <a name="real-time-analysis"></a>实时分析
下面的流分析 SQL 查询用于获取所有重要车辆参数的平均值。 这些参数包括车速、燃油油位、引擎温度、里程表读数、胎压、机油油位等其他参数。 这些平均值可用于检测异常情况、发出警报并确定在特定区域运转的车辆的整体运行状况。 然后将平均值与人口统计数据相关联。 

![用于实时处理的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

所有平均值都是通过三秒钟翻转窗口计算而得。 之所以使用翻转窗口是因为其非重叠性，且需要连续的时间间隔。 

要了解有关流分析中“窗口化”功能的详细信息，请参阅[窗口化（Azure 流分析）](https://msdn.microsoft.com/library/azure/dn835019.aspx)。

#### <a name="real-time-prediction"></a>**实时预测**

一个应用程序作为解决方案的一部分实时地操作机器学习。 此 RealTimeDashboardApp 应用程序作为解决方案部署的一部分进行创建和配置。 该应用程序：

* 监听事件中心实例，其中流分析会持续发布模式中的事件。

    ![用于发布数据的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* 接收事件。 对于此应用程序收到的每个事件： 
   
   * 使用机器学习请求-响应评分 (RRS) 终结点来处理数据。 作为部署的一部分，RRS终结点自动被发布。
   * 使用推送 API 将 RRS 输出发布到 Power BI 数据集。

另外，此模式也适用于希望将业务线应用程序与实时分析流集成的场景。 这些场景包括警报、通知和消息传递。

若要下载用于自定义的 RealtimeDashboardApp Visual Studio 解决方案，请参阅 [RealtimeDashboardApp download](http://go.microsoft.com/fwlink/?LinkId=717078) 网页。 

#### <a name="execute-the-real-time-dashboard-application"></a>执行实时仪表板应用程序
1. 提取 RealtimeDashboardApp，并将它保存到本地。

    ![RealTimeDashboardApp 文件夹](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. 执行应用程序 RealtimeDashboardApp.exe。

3. 输入有效的 Power BI 凭据，并选择“登录”。  

    ![实时仪表板应用登录窗口](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. 选择“接受”。

    ![实时仪表板应用最终登录窗口](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>如果要刷新 Power BI 数据集，请使用“flushdata”参数执行 RealtimeDashboardApp。 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>批处理分析
我们的目标是演示 Contoso Motors 如何使用 Azure 计算功能，以充分利用大数据。 此数据揭示了关于驾驶模式、使用行为和车辆运行状况的丰富见解。 此信息可使你：

* 通过提供驾驶习惯和节油驾驶行为的相关见解，改进客户体验并降低费用。
* 积极主动地了解客户及其驾驶模式，从而管理业务决策，并提供同类最佳产品和服务。

在此解决方案中，下列各项指标将针对：

* 攻击性驾驶行为：标识模型、位置、驾驶条件和一年时间中的趋势，以获得对攻击性驾驶模式的见解。 Contoso Motors 可以将这些见解用于市场营销活动，从而引入新的个性化功能和基于使用情况的保险。
* 节油驾驶行为：标识模型、位置、驾驶条件和一年时间中的趋势，以获得对节油驾驶模式的见解。 Contoso Motors 可将这些见解用于市场营销活动，从而引入新功能并主动向驾驶员报告，以形成经济高效和环保的驾驶习惯。
* 召回模型：通过操作异常情况检测机器学习实验来标识需要召回的模型。

让我们来看看每个指标的详细信息。

#### <a name="aggressive-driving-behavior-patterns"></a>**攻击性驾驶行为模式**

在 AggresiveDrivingPatternPipeline 中处理分区的车辆信号和诊断数据，如下面的工作流所示。 用 Hive 来确定显示攻击性驾驶模式的模型、位置、车辆、驾驶条件和其他参数。

![攻击性驾驶模式工作流](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***过激驾驶模式 Hive 查询***

Hive 脚本 aggresivedriving.hql 用于分析攻击性驾驶条件模式。 它位于下载的 zip 文件的 \demo\src\connectedcar\scripts 文件夹中。 

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


该脚本使用车辆的变速齿轮档位、制动踏板状态和转速的组合，基于高速制动模式来检测鲁莽/攻击性驾驶行为。 

管道成功执行后，会在“connectedcar”容器下的存储帐户中看到以下已生成的分区：

![AggressiveDrivingPatternPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**节油驾驶行为模式**

在 FuelEfficientDrivingPatternPipeline 中处理分区的车辆信号和诊断数据，如下面的工作流所示。 用 Hive 来确定显示节油驾驶模式的模型、位置、车辆、驾驶条件和其他属性。

![节油驾驶模式](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***节油驾驶模式 Hive 查询***

Hive 脚本 fuelefficientdriving.hql 用于分析节油驾驶条件模式。 它位于下载的 zip 文件的 \demo\src\connectedcar\scripts 文件夹中。 

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


该脚本通过车辆的变速齿轮档位、制动踏板状态，转速和加速器踏板位置的组合，基于加速、刹车和转速模式来检测节油驾驶行为。 

管道成功执行后，会在“connectedcar”容器下的存储帐户中看到以下已生成的分区：

![FuelEfficientDrivingPatternPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**召回模型预测**

作为解决方案部署的一部分，机器学习实验作为 Web 服务来预配和发布。 此工作流中使用批处理评分终结点。 它注册为数据工厂链接服务，并通过数据工厂批处理计分活动进行操作。

![机器学习终结点](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

在 DetectAnomalyPipeline 中使用已注册的链接服务，通过异常情况检测模型对数据进行评分。 

![数据工厂中的机器学习批处理评分活动](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

对于数据准备，在此管道中要执行几个步骤，以便可以通过批处理评分 Web 服务对其进行操作。 

![用于召回预测的 DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***异常情况检测 Hive 查询***

评分完成后，HDInsight 活动将处理并聚合被模型归类为异常的数据。 模型将使用 0.60 或更高的概率评分。

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


管道成功执行后，会在“connectedcar”容器下的存储帐户中看到以下已生成的分区：

![DetectAnomalyPipeline 输出](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>发布

### <a name="real-time-analysis"></a>实时分析
流分析作业中的其中一个查询将事件发布到输出事件中心实例。 

![流分析作业发布到输出事件中心实例](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

以下流分析查询用于发布到输出事件中心实例：

![发布到输出事件中心实例的流分析查询](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

此事件流由解决方案中包含的 RealTimeDashboardApp 使用。 此应用程序使用机器学习请求-响应 Web 服务进行实时评分。 它将生成的数据发布到 Power BI 数据集以供使用。 

### <a name="batch-analysis"></a>批处理分析
批处理和实时处理的结果将发布到 Azure SQL 数据库表以供使用。 SQL Server、数据库和表作为设置脚本的一部分自动创建。 

批处理结果会被复制到数据市场工作流。

![复制到数据市场工作流的批处理结果](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

流分析作业会被发布到数据市场。

![发布到数据市场的流分析作业](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

数据市场设置位于流分析作业中。

![流分析作业中的数据市场设置](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>使用
Power BI 为此解决方案提供了丰富的仪表板，用于实时数据和预测分析可视化。 

最终仪表板如以下示例所示：

![Power BI 仪表板](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>摘要
本文档包含对车辆遥测分析解决方案的详细深入分析。 lambda 体系结构模式用于实时和批处理分析，包括预测和操作。 此模式适用于需要热路径（实时）和冷路径（批处理）分析的各种的示例。 

### <a name="references"></a>参考

* [Vehicle Telematics Simulator Visual Studio Solution](http://go.microsoft.com/fwlink/?LinkId=717075)（车辆远程信息处理模拟器 Visual Studio 解决方案） 
* [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)
* [Azure 数据工厂](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [用于流引入的 Azure 事件中心 SDK](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Azure 数据工厂数据移动功能](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Azure 数据工厂 .NET 活动](../../data-factory/v1/data-factory-use-custom-activities.md)
* [用于准备示例数据的 Azure 数据工厂 .NET 活动 Visual Studio 解决方案](http://go.microsoft.com/fwlink/?LinkId=717077) 
