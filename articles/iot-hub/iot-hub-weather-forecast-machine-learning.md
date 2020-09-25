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
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 5f51ffc3135ff35214a2c5c40cce1f2b3fcaf33e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91290875"
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
- [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json#types-of-storage-accounts)是**常规用途 v2**帐户，但支持 azure Blob 存储的任何 azure 存储帐户也会起作用。

> [!Note]
> 本文使用 Azure 流分析和多个其他付费服务。 当必须在 Azure 区域之间传输数据时，Azure 流分析会产生额外的费用。 出于此原因，最好确保资源组、IoT 中心和 Azure 存储帐户，以及本教程后面添加的机器学习 Studio (经典) 工作区和 Azure 流分析作业，它们都位于同一 Azure 区域。 可以在 " [按区域的 Azure 产品可用性" 页](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)上查看 Azure 机器学习 Studio 和其他 Azure 服务的区域支持。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>将天气预测模型部署为 Web 服务

在本部分中，你将从 Azure AI 库获取天气预报预测模型。 然后，将一个 R 脚本模块添加到该模型，以清理温度和湿度数据。 最后，将该模型部署为预测性 web 服务。

### <a name="get-the-weather-prediction-model"></a>获取天气预报预测模型

在本部分中，你将从 Azure AI 库获取天气预报预测模型，并在 Azure 机器学习 Studio (经典) 中将其打开。

