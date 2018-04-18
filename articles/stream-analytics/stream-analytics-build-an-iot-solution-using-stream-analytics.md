---
title: 使用 Azure 流分析生成 IoT 解决方案
description: 使用收费站方案了解流分析 IoT 解决方案的入门教程
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>使用流分析生成 IoT 解决方案

## <a name="introduction"></a>介绍
本解决方案演示如何使用 Azure 流分析从数据获得实时见解。 开发人员可以轻松将数据流（例如点击流、日志和设备生成的时间）与历史记录或引用数据结合起来，获取业务信息。 由 Microsoft Azure 托管的 Azure 流分析是可完全管理的实时流计算服务，它提供内置冗余、低延迟及伸缩性，可让用户在几分钟之内就立刻上手。

完成后此解决方案，你将可以：

* 熟悉 Azure 流分析门户。
* 配置和部署流式处理作业。
* 使用流分析查询语言来表达真实世界的问题并解决这些问题。
* 自信地使用流分析为客户开发流解决方案。
* 使用监视和日志记录体验来排解问题。

## <a name="prerequisites"></a>先决条件
若要完成本解决方案，需要满足以下先决条件：
* [Azure 订阅](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>方案简介：“你好，收费站！”
收费站是常见景象。 在世界各地的高速公路、桥梁和隧道旁边都会看到它们。 每个收费站有多个收费亭。 在手动收费亭前，要停下来向办事员支付通行费。 在自动收费亭前，穿过收费亭时，每个收费亭上的传感器将扫描安装在汽车挡风玻璃上的 RFID 卡。 我们可以轻松地将车辆通过这些收费站的情况想象成能够执行许多有趣操作的事件流。

![收费亭前汽车的图片](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>传入的数据
本解决方案使用两个数据流。 安装在收费站入口和出口的传感器产生第一个流。 第二个流是包含汽车注册数据的静态查找数据集。

### <a name="entry-data-stream"></a>入口数据流
入口数据流包含汽车进入收费站时的相关信息。 出口数据事件从示例应用中包含的 Web 应用实时流式传输到事件中心队列。

| TollID | EntryTime | LicensePlate | State | 制造商 | 模型 | VehicleType | VehicleWeight | 收费站 | 标记 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

下面是每个列的简短说明：

| 列 | 说明 |
| --- | --- |
| TollID |唯一标识收费亭的收费亭 ID |
| EntryTime |汽车进入收费亭的日期和时间（世界协调时） |
| LicensePlate |汽车的牌照号码 |
| State |美国的某个州 |
| 制造商 |汽车制造商 |
| 模型 |汽车型号 |
| VehicleType |1（客车）或 2（商用车） |
| WeightType |汽车的重量，单位为吨；0 代表客车 |
| 收费站 |通行费，单位为美元 |
| 标记 |汽车上可用于自动付费的 e-Tag，空白代表手动付款 |

### <a name="exit-data-stream"></a>出口数据流
出口数据流包含汽车离开收费站的相关信息。 出口数据事件从示例应用中包含的 Web 应用实时流式传输到事件中心队列。

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

下面是每个列的简短说明：

| 列 | 说明 |
| --- | --- |
| TollID |唯一标识收费亭的收费亭 ID |
| ExitTime |汽车离开收费亭的日期和时间（世界协调时） |
| LicensePlate |汽车的牌照号码 |

### <a name="commercial-vehicle-registration-data"></a>商用车注册数据
本解决方案使用商用车注册数据库的静态快照。 此数据作为 JSON 文件保存到示例随附的 Azure Blob 存储中。

| LicensePlate | RegistrationId | Expired |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

下面是每个列的简短说明：

| 列 | 说明 |
| --- | --- |
| LicensePlate |汽车的牌照号码 |
| RegistrationId |汽车的注册 ID |
| 已过期 |汽车的注册状态：0 代表汽车注册仍有效，1 代表汽车注册已过期 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>设置 Azure 流分析的环境
若要完成本解决方案，需要一个 Microsoft Azure 订阅。 如果没有 Azure 帐户，可以[请求免费试用版](http://azure.microsoft.com/pricing/free-trial/)。

请务必按照本文末尾的“清理 Azure 帐户”部分中的步骤操作，以便充分利用 Azure 信用额度。

## <a name="deploy-the-sample"></a>部署示例 
只需单击几下鼠标，就能轻松将多个资源一起部署在某个资源组中。 解决方案定义托管在 [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) 上的 github 存储库中。

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>在 Azure 门户中部署 TollApp 模板
1. 若要将 TollApp 环境部署到 Azure，请使用此链接[部署 TollApp Azure 模板](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)。

2. 根据提示登录到 Azure 门户。

3. 选择要对其中各种资源计费的订阅。

4. 指定具有唯一名称的新资源组，例如 `MyTollBooth`。 

5. 选择 Azure 位置。

6. 在“间隔”中指定若干秒。 此值将在示例 Web 应用中使用，指定将数据发送到事件中心的频率。 

7. **选中**表示同意条款和条件的复选框。

8. 选择“固定到仪表板”，以便稍后可以轻松找到资源。

9. 选择“购买”以部署示例模板。

10. 片刻之后，将会显示一条通知来确认**部署成功**。

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>查看 Azure 流分析 TollApp 资源
1. 登录到 Azure 门户

2. 找到在上一部分命名的资源组。

3. 检查该资源组中是否列出了以下资源：
   - 一个 Cosmos DB 帐户
   - 一个 Azure 流分析作业
   - 一个 Azure 存储帐户
   - 一个 Azure 事件中心
   - 两个 Web 应用

## <a name="examine-the-sample-tollapp-job"></a>检查示例 TollApp 作业 
1. 从上一部分创建的资源组着手，选择以名称 **tollapp** 开头的流分析流式处理作业（名称包含随机字符，以确保唯一性）。

2. 在该作业的“概述”页上，观察“查询”框以查看查询语法。

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   为了解释查询的意图，我们假设需要统计进入某个收费亭的汽车数目。 由于进入高速公路收费亭的车流是连续性的，这些入口事件类似于永不停止的流。 若要量化流，必须定义要不断度量的“时间段”。 我们进一步将问题具体化为“每三分钟有多少汽车进入收费亭？” 这通常称为轮转计数。

   如你所见，Azure 流分析使用类似 SQL 的查询语言，并且添加了一些扩展以指定查询与时间相关的方面。  有关详细信息，请参阅[时间管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)和查询中所用的[开窗](https://msdn.microsoft.com/library/azure/dn835019.aspx)构造。

3. 检查 TollApp 示例作业的输入。 当前查询中仅使用了 EntryStream 输入。
   - **EntryStream** 输入是一个事件中心连接，它将代表每次汽车进入高速公路收费亭的事件数据排队。 示例中包含的 Web 应用将会创建事件，而这些数据将在此事件中心排队。 请注意，此输入在流式处理查询的 FROM 子句中查询。
   - **ExitStream** 输入是一个事件中心连接，它将代表每次汽车离开高速公路收费亭的事件数据排队。 此流输入在稍后的查询语法变体中使用。
   - **Registration** 输入是一个 Azure Blob 存储连接，指向按需用于查找的静态 registration.json 文件。 此参考数据输入在稍后的查询语法变体中使用。

4. 检查 TollApp 示例作业的输出。
   - **Cosmos DB** 输出是接收输出接收器事件的 Cosmos 数据库集合。 请注意，此输出在流式处理查询的 INTO 子句中使用。

## <a name="start-the-tollapp-streaming-job"></a>启动 TollApp 流式处理作业
遵循以下步骤启动流式处理作业：

1. 在作业的“概述”页上选择“启动”。

2. 在“启动作业”窗格中选择“立即”。

3. 等待片刻，作业运行后，请在流式处理作业的“概述”页上查看“监视”图表。 此图应显示数千个输入事件和数十个输出事件。

## <a name="review-the-cosmosdb-output-data"></a>查看 CosmosDB 输出数据
1. 找到包含 TollApp 资源的资源组。

2. 选择名称模式为 **tollapp<random>-cosmos** 的 Azure Cosmos DB 帐户。

3. 选择“数据资源管理器”标题打开“数据资源管理器”页。

4. 展开“tollAppDatabase” > “tollAppCollection” > “文档”。

5. 提供输出后，ID 列表中会显示多个文档。

6. 选择每个 ID 以查看 JSON 文档。 请注意每个 tollid、windowend 时间和该时限内的汽车计数。

7. 再过三分钟后，将显示另外四个文档，每个 tollid 对应一个文档。 


## <a name="report-total-time-for-each-car"></a>报告每辆汽车的总时间
了解汽车通过收费站所需的平均时间有助于评估进度效率和客户体验。

要得出总时间，请将 EntryTime 流与 ExitTime 流相联接。 在等量匹配的 TollId 和 LicencePlate 列中联接两个输入流。 **JOIN** 运算符要求指定一个临时余量，以描述联接事件之间可接受的时间差。 使用 **DATEDIFF** 函数指定事件之间的时间差不能超过 15 分钟。 另外，将 **DATEDIFF** 函数应用到出口及入口时间，以计算汽车经过收费站的实际时间。 请注意相比 **JOIN** 条件，在 **SELECT** 语句中使用 **DATEDIFF** 的差异。

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>更新 TollApp 流式处理作业查询语法：

1. 在作业的“概述”页上选择“停止”。

2. 片刻之后，将出现作业已停止的通知。

3. 在“作业拓扑”标题下，选择“< > 查询”

4. 粘贴调整后的流式处理 SQL 查询。

5. 选择“保存”以保存查询。 选择“是”确认保存更改。

6. 在作业的“概述”页上选择“启动”。

7. 在“启动作业”窗格中选择“立即”。

### <a name="review-the-total-time-in-the-output"></a>查看输出中的总时间
重复前一部分所述的步骤，查看流式处理作业返回的 CosmosDB 输出数据。 查看最新的 JSON 文档。 

例如，以下文档显示了某辆示例汽车的特定牌照、entrytime 和 exittime，以及 DATEDIFF 计算的 durationinminutes 字段（显示该汽车在收费亭停留了两分钟）： 
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>报告注册已过期的汽车
Azure 流分析可以使用参考数据静态快照来与时态数据流联接。 为了演示此功能，请使用以下示例问题。 Registration 输入是一个静态 blob json 文件，其中列出牌照过期时间。 基于牌照执行联接，可将参考数据与通过收费亭的每辆汽车进行比较。 

如果某辆商用车已向收费公司注册，则可以直接通过收费亭，而不用停车接受检查。 使用注册查找表来识别注册已过期的所有商用车。

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. 重复前一部分所述的步骤，更新 TollApp 流式处理作业查询语法。

2. 重复前一部分所述的步骤，查看流式处理作业返回的 CosmosDB 输出数据。 

示例输出：
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>横向扩展作业
Azure 流分析可弹性缩放，因而能够处理大量数据。 Azure 流分析查询可以使用 **PARTITION BY** 子句来告诉系统此步骤会横向扩展。**PartitionId** 是系统加入以与输入（事件中心）的分区 ID 匹配的特殊列。

若要横向扩展对分区的查询，请将查询语法编辑为以下代码：
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

将流式处理作业纵向扩展为更多的流单元：

1. **停止**当前作业。 

2. 在“< > 查询”页中更新查询语法，然后保存更改。

3. 在流式处理作业的“配置”标题下，选择“缩放”。
   
4. 将“流单元”滑块从 1 滑到 6。 流单元定义作业能够接收的计算能力大小。 选择“保存”。

5. **启动**流式处理作业，以演示其他缩放操作。 Azure 流分析可在更多的计算资源之间分配工作，并可以使用 PARTITION BY 子句中指定的列将不同资源中的工作分区，从而提高吞吐量。 

## <a name="monitor-the-job"></a>监视作业
“监视器”区域包含正在运行的作业的相关统计信息。 需要完成首次配置，才能使用同一区域中的存储帐户（按本文档其余部分命名收费站）。   

![监视器屏幕截图](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

还可通过作业仪表板的“设置”区域访问“活动日志”。

## <a name="clean-up-the-tollapp-resources"></a>清理 TollApp 资源
1. 请在 Azure 门户中停止流分析作业。

2. 找到包含与 TollApp 模板相关的八个资源的资源组。

3. 选择“删除资源组”。 键入资源组名称以确认删除。

## <a name="conclusion"></a>结束语
本解决方案介绍了 Azure 流分析服务。 它演示了如何为流分析作业配置输入和输出。 本解决方案还使用收费站数据场景来解释在数据空间不断变化时所引发的常见问题类型，以及如何在 Azure 流分析中使用类似于 SQL 的简单查询来解决这些问题。 本解决方案介绍了用于处理时态数据的 SQL 扩展构造。 它演示了如何联接不同的数据流、如何使用静态引用数据来扩充数据流以及如何扩大查询以达成更高的吞吐量。

尽管本解决方案提供了详细介绍，但它不可能面面俱到。 可通过在[常用流分析使用模式的查询示例](stream-analytics-stream-analytics-query-patterns.md)中使用 SAQL 语言，发现更多查询模式。
