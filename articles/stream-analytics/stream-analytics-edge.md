---
title: "IoT Edge 上的 Azure 流分析（预览版）"
description: "在 Azure 流分析中创建 Edge 作业，并将其部署到运行 Azure IoT Edge 的设备。"
keywords: "数据流, iot, edge"
services: stream-analytics
documentationcenter: 
author: jseb225
manager: jhubbard
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/16/2017
ms.author: jeanb
ms.openlocfilehash: 916aefb7916ab374c882efb95417babfc5b06a50
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
---
# <a name="azure-stream-analytics-on-iot-edge-preview"></a>IoT Edge 上的 Azure 流分析（预览版）

> [!IMPORTANT]
> 此功能处于预览状态。 不建议在生产中使用。
 
IoT Edge 上的 Azure 流分析 (ASA) 可让开发人员将近乎实时的分析智能更近地部署到 IoT 设备，以便他们能够使设备生成的数据发挥出全部价值。 专为低延迟、复原能力、高效使用带宽和合规性而设计，企业现在可以将控制逻辑部署到接近工业运营的位置，并补充在云中完成的大数据分析。  
IoT Edge 上的 Azure 流分析在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 框架中运行。 在 ASA 中创建作业后，便可使用 IoT 中心部署和管理 ASA 作业。
此功能当前处于预览版状态，如果你有任何问题或反馈，请使用[此调查](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u)与产品团队联系。 

## <a name="scenarios"></a>方案
![概要关系图](media/stream-analytics-edge/ASAedge_highlevel.png)

* **低延迟命令和控制**：例如，生产安全系统时必须以超低的延迟响应运行数据。 借助 IoT Edge 上的 ASA，可以近乎实时地分析传感器数据，并在检测到异常情况时发出命令，从而停止计算机或触发警报。
*   到云的受限连接：关键任务系统（如远程采矿设备、连接的船舶或海上钻井）需要分析数据并对数据做出反应，即使云连接是间歇性的也是如此。 使用 ASA，流式处理逻辑可独立于网络连接运行，你可以选择发送到云以作进一步处理或存储的内容。
* 有限的带宽：由喷气引擎或车联网生成的数据量可能非常大，因此，在将数据发送到云之前必须对数据进行筛选或预处理。 使用 ASA，你可以筛选或聚合需要发送到云的数据。
* 符合性：监管符合性可能需要在将一些数据发送到云之前对其进行本地匿名或聚合处理。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 流分析作业中的 Edge 作业
### <a name="what-is-an-edge-job"></a>什么是“Edge”作业？

ASA Edge 作业在 [Azure IoT Edge 运行时](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)中作为模块运行。 它们由两个部分组成：
1.  云部分负责作业定义：用户在云中定义输入、输出、查询和其他设置（无序事件等）。
2.  本地运行的 IoT Edge 模块上的 ASA。 它包含 ASA 复杂事件处理引擎，并从云接收作业定义。 

ASA 使用 IoT 中心将 Edge 作业部署到设备。 [可在此处查看有关 IoT Edge 部署](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)的详细信息。

![Edge 作业](media/stream-analytics-edge/ASAedge_job.png)