1. 转到[天气预测模型页](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)。

   ![在 Azure AI 库中打开 "天气预报预测模型" 页](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. 选择 **"在工作室中打开" (经典) ** 以 Microsoft Azure 机器学习工作室 (经典) 中打开模型。 在 " **从库中复制试验** " 弹出窗口中选择与 IoT 中心附近的区域并选择正确的工作区。

   ![在 Azure 机器学习 Studio 中打开天气预报模型 (经典) ](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>添加 R 脚本模块以清理温度和湿度数据

若要使模型正常运行，温度和湿度数据必须可转换为数值数据。 在本部分中，将向天气预测模型中添加一个 R 脚本模块，该模块删除其温度或湿度数据值不能转换为数字值的任何行。

1. 在 Azure 机器学习 Studio 窗口的左侧，选择箭头以展开 "工具" 面板。 在搜索框中输入 "执行"。 选择 " **执行 R 脚本** " 模块。

   ![选择执行 R 脚本模块](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. 将 **执行 R 脚本** 模块拖到关系图上的 " **清理缺失数据** " 模块和现有 " **执行 r 脚本** " 模块附近。 删除 " **清理缺失数据** " 和 " **执行 R 脚本** " 模块之间的连接，然后连接新模块的输入和输出，如下所示。

   ![添加执行 R 脚本模块](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. 选择新的 " **执行 R 脚本** " 模块以打开其 "属性" 窗口。 将以下代码复制并粘贴到 " **R 脚本** " 框中。

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   完成后，"属性" 窗口应如下所示：

   ![添加代码以执行 R 脚本模块](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>部署预测 web 服务

在本部分中，您将验证模型、根据模型设置预测性 web 服务，然后部署 web 服务。

1. 选择 " **运行** " 以验证模型中的步骤。 此步骤可能需要几分钟才能完成。

   ![运行试验来验证步骤](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. 选择 "**设置 WEB 服务**  >  **预测 web 服务**"。 预测试验图随即打开。

   ![在 Azure 机器学习 Studio (经典) 中部署天气预报预测模型](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. 在预测试验图中，删除 **Web 服务输入** 模块和顶部的 " **选择数据集中的列** " 之间的连接。 然后，将 " **Web 服务输入** 模块" 拖到 " **评分模型** " 模块附近的某个位置，并按如下所示连接：

   ![将两个模块连接 Azure 机器学习 Studio (经典) ](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. 选择 " **运行** " 以验证模型中的步骤。

1. 选择 " **部署 WEB 服务** " 以将模型部署为 WEB 服务。

1. 在模型的仪表板上，下载“Excel 2010 或更早版本的工作簿”**** 用于“请求/响应”****。

   > [!Note]
   > 即使您在计算机上运行更高版本的 Excel，也请确保下载 **excel 2010 或更早版本的工作簿** 。

   ![为请求响应终结点下载 Excel](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. 打开 Excel 工作簿，记下“WEB 服务 URL”**** 和“访问密钥”****。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>创建、配置和运行流分析作业

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“创建资源”。 在搜索框中键入 "流分析作业"，然后从 "结果" 下拉列表中选择 " **流分析作业** "。 当 " **流分析作业** " 窗格打开时，选择 " **创建**"。
1. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。

   **订阅**：如果你的订阅不同于默认订阅，请选择它。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：与资源组使用同一位置。

   将所有其他字段保留默认值。

   ![在 Azure 中创建流分析作业](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. 选择“创建” 。

### <a name="add-an-input-to-the-stream-analytics-job"></a>将输入添加到流分析作业

1. 打开流分析作业。
1. 在“作业拓扑”下选择“输入”。
1. 在 " **输入** " 窗格中，选择 " **添加流输入**"，然后从下拉列表中选择 " **IoT 中心** "。 在 " **新建输入** " 窗格中，选择 " **从订阅中选择 IoT 中心** "，并输入以下信息：

   **输入别名**：输入的唯一别名。

   **订阅**：如果你的订阅不同于默认订阅，请选择它。

   **Iot 中心**：从订阅中选择 IoT 中心。

   **共享访问策略名称**：选择 "  **服务**"。  (还可以使用 **iothubowner**。 ) 

   **使用者组**：选择你创建的使用者组。

   将所有其他字段保留默认值。

   ![向 Azure 中的流分析作业添加输入](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. 选择“保存”。

### <a name="add-an-output-to-the-stream-analytics-job"></a>将输出添加到流分析作业

1. 在“作业拓扑”下选择“输出”。
1. 在 " **输出** " 窗格中，选择 " **添加**"，然后从下拉列表中选择 " **Blob 存储/Data Lake Storage** "。 在 " **新建输出** " 窗格中，选择 " **从订阅中选择存储** "，并输入以下信息：

   **输出别名**：输出的唯一别名。

   **订阅**：如果你的订阅不同于默认订阅，请选择它。

   **存储帐户**：Blob 存储的存储帐户。 可以创建一个存储帐户或使用现有存储帐户。

   **容器**：保存 Blob 的容器。 可以创建一个容器或使用现有容器。

   **事件序列化格式**：选择“CSV”****。

   ![向 Azure 中的流分析作业添加输出](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. 选择“保存”。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>向流分析作业添加函数以调用你部署的 Web 服务

1. 在 " **作业拓扑**" 下，选择 " **函数**"。
1. 在 " **函数** " 窗格中，选择 " **添加**"，然后从下拉列表中选择 " **Azure 机器学习工作室** "。  (确保选择 " **AZURE Ml Studio**，而不 **是 azure ml 服务**"。 ) 在 " **新建函数** " 窗格上，选择 " **手动提供 Azure 机器学习函数设置** "，并输入以下信息：

   **函数别名**：输入 `machinelearning`。

   **URL**：输入从 Excel 工作簿记下的 WEB 服务 URL。

   **密钥**：输入从 Excel 工作簿记下的访问密钥。

   ![向 Azure 中的流分析作业添加函数](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. 选择“保存”。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下选择“查询”。
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

1. 选择“保存查询”。

> [!Note]
> 如果选择 " **测试查询**"，则会显示以下消息：不支持包含机器学习函数的查询测试。 请修改该查询，然后重试。 您可以放心地忽略此消息，然后选择 **"确定"** 关闭该消息框。 请确保在继续下一部分之前保存查询。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，选择左窗格中的 " **概述** "。 然后选择 "立即**启动**"  >  **Now**  >  **Start**。 成功启动作业后，作业状态将从“已停止”  更改为“正在运行”  。

![运行流分析作业](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>使用 Microsoft Azure 存储资源管理器查看天气预报

运行客户端应用程序，以开始收集温度和湿度数据并将其发送到 IoT 中心。 对于 IoT 中心收到的每条消息，流分析作业将调用天气预报 Web 服务，以生成下雨的可能性。 然后，结果将保存到 Azure Blob 存储。 Azure 存储资源管理器是一种可用于查看结果的工具。

1. [下载并安装 Microsoft Azure 存储资源管理器](https://storageexplorer.com/)。
1. 打开 Azure 存储资源管理器。
1. 登录 Azure 帐户。
1. 选择订阅。
1. > **存储** 帐户中选择你的订阅 > 存储帐户 > 容器中的 **Blob > 容器** 。
1. 下载 .csv 文件以查看结果。 最后一列记录下雨的可能性。

   ![使用 Azure 机器学习获取天气预报结果](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>总结

已成功使用 Azure 机器学习基于 IoT 中心收到的温度和湿度数据生成下雨的可能性。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
