---
title: Azure IoT Edge 流分析
description: 在 Azure 流分析中创建 edge 作业，并将其部署到运行 Azure IoT Edge 的设备。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 02/14/2020
ms.custom: seodec18
ms.openlocfilehash: 7e4a1cf43931b56cfdb3c31ffe870a07dbaa75af
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201749"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Azure IoT Edge 流分析
 
IoT Edge 上的 Azure 流分析 (ASA) 可让开发人员将近乎实时的分析智能更近地部署到 IoT 设备，以便他们能够使设备生成的数据发挥出全部价值。 Azure 流分析专为实现低延迟、复原能力、有效使用带宽和合规性而设计。 企业现在可以将控制逻辑部署到接近工业运营的位置，并补充在云中完成的大数据分析。  

IoT Edge 上的 Azure 流分析在 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 框架中运行。 在 ASA 中创建作业后，便可使用 IoT 中心进行部署和管理。

## <a name="scenarios"></a>方案
![IoT Edge 的高级别关系图](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **低延迟命令和控制**：例如，生产安全系统时必须以超低的延迟响应运行数据。 借助 IoT Edge 上的 ASA，可以近乎实时地分析传感器数据，并在检测到异常情况时发出命令，从而停止计算机或触发警报。
*   **与云的受限连接**：任务关键型系统（如远程采矿设备、连接的船舶或海上钻井）需要分析数据并对数据做出反应，即使云连接是间歇性的也是如此。 使用 ASA，流式处理逻辑可独立于网络连接运行，你可以选择发送到云以作进一步处理或存储的内容。
* 有限的带宽：由喷气引擎或车联网生成的数据量可能非常大，因此，在将数据发送到云之前必须对数据进行筛选或预处理。 使用 ASA，可以筛选或聚合需要发送到云的数据。
* 符合性：监管符合性可能需要在将一些数据发送到云之前对其进行本地匿名或聚合处理。

## <a name="edge-jobs-in-azure-stream-analytics"></a>Azure 流分析作业中的 Edge 作业
### <a name="what-is-an-edge-job"></a>什么是“Edge”作业？

ASA Edge 作业在部署到 [Azure IoT Edge 设备](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)的容器中运行。 它们由两个部分组成：
1.  负责作业定义的云部分：用户在云中定义输入、输出、查询和其他设置（无序事件等）。
2.  在 IoT 设备上运行的模块。 它包含 ASA 引擎，并从云接收作业定义。 

ASA 使用 IoT 中心将 Edge 作业部署到设备。 [可在此处查看有关 IoT Edge 部署](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)的详细信息。

![Azure 流分析 Edge 作业](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>安装说明
下表描述了高级步骤： 下面的部分将进行详细说明。

|      |步骤   | 注意   |
| ---   | ---   |  ---      |
| 1   | 创建存储容器   | 存储容器用于保存作业定义，IoT 设备可在其中进行访问它们。 <br>  你可以重用任何现有的存储容器。     |
| 2   | 创建 ASA 边缘作业   |  创建新的作业，选择“Edge”作为“宿主环境”。 <br> 这些作业从云创建/管理，并在你自己的 IoT Edge 设备上运行。     |
| 3   | **在设备上设置 IoT Edge 环境**   | [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 说明。          |
| 4   | 在 IoT Edge 设备上部署 ASA   |  ASA 作业定义被导出到之前创建的存储容器。       |

你可以按照[本分步教程](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)在 IoT Edge 上部署你的第一个 ASA 作业。 下面的视频可帮助你了解在 IoT Edge 设备上运行流分析作业的过程：  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>创建存储容器
需要存储容器才能导出 ASA 已编译查询和作业配置。 它用于使用特定查询来配置 ASA Docker 映像。 
1. 请按照[这些说明](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)在 Azure 门户创建存储帐户。 你可以保留所有默认选项以在 ASA 中使用此帐户。
2. 在新创建的存储帐户中，创建一个 blob 存储容器：
    1. 依次单击“Blob”和“+ 容器”。 
    2. 输入名称，并将容器保留为“专用”。

#### <a name="create-an-asa-edge-job"></a>创建 ASA Edge 作业
> [!Note]
> 本教程重点介绍如何使用 Azure 门户创建 ASA 作业。 也可以[使用 Visual Studio 插件创建 ASA Edge 作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. 在 Azure 门户创建一个新的“流分析作业”。 [在此处创建新的 ASA 作业的直接链接](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob)。

2. 在“创建”屏幕中，选择“Edge”作为“宿主环境”（请参阅下图）

   ![在 Edge 上创建流分析作业](media/stream-analytics-edge/create-asa-edge-job.png)
3. 作业定义
    1. 定义输入流。 为作业定义一个或多个输入流。
    2. 定义参考数据（可选）。
    3. 定义输出流。 为作业定义一个或多个输出流。 
    4. 定义查询。 在云中使用内联编辑器定义 ASA 查询。 编译器将为 ASA Edge 自动启用语法检查。 此外，还可以通过上传示例数据来测试你的查询。 

4. 在“IoT Edge 设置”菜单中设置存储容器信息。

5. 设置可选设置
    1. 事件排序。 你可以在门户中配置无序策略。 [此处](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics)可获取文档。
    2. 区域设置。 设置内部化格式。



> [!Note]
> 创建部署时，ASA 将作业定义导出到存储容器。 此作业定义在部署期间保持不变。 因此，如果要更新在 Edge 上运行的作业，则需要在 ASA 中编辑作业，然后在 IoT 中心创建新的部署。


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>在设备上设置 IoT Edge 环境
边缘作业可以部署在运行 Azure IoT Edge 的设备上。
为此，需要执行以下步骤：
- 创建 IoT 中心。
- 在 Edge 设备上安装 Docker 和 IoT Edge 运行时。
- 在 IoT 中心将设备设置为“IoT Edge 设备”。

这些步骤在针对 [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) 的 IoT Edge 文档中有相关描述。  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>在 IoT Edge 设备上部署 ASA
##### <a name="add-asa-to-your-deployment"></a>将 ASA 添加到部署
- 在 Azure 门户中，打开 IoT 中心，导航到 **IoT Edge**，并单击要用于此部署的设备。
- 选择“设置模块”，然后依次选择“+ 添加”、“Azure 流分析模块”。
- 选择订阅和你创建的 ASA Edge 作业。 单击“保存”。
![在部署中添加 ASA 模块](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> 在此步骤中，ASA 会在存储容器中创建一个名为“EdgeJobs”的文件夹（如果该文件夹尚不存在）。 对于每项部署，“EdgeJobs”文件夹中都将创建一个新的子文件夹。
> 将作业部署到 IoT Edge 设备后，ASA 将为作业定义文件创建共享访问签名（SAS）。 SAS 密钥使用设备孪生安全地传输到 IoT Edge 设备。 此密钥将在其创建之日后的三年过期。 更新 IoT Edge 作业时，将更改 SAS，但映像版本将不会更改。 **更新**后，请遵循部署工作流，并在设备上记录更新通知。


有关 IoT Edge 部署的详细信息，请参阅[此页](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)。


##### <a name="configure-routes"></a>配置路由
IoT Edge 提供了一种在模块之间，以及模块和 IoT 中心之间以声明方式路由消息的方法。 [此处](https://docs.microsoft.com/azure/iot-edge/module-composition)描述了完整的语法。
在 ASA 作业中创建的输入和输出的名称可以用作路由的终结点。  

###### <a name="example"></a>示例

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
此示例演示下图中所述的方案的路由。 它包含名为“ASA”的边缘作业，以及名为“temperature”的输入和和名为“alert”的输出。
![消息路由关系图示例](media/stream-analytics-edge/edge-message-routing-example.png)

以下示例定义了以下路由：
- 来自 tempSensor 的每个消息均发送到名为 ASA 的模块，再到名为“temperature”的输入，
- ASA模块的所有输出均发送到链接此设备的 IoT 中心 ($upstream)，
- ASA模块的所有输出均发送到 tempSensor的控制终结点。


## <a name="technical-information"></a>技术信息
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>与云作业相比，IoT Edge 作业当前受限制
目标是在 IoT Edge 作业和云作业之间进行平衡。 支持大多数 SQL 查询语言功能，从而能够在云和 IoT Edge 上运行相同的逻辑。
但是，以下功能尚不支持 Edge 作业：
* JavaScript 中的用户定义函数 (UDF)。 [用于 IoT Edge 作业的 C#](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf)（预览版）中提供了 UDF。
* 用户定义聚合 (UDA)。
* Azure ML 函数。
* 在单个步骤中使用超过 14 个聚合。
* 用于输入/输出的 AVRO 格式。 目前仅支持 CSV 和 JSON。
* 以下 SQL 运算符：
    * PARTITION BY
    * GetMetadataPropertyValue


### <a name="runtime-and-hardware-requirements"></a>运行时和硬件要求
若要在 IoT Edge 上运行 ASA，需要可以运行 [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/) 的设备。 

ASA 和 Azure IoT Edge 使用 Docker容器来提供可在多个主机操作系统（Windows、Linux）上运行的便携式解决方案。

IoT Edge 上的 ASA 可用作 Windows 和 Linux 映像运行于 x86-64 或 ARM（高级 RISC 计算机）架构之上。 


### <a name="input-and-output"></a>输入和输出
#### <a name="input-and-output-streams"></a>输入和输出流
ASA Edge 作业可以从在 IoT Edge 设备上运行的其他模块获取输入和输出。 要与特定模块实现相互连接，你可以在部署时设置路由配置。 有关详细信息，请参阅 [IoT Edge 模块组成文档](https://docs.microsoft.com/azure/iot-edge/module-composition)。

输入和输出均支持 CSV 和 JSON 格式。

对于在 ASA 作业中创建的每个输入和输出流，都将在部署的模块上创建相应的终结点。 这些终结点可以用于部署的路由。

目前，唯一支持的流输入和流输出类型是“Edge 中心”。 参考输入支持参考文件类型。 可以使用下游的云作业访问其他输出。 例如，在 Edge 中托管的流分析作业会将输出发送到 Edge 中心，然后后者可以将输出发送到 IoT 中心。 可以使用第二个云托管的 Azure 流分析作业，该作业的输入来自 IoT 中心，并输出到 Power BI 或其他输出类型。



##### <a name="reference-data"></a>引用数据
参考数据（也称为查找表）是一个静态的或本质上缓慢变化的有限数据集。 可用于执行查找或与数据流相关联。 为了在 Azure 流分析作业中利用参考数据，通常会在查询中使用[参考数据联接](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics)。 有关详细信息，请参阅[在流分析中使用参考数据进行查找](stream-analytics-use-reference-data.md)。

仅支持本地参考数据。 将作业部署到 IoT Edge 设备时，它将从用户定义的文件路径中加载参考数据。

若要在 Edge 上创建包含参考数据的作业，请执行以下操作：

1. 为作业创建一个新输入。

2. 选择“参考数据”作为”源类型“。

3. 在设备上将参考数据文件准备就绪。 对于 Windows 容器，请将参考数据文件放置在本地驱动器上并通过 Docker 容器共享本地驱动器。 对于 Linux 容器，请创建一个 Docker 卷并将该数据文件填充到该卷。

4. 设置文件路径。 对于 Windows 主机 OS 和 Windows 容器，请使用绝对路径：`E:\<PathToFile>\v1.csv`。 对于 Windows 主机 OS 和 Linux 容器或 Linux OS 以及 Linux 容器，请使用卷中的路径：`<VolumeName>/file1.txt`。

![为 IoT Edge 上的 Azure 流分析作业新建参考数据输入](./media/stream-analytics-edge/Reference-Data-New-Input.png)

IoT Edge 上的参考数据更新将由部署触发。 在触发后，ASA 模块选取更新的数据且不停止正在运行的作业。

有两种方式可用来更新参考数据：
* 从 Azure 门户中更新 ASA 作业中的参考数据路径。
* 更新 IoT Edge 部署。

## <a name="license-and-third-party-notices"></a>许可证和第三方通知
* [IoT Edge 上的 Azure 流分析许可证](https://go.microsoft.com/fwlink/?linkid=862827)。 
* [IoT Edge 上的 Azure 流分析的第三方通知](https://go.microsoft.com/fwlink/?linkid=862828)。

## <a name="azure-stream-analytics-module-image-information"></a>Azure 流分析模块映像信息 

此版本信息上次更新时间：2019-06-27：

- 映像： `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - 基本映像： microsoft/dotnet： 2.1.6-alpine 3。7
   - 平台
      - 体系结构： amd64
      - 操作系统： linux
  
- 映像： `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - 基本映像： microsoft/dotnet： 2.1.6-bionic-arm32v7
   - 平台
      - 体系结构： arm
      - 操作系统： linux
  
- 映像： `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - 基本映像： microsoft/dotnet： 2.1.6-nanoserver-1809
   - 平台
      - 体系结构： amd64
      - 操作系统： windows
      
      
## <a name="get-help"></a>获取帮助
如需进一步的帮助，请尝试参考 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤

* [Azure IoT Edge 的详细信息](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [IoT Edge 教程上的 ASA ](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [使用 Visual Studio 工具开发流分析 Edge 作业](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [使用 API 实现流分析的 CI/CD](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
