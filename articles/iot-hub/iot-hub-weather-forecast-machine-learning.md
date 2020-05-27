---
title: 使用 Azure 机器学习根据 IoT 中心数据预测天气
description: 使用 Azure 机器学习基于 IoT 中心从传感器收集的温度和湿度数据来预测下雨的可能性。
author: robinsh
manager: philmea
keywords: 天气预报机器学习
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: 5551655843b8d3ed5b6d70f5d6ed3a0eb4d0e92f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746960"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>在 Azure 机器学习中使用 IoT 中心的传感器数据进行天气预报

![端到端关系图](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

机器学习是一项数据科研技术，可帮助计算机从现有的数据中学习，预测将来的行为、结果和趋势。 Azure 机器学习是一种云预测分析服务，使用它可以快速创建预测模型，并将其部署为分析解决方案。

## <a name="what-you-learn"></a>学习内容

了解如何使用 Azure 机器学习根据 Azure IoT 中心的温度和湿度数据来进行天气预报（下雨的可能性）。 下雨的可能性是已准备的天气预测模型的输出。 该模型基于历史数据构建，以根据温度和湿度预测下雨的可能性。

## <a name="what-you-do"></a>准备工作

- 将天气预测模型部署为 Web 服务。
- 添加一个使用者组，让 IoT 中心做好数据访问准备。
- 创建流分析作业并将作业配置为：
  - 从 IoT 中心读取温度和湿度数据。
  - 调用 Web 服务以获取下雨的可能性。
  - 将结果保存到 Azure Blob 存储中。
- 使用 Microsoft Azure 存储资源管理器查看天气预报。

## <a name="what-you-need"></a>所需条件

- 完成 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)教程或其中一个设备教程；例如[将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)。 这包括以下要求：
  - 一个有效的 Azure 订阅。
  - 已在订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的客户端应用程序。