### <a name="installation-instructions"></a>安装说明
下表描述了高级步骤： 下面的部分将进行详细说明。
|      |步骤   | 位置     | 说明   |
| ---   | ---   | ---       |  ---      |
| 1   | 创建 ASA 边缘作业   | Azure 门户      |  创建新的作业，选择“Edge”作为“宿主环境”。 <br> 这些作业从云创建/管理，并在你自己的 IoT Edge 设备上运行。     |
| #N/A   | 创建存储容器   | Azure 门户       | 存储容器用于保存作业定义，IoT 设备可在其中进行访问它们。 <br>  你可以重用任何现有的存储容器。     |
| 3   | 在设备上设置 IoT Edge 环境   | 设备      | [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 说明。          |
| 4   | 在 IoT Edge 设备上部署 ASA   | Azure 门户      |  ASA 作业定义被导出到之前创建的存储容器。       |
你可以按照[本分步教程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)在 IoT Edge 上部署你的第一个 ASA 作业。

#### <a name="create-an-asa-edge-job"></a>创建 ASA Edge 作业
1. 在 Azure 门户创建一个新的“流分析作业”。 [在此处创建新的 ASA 作业的直接链接](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)。

> [!Note]
> 可以在 ASA 支持的所有区域（**“美国西部 2”区域除外**）中创建 Edge 作业。
> 此限制很快将会取消。

2. 在“创建”屏幕中，选择“Edge”作为“宿主环境”（请参阅下图）![作业创建](media/stream-analytics-edge/ASAEdge_create.png)
3. 作业定义
    1. 定义输入流。 为作业定义一个或多个输入流。
    2. 定义参考数据（可选）。
    3. 定义输出流。 为作业定义一个或多个输出流。 
    4. 定义查询。 在云中使用内联编辑器定义 ASA 查询。 编译器将为 ASA Edge 自动启用语法检查。 此外，还可以通过上传示例数据来测试你的查询。 
4. 设置可选设置
    1. 事件排序。 你可以在门户中配置无序策略。 [此处](https://msdn.microsoft.com/library/azure/mt674682.aspx?f=255&MSPPError=-2147217396)可获取文档。
    2. 区域设置。 设置内部化格式。


#### <a name="create-a-storage-container"></a>创建存储容器
需要存储容器才能导出 ASA 已编译查询和作业配置。 它用于使用特定查询来配置 ASA Docker 映像。 
1. 请按照[这些说明](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)在 Azure 门户创建存储帐户。 你可以保留所有默认选项以在 ASA 中使用此帐户。
2. 在新创建的存储帐户中，创建一个 blob 存储容器：
    1. 依次单击“Blob”和“+ 容器”。 
    2. 输入名称，并将容器保留为“专用”。


> [!Note]
> 创建部署时，ASA 将作业定义导出到存储容器。 此作业定义在部署期间保持不变。 因此，如果要更新在 Edge 上运行的作业，则需要在 ASA 中编辑作业，然后在 IoT 中心创建新的部署。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>在设备上设置 IoT Edge 环境
边缘作业可以部署在运行 Azure IoT Edge 的设备上。
为此，需要执行以下步骤：
- 创建 IoT 中心；
- 在 Edge 设备上安装 Docker 和 IoT Edge 运行时；
- 将设备在 IoT 中心设置为“IoT Edge 设备”。

这些步骤在针对 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的 IoT Edge 文档中有相关描述。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>在 IoT Edge 设备上部署 ASA
##### <a name="add-asa-to-your-deployment"></a>将 ASA 添加到部署
- 在 Azure 门户中，打开“IoT 中心”，导航到“IoT Edge Explorer”，然后打开设备边栏选项卡。
- 选择“设置模块”，然后选择“导入 Azure Service IoT Edge 模块”。
- 选择订阅和你创建的 ASA Edge 作业。 然后选择存储帐户。 单击“保存”。
![在部署中添加 ASA 模块](media/stream-analytics-edge/set_module.png)


> [!Note]
> 在此步骤中，ASA 请求访问选定的存储容器，然后创建名为“EdgeJobs”的文件夹。 对于每项部署，“EdgeJobs”文件夹中都将创建一个新的子文件夹。
> 为了将你的作业部署到 Edge 设备，ASA 会为作业定义文件创建一个共享访问签名 (SAS)。 SAS 密钥使用设备孪生安全地传输到 IoT Edge 设备。 此密钥将在其创建之日后的三年过期。


有关 IoT Edge 部署的详细信息，请参阅[本页](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。


##### <a name="configure-routes"></a>配置路由
IoT Edge 提供了一种在模块之间，以及模块和 IoT 中心之间以声明方式路由消息的方法。 [此处](https://docs.microsoft.com/azure/iot-edge/module-composition)描述了完整的语法。
在 ASA 作业中创建的输入和输出的名称可以用作路由的终结点。  

###### <a name="example"></a>示例
```
{
"routes": {                                              
    "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
    "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream", 
    "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")" 
}
}   

```
此示例演示在以下图片中所述的方案的路由。 它包含名为“ASA”的边缘作业，以及名为“temperature”的输入和和名为“alert”的输出。
![示例路由](media/stream-analytics-edge/RoutingExample.png)

以下示例定义了以下路由：
- 来自 tempSensor 的每个消息均发送到名为 ASA 的模块，再到名为“temperature”的输入，
- ASA 模块的所有输出均发送到链接此设备的 IoT 中心 ($upstream)，
- ASA 模块的所有输出均发送到 tempSensor 的控制终结点。


## <a name="technical-information"></a>技术信息
### <a name="current-limitations-for-edge-jobs-compared-to-cloud-jobs"></a>与云作业相比，Edge 作业的当前限制
目标是在 Edge 作业和云作业之间进行平衡。 已支持 SQL 查询语言的大部分功能。
但是，以下功能尚不支持 Edge 作业：
* 用户定义的函数 (UDF) 和用户定义的聚合 (UDA)。
* Azure ML 函数。
* 在单个步骤中使用超过 14 个聚合。
* 用于输入/输出的 AVRO 格式。 目前支持仅 CSV 和 JSON。
* JSON 输入的压缩。
* 以下 SQL 运算符：
    * AnomalyDetection
    * 地理空间运算符：
        * CreatePoint
        * CreatePolygon
        * CreateLineString
        * ST_DISTANCE
        * ST_WITHIN
        * ST_OVERLAPS
        * ST_INTERSECTS
    * 分区依据
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>运行时和硬件要求
若要在 IoT Edge 上运行 ASA，你需要可以运行 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 的设备。 

ASA 和 Azure IoT Edge 使用 Docker 容器来提供可在多个主机操作系统（Windows、Linux）上运行的便携式解决方案。

IoT Edge 上的 ASA 可用作 Windows 和 Linux 映像运行于 x86-64 或 ARM 架构之上。 


### <a name="input-and-output"></a>输入和输出
#### <a name="input-and-output-streams"></a>输入和输出流
ASA Edge 作业可以从在 IoT Edge 设备上运行的其他模块获取输入和输出。 要与特定模块实现相互连接，你可以在部署时设置路由配置。 有关详细信息，请参阅 [IoT Edge 模块组成文档](https://docs.microsoft.com/azure/iot-edge/module-composition)。

输入和输出均支持 CSV 和 JSON 格式。

对于在 ASA 作业中创建的每个输入和输出流，都将在部署的模块上创建相应的终结点。 这些终结点可以用于部署的路由。



##### <a name="reference-data"></a>引用数据
参考数据（也称为查找表）是一个静态的或本质上缓慢变化的有限数据集。 可用于执行查找或与数据流相关联。 为了在 Azure 流分析作业中利用引用数据，通常会在查询中使用[引用数据联合](https://msdn.microsoft.com/library/azure/dn949258.aspx)。 有关详细信息，请参阅[关于参考数据的 ASA 文档](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data)。

为了在 Iot Edge 上使用 ASA 的参考数据，你需要执行以下步骤： 
1. 为作业创建一个新输入
2. 选择“参考数据”作为”源类型“。
3. 设置文件路径。 文件路径应为在![参考数据创建](media/stream-analytics-edge/ReferenceData.png)设备上的绝对文件路径
4. 在 Docker 配置中启用“共享驱动器”，并确保在开始部署之前已启用驱动器。

有关详细信息，请参阅此处 [Windows 适用的 Docker 文档](https://docs.docker.com/docker-for-windows/#shared-drives)。

> [!Note]
> 目前仅支持本地参考数据。




## <a name="license-and-third-party-notices"></a>许可证和第三方通知
* [IoT Edge 预览版许可证上的 Azure 流分析](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [IoT Edge 预览版上 Azure 流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试参考 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>后续步骤

* [Azure IoT Edge 的详细信息](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [IoT Edge 教程上的 ASA ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用此调查向团队发送反馈](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
