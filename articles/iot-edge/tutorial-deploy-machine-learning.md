---
title: "使用 Azure IoT Edge 部署 Azure 机器学习 | Microsoft 文档"
description: "将 Azure 机器学习作为一个模块部署到 Edge 设备"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: e061e599f365bf3d343cb59b8dc6a61e06627517
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2017
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>将 Azure 机器学习作为 IoT Edge 模块进行部署 - 预览版

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程将逐步演示部署 Azure 机器学习模块的过程，该模块可根据 IoT Edge 模拟设备（该设备是 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程“在模拟设备上部署 Azure IoT Edge”部分创建的）上的传感器数据预测设备何时出现故障。 学习如何： 

> [!div class="checklist"]
> * 将 Azure 机器学习模块部署到 loT Edge 设备
> * 查看生成的数据

如果想要在解决方案中使用你自己的 [Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/preview/)模型，则需要为 loT Edge [部署一个模型](https://aka.ms/aml-iot-edge-doc)，并将其托管在容器注册表中，如 [Azure 容器注册表](../container-registry/index.yml)或 Docker。

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或第一个教程创建 Azure IoT Edge 设备。
* IoT 中心连接字符串适用于 IoT Edge 设备连接到的 IoT 中心。
* Azure ML 容器

## <a name="create-the-azure-ml-container"></a>创建 Azure ML 容器
若要创建 Azure ML 容器，请按照 [Azure IoT Edge 的 AI 工具包](https://aka.ms/aml-iot-edge-anomaly-detection)中的说明操作。

## <a name="run-the-solution"></a>运行解决方案

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
1. 选择“设置模块”。
1. 选择“添加 IoT Edge 模块”。
1. 在“名称”字段中，输入 `tempSensor`。
1. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
1. 将其他设置保留不变，然后选择“保存”。
1. 也可以在“添加模块”步骤中，再次选择“添加 IoT Edge 模块”。
1. 在“名称”字段中，输入在上一节中设定的容器名称。 请参阅 [Azure IoT Edge 的 AI 工具包](https://aka.ms/aml-iot-edge-anomaly-detection)来帮助查找名称。
1. 在“映像”字段中，输入在上一节中设定的容器的映像 URI。 请参阅 [Azure IoT Edge 的 AI 工具包](https://aka.ms/aml-iot-edge-anomaly-detection)来帮助查找映像。
1. 单击“保存” 。
1. 返回到“添加模块”步骤，单击“下一步”。
1. 更新模块路由：
1. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 这些模块会将所有消息发布到 Edge 运行时。 此运行时中的声明性规则将定义这些消息流经的位置。 本教程需要两个路由。 第一个路由通过“amlInput”终结点将消息从温度传感器传输到机器学习模块，这是所有 Azure 机器学习模块都使用的终结点。 第二个路由会将消息从机器学习模块传输到 IoT 中心。 在此路由中，“amlOutput”是所有 Azure 机器学习模块用于输出数据的终结点，“$upstream”则是一个特殊目标，告知 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. 单击“下一步”。 
1. 在“审阅模板”步骤中，单击“提交”。 
1. 返回到“设备详细信息”页，并单击“刷新”。  应看到新的“machinelearningmodule”与“tempSensor 模块”和“IoT Edge 运行时”在同时运行。

## <a name="view-generated-data"></a>查看生成的数据

 在 VS Code 中，请使用“视图 | 命令面板... | IoT: 开始监视 D2C 消息”菜单命令来监视 loT 中心收到的数据。 

## <a name="next-steps"></a>后续步骤

在本教程中，可以部署由 Azure 机器学习提供支持的 IoT Edge 模块。 你可以继续查看任何其他教程，了解 Azure IoT Edge 可帮助你将数据转化为临界业务见解的其他方式。

> [!div class="nextstepaction"]
> [将 Azure Functions 作为模块部署](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