- 一个 [Azure 机器学习工作室（经典版）](https://studio.azureml.net/)帐户。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>将天气预测模型部署为 Web 服务

在本部分，你将从 Azure AI 库获取天气预测模型。 然后，将一个 R 脚本模块添加到该模型，以清理温度和湿度数据。 最后，将该模型部署为预测 Web 服务。

### <a name="get-the-weather-prediction-model"></a>获取天气预测模型

在本部分，你将从 Azure AI 库获取天气预测模型，并在 Azure 机器学习工作室（经典版）中将其打开。

1. 转到[天气预测模型页](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)。

   ![打开 Azure AI 库中的天气预测模型页](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. 单击“在工作室(经典版)中打开”，以便在 Microsoft Azure 机器学习工作室（经典版）中打开该模型。 

   ![在 Azure 机器学习工作室（经典版）中打开天气预测模型](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>添加一个 R 脚本模块用于清理温度和湿度数据

若要使模型正常运行，温度和湿度数据必须可转换为数字数据。 在本部分，你将在天气预测模型中添加一个 R 脚本模块，用于删除所有包含无法转换为数字值的温度或湿度数据值的行。

1. 在 Azure 机器学习工作室窗口的左侧，单击箭头展开“工具”面板。 在搜索框中输入“执行”。 选择“执行 R 脚本”模块。 

   ![选择“执行 R 脚本”模块](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. 将“清理缺失数据”模块旁边的“执行 R 脚本”模块以及现有的“执行 R 脚本”模块拖到图中。    删除“清理缺失数据”与“执行 R 脚本”模块之间的连接，然后如下所示连接新模块的输入和输出。  

   ![添加“执行 R 脚本”模块](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 选择新的“执行 R 脚本”模块打开其“属性”窗口。  将以下代码复制并粘贴到“R 脚本”框中。 

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   完成后，“属性”窗口应如下所示：

   ![将代码添加到“执行 R 脚本”模块](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>部署预测 Web 服务

在本部分，你将验证模型，根据该模型设置预测 Web 服务，然后部署该 Web 服务。

1. 单击“运行”  以验证模型中的步骤。 此步骤可能需要几分钟才能完成。

   ![运行试验以验证步骤](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. 单击“设置 WEB 服务”   > “预测 Web 服务”  。 预测试验图随即打开。

   ![在 Azure 机器学习工作室（经典版）中部署天气预测模型](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 在预测试验图中，删除“Web 服务输入”模块与顶部“天气数据集”之间的连接。   然后将“Web 服务输入”模块拖到“评分模型”模块附近的某个位置，并连接该输入模块，如下所示：  

   ![在 Azure 机器学习工作室（经典版）中连接两个模块](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. 单击“运行”  以验证模型中的步骤。

1. 单击“部署 WEB 服务”  以将模型部署为 Web 服务。

1. 在模型的仪表板上，下载“Excel 2010 或更早版本的工作簿”  用于“请求/响应”  。

   > [!Note]
   > 即使在计算机上运行更高版本的 Excel，也请确保下载 **Excel 2010 或更低版本的工作簿**。

   ![为请求响应终结点下载 Excel](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. 打开 Excel 工作簿，记下“WEB 服务 URL”  和“访问密钥”  。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>创建、配置和运行流分析作业

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“创建资源”   > “物联网”   > “流分析作业”  。
1. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：与资源组使用同一位置。

   **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

   ![在 Azure 中创建流分析作业](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. 单击“创建”。 

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 打开流分析作业。
1. 在“作业拓扑”下  ，单击“输入”  。
1. 在“输入”  窗格中单击“添加”  ，并输入以下信息：

   **输入别名**：输入的唯一别名。

   **源**：选择“IoT 中心”  。

   **使用者组**：选择你创建的使用者组。

   ![向 Azure 中的流分析作业添加输入](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. 单击“创建”。 

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下  ，单击“输出”  。
1. 在“输出”  窗格中单击“添加”  ，并输入以下信息：

   **输出别名**：输出的唯一别名。

   **接收器**：选择“Blob 存储”  。

   **存储帐户**：Blob 存储的存储帐户。 可以创建一个存储帐户或使用现有存储帐户。

   **容器**：保存 Blob 的容器。 可以创建一个容器或使用现有容器。

   **事件序列化格式**：选择“CSV”  。

   ![向 Azure 中的流分析作业添加输出](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. 单击“创建”。 

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>向流分析作业添加函数以调用你部署的 Web 服务

1. 在“作业拓扑”  下，单击“函数”   > “添加”  。
1. 输入以下信息：

   **函数别名**：输入 `machinelearning`。

   **函数类型**：选择“Azure ML”  。

   **导入选项**：选择“从其他订阅导入”  。

   **URL**：输入从 Excel 工作簿记下的 WEB 服务 URL。

   **密钥**：输入从 Excel 工作簿记下的访问密钥。

   ![向 Azure 中的流分析作业添加函数](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. 单击“创建”。 

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下  ，单击“查询”  。
1. 将现有代码替换为以下代码：

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   将 `[YourInputAlias]` 替换为作业的输入别名。

   将 `[YourOutputAlias]` 替换为作业的输出别名。

1. 单击“保存”  。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，单击“启动”   > “现在”   >   “启动”。 成功启动作业后，作业状态将从“已停止”  更改为“正在运行”  。

![运行流分析作业](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>使用 Microsoft Azure 存储资源管理器查看天气预报

运行客户端应用程序，以开始收集温度和湿度数据并将其发送到 IoT 中心。 对于 IoT 中心收到的每条消息，流分析作业将调用天气预报 Web 服务，以生成下雨的可能性。 然后，结果将保存到 Azure Blob 存储。 Azure 存储资源管理器是一种可用于查看结果的工具。

1. [下载并安装 Microsoft Azure 存储资源管理器](https://storageexplorer.com/)。
1. 打开 Azure 存储资源管理器。
1. 登录 Azure 帐户。
1. 选择订阅。
1. 单击 Azure 订阅 >“存储帐户”  > 存储帐户 >“ Blob 容器”  > 容器。
1. 下载一个 .csv 文件以查看结果。 最后一列记录下雨的可能性。

   ![使用 Azure 机器学习获取天气预报结果](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>总结

已成功使用 Azure 机器学习基于 IoT 中心收到的温度和湿度数据生成下雨的可能性。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
