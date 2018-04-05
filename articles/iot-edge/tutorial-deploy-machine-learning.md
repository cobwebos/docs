---
title: 使用 Azure IoT Edge 部署 Azure 机器学习 | Microsoft 文档
description: 将 Azure 机器学习作为一个模块部署到 Edge 设备
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/12/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3d3a271bcdd5c507125b8b1a5482f833607a5a78
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="deploy-azure-machine-learning-as-an-iot-edge-module---preview"></a>将 Azure 机器学习作为 IoT Edge 模块进行部署 - 预览版

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程将逐步演示部署 Azure 机器学习模块的过程，该模块可根据 IoT Edge 模拟设备（该设备是在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程“在模拟设备上部署 Azure IoT Edge”中创建的）上的传感器数据预测设备何时出现故障。 

本教程介绍如何执行下列操作： 

> [!div class="checklist"]
> * 创建 Azure 机器学习模块
> * 将模块容器推送到 Azure 容器注册表
> * 将 Azure 机器学习模块部署到 loT Edge 设备
> * 查看生成的数据

本教程中创建的 Azure 机器学习模块将读取设备生成的环境数据，并将消息标记为异常或正常。 

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或第一个教程创建 Azure IoT Edge 设备。
* IoT 中心连接字符串适用于 IoT Edge 设备连接到的 IoT 中心。
* 一个 Azure 机器学习帐户。 若要创建帐户，请按照[创建 Azure 机器学习帐户和安装 Azure Machine Learning Workbench](../machine-learning/preview/quickstart-installation.md#create-azure-machine-learning-services-accounts) 中的说明进行操作。 对于本教程，不需要安装 Workbench 应用程序。 
* 计算机上的“Azure ML 模块管理”。 若要设置环境并创建帐户，请按照[模型管理设置](https://docs.microsoft.com/azure/machine-learning/preview/deployment-setup-configuration)中的说明进行操作。

Azure 机器学习模块不支持 ARM 处理器。 

## <a name="create-the-azure-ml-container"></a>创建 Azure ML 容器
在本部分中，你将下载已训练的模型文件并将它们转换为 Azure ML 容器。  

在运行“Azure ML 模块管理”的计算机上，从 GitHub 上的 Azure ML IoT Toolkit 下载并保存 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 和 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)。 这些文件定义了将部署到 Iot Edge 设备的已训练的机器学习模型。 

使用已训练的模型创建可以部署到 IoT Edge 设备的容器。 使用以下命令：

   * 注册模型。
   * 创建清单。
   * 创建名为 *machinelearningmodule* 的 Docker 容器映像。
   * 将映像部署到 Azure 容器服务 (AKS) 群集。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>查看容器存储库

检查容器映像是否已成功创建并存储在与机器学习环境关联的 Azure 容器存储库中。

1. 在 [Azure 门户](https://portal.azure.com)中，转到“所有服务”并选择“容器注册表”。
2. 选择你的注册表。 名称应当以 **mlcr** 开头，并且它属于你用来设置模块管理的资源组、位置和订阅。
3. 选择“访问密钥”
4. 复制“登录服务器”、“用户名”和“密码”的值。  你需要使用这些值来从 Edge 设备访问注册表。
5. 选择“存储库”
6. 选择“machinelearningmodule”
7. 你现在已获得了容器的完整映像路径。 请记下此映像路径供下一部分使用。 它应当类似于以下内容：**<registry_name>.azureacr.io/machinelearningmodule:1**

## <a name="add-registry-credentials-to-your-edge-device"></a>将注册表凭据添加到 Edge 设备

在运行 Edge 设备的计算机上将注册表凭据添加到 Edge 运行时。 此命令为该运行时提供访问权限来拉取容器。

Linux：
   ```cmd
   sudo iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

Windows:
   ```cmd
   iotedgectl login --address <registry-login-server> --username <registry-username> --password <registry-password> 
   ```

## <a name="run-the-solution"></a>运行解决方案

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
1. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
1. 选择“设置模块”。
1. 如果以前已将 tempSensor 模块部署到 IoT Edge 设备，则它可能会自动填充。 如果它尚未出现在模块列表中，请添加它。
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 选择“保存”。
1. 添加你创建的机器学习模块。
    1. 选择“添加 IoT Edge 模块”。
    1. 在“名称”字段中，输入 `machinelearningmodule`。
    1. 在“映像”字段中，输入映像地址；例如 `<registry_name>.azurecr.io/machinelearningmodule:1`。
    1. 选择“保存”。
1. 返回到“添加模块”步骤，选择“下一步”。
1. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 第一个路由通过“amlInput”终结点将消息从温度传感器传输到机器学习模块，这是所有 Azure 机器学习模块都使用的终结点。 第二个路由会将消息从机器学习模块传输到 IoT 中心。 在此路由中，“amlOutput”是所有 Azure 机器学习模块用来输出数据的终结点，“$upstream”表示 IoT 中心。 

    ```json
    {
        "routes": {
            "sensorToMachineLearning":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/machinelearningmodule/inputs/amlInput\")",
            "machineLearningToIoTHub": "FROM /messages/modules/machinelearningmodule/outputs/amlOutput INTO $upstream"
        }
    }
    ``` 

1. 选择“**下一步**”。 
1. 在“审阅模板”步骤中，选择“提交”。 
1. 返回到“设备详细信息”页，并选择“刷新”。  你应该会看到新的“machinelearningmodule”在与“tempSensor”模块和 IoT Edge 运行时模块同时运行。

## <a name="view-generated-data"></a>查看生成的数据

可以使用 [IoT 中心资源管理器](https://github.com/azure/iothub-explorer)或 Visual Studio Code 的 Azure IoT Toolkit 扩展查看 IoT Edge 设备发送的设备到云消息。 

1. 在 Visual Studio Code 中，选择“IoT 中心设备”。 
2. 选择 **...**，然后从菜单中选择“设置 IoT 中心连接字符串”。 

   ![IoT 中心设备“更多”菜单](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 在页面顶部打开的文本框中，输入你的 IoT 中心的 iothubowner 连接字符串。 你的 IoT Edge 设备应当会出现在“IoT 中心设备”列表中。
4. 再次选择 **...**，然后选择“开始监视 D2C 消息”。
5. 观察每五秒来自 tempSensor 的消息。 消息正文包含 machinelearningmodule 提供的名为 **anomaly**、值为 true 或 false 的属性。 如果模型成功运行，**AzureMLResponse** 属性包含值“OK”。 

   ![消息正文中的 Azure ML 响应](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="next-steps"></a>后续步骤

在本教程中，可以部署由 Azure 机器学习提供支持的 IoT Edge 模块。 你可以继续查看任何其他教程，了解 Azure IoT Edge 可帮助你将数据转化为临界业务见解的其他方式。

> [!div class="nextstepaction"]
> [将 Azure Functions 作为模块部署](tutorial-deploy-function.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
