---
title: "使用 IoT 中心的数据通过 Azure 机器学习进行天气预报 | Microsoft Docs"
description: "使用 Azure 机器学习基于 IoT 中心从传感器收集的温度和湿度数据来预测下雨的可能性。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "天气预报机器学习"
ms.assetid: 8ba7d9e7-699c-4448-b353-0f3e1429d198
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: xshi
ms.openlocfilehash: 50ae54b9476c49b80236e295c0bf244df8236cff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
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

- 已完成教程[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)，其中涵盖以下要求：
  - 一个有效的 Azure 订阅。
  - 已在订阅中创建一个 Azure IoT 中心。
  - 一个可向 Azure IoT 中心发送消息的客户端应用程序。
- Azure 机器学习工作室帐户。 （[免费试用机器学习工作室](https://studio.azureml.net/)）。

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>将天气预测模型部署为 Web 服务

1. 转到[天气预测模型页](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)。
1. 在 Microsoft Azure 机器学习工作室中单击“在工作室中打开”。
   ![在 Cortana Intelligence 库中打开天气预测模型页](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. 单击“运行”以验证模型中的步骤。 此步骤可能需要 2 分钟才能完成。
   ![在 Azure 机器学习工作室中打开天气预测模型](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. 单击“设置 WEB 服务” > “预测 Web 服务”。
   ![在 Azure 机器学习工作室中部署天气预测模型](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. 在关系图中，将“Web 服务输入”模块拖动至“评分模型”模块附近的某个位置。
1. 将“Web 服务输入”模块连接到“评分模型”模块。
   ![在 Azure 机器学习工作室中连接两个模块](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. 单击“运行”以验证模型中的步骤。
1. 单击“部署 WEB 服务”以将模型部署为 Web 服务。
1. 在模型的仪表板上，下载“Excel 2010 或更早版本的工作簿”用于“请求/响应”。

   > [!Note]
   > 即使你在计算机上运行更高版本的 Excel，也请确保下载 Excel 2010 或更早版本的工作簿。

   ![为请求响应终结点下载 Excel](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. 打开 Excel 工作簿，记下“WEB 服务 URL”和“访问密钥”。

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>创建、配置和运行流分析作业

### <a name="create-a-stream-analytics-job"></a>创建流分析作业

1. 在 [Azure 门户](https://ms.portal.azure.com/)中，单击“新建” > “物联网” > “流分析作业”。
1. 为作业输入以下信息。

   **作业名称**：作业的名称。 该名称必须全局唯一。

   **资源组**：使用 IoT 中心所用的同一资源组。

   **位置**：与资源组使用同一位置。

   **固定仪表板**：选中此选项可以方便地从仪表板访问 IoT 中心。

   ![在 Azure 中创建流分析作业](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. 单击“创建”。

### <a name="add-an-input-to-the-stream-analytics-job"></a>向流分析作业添加输入

1. 打开流分析作业。
1. 在“作业拓扑”下，单击“输入”。
1. 在“输入”窗格中单击“添加”，并输入以下信息：

   **输入别名**：输入的唯一别名。

   **源**：选择“IoT 中心”。

   **使用者组**：选择你创建的使用者组。

   ![向 Azure 中的流分析作业添加输入](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. 单击“创建” 。

### <a name="add-an-output-to-the-stream-analytics-job"></a>向流分析作业添加输出

1. 在“作业拓扑”下，单击“输出”。
1. 在“输出”窗格中单击“添加”，并输入以下信息：

   **输出别名**：输出的唯一别名。

   **接收器**：选择“Blob 存储”。

   **存储帐户**：Blob 存储的存储帐户。 可以创建一个存储帐户或使用现有存储帐户。

   **容器**：保存 Blob 的容器。 可以创建一个容器或使用现有容器。

   **事件序列化格式**：选择“CSV”。

   ![向 Azure 中的流分析作业添加输出](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. 单击“创建” 。

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>向流分析作业添加函数以调用你部署的 Web 服务

1. 在“作业拓扑”下，单击“函数” > “添加”。
1. 输入以下信息：

   **函数别名**：输入 `machinelearning`。

   **函数类型**：选择“Azure ML”。

   **导入选项**：选择“从其他订阅导入”。

   **URL**：输入从 Excel 工作簿记下的 WEB 服务 URL。

   **密钥**：输入从 Excel 工作簿记下的访问密钥。

   ![向 Azure 中的流分析作业添加函数](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. 单击“创建” 。

### <a name="configure-the-query-of-the-stream-analytics-job"></a>配置流分析作业的查询

1. 在“作业拓扑”下，单击“查询”。
1. 将现有代码替换为以下代码：

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   将 `[YourInputAlias]` 替换为作业的输入别名。

   将 `[YourOutputAlias]` 替换为作业的输出别名。

1. 单击“保存” 。

### <a name="run-the-stream-analytics-job"></a>运行流分析作业

在流分析作业中，单击“启动” > “现在” > “启动”。 成功启动作业以后，作业状态将从“已停止”更改为“正在运行”。

![运行流分析作业](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>使用 Microsoft Azure 存储资源管理器查看天气预报

运行客户端应用程序，以开始收集温度和湿度数据并将其发送到 IoT 中心。 对于 IoT 中心收到的每条消息，流分析作业将调用天气预报 Web 服务，以生成下雨的可能性。 然后，结果将保存到 Azure Blob 存储。 Azure 存储资源管理器是一种可用于查看结果的工具。

1. [下载并安装 Microsoft Azure 存储资源管理器](http://storageexplorer.com/)。
1. 打开 Azure 存储资源管理器。
1. 登录到 Azure 帐户。
1. 选择订阅。
1. 单击 Azure 订阅 >“存储帐户”> 存储帐户 >“ Blob 容器”> 容器。
1. 打开一个 .csv 文件以查看结果。 最后一列记录下雨的可能性。

   ![使用 Azure 机器学习获取天气预报结果](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>摘要

已成功使用 Azure 机器学习基于 IoT 中心收到的温度和湿度数据生成下雨的可能性。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]