---
title: 使用 Azure IoT Edge 部署 Azure 机器学习 | Microsoft 文档
description: 在本教程中，请将 Azure 机器学习作为一个模块部署到边缘设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/25/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 28b963922b423bb776aa97e9b76392bc484ddcd6
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627801"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>教程：将 Azure 机器学习作为 IoT Edge 模块（预览版）进行部署

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程逐步演示如何部署 Azure 机器学习模块，以便根据模拟的机器温度数据预测设备故障时间。 有关 IoT Edge 上的 Azure ML 的详细信息，请参阅 [Azure 机器学习文档](../machine-learning/desktop-workbench/use-azure-iot-edge-ai-toolkit.md)。

本教程中创建的 Azure 机器学习模块将读取设备生成的环境数据，并将消息标记为异常或正常。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure 机器学习模块
> * 将模块容器推送到 Azure 容器注册表
> * 将 Azure 机器学习模块部署到 IoT Edge 设备
> * 查看生成的数据

>[!NOTE]
>Azure IoT Edge 上的 Azure 机器学习模块为公开预览版。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。
* Azure 机器学习模块不支持 ARM 处理器。

云资源：

* Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 
* 一个 Azure 机器学习帐户。 按照[创建 Azure 机器学习帐户和安装 Azure Machine Learning Workbench](../machine-learning/service/quickstart-installation.md#create-azure-machine-learning-services-accounts) 中的说明进行操作。 对于本教程，不需要安装 Workbench 应用程序。 

开发资源：

* Azure ML 的模型管理。 若要设置环境并创建帐户，请按照[模型管理设置](../machine-learning/desktop-workbench/deployment-setup-configuration.md)中的说明进行操作。 在设置部署期间，建议尽可能选择本地步骤而非群集。

### <a name="disable-process-identification"></a>禁用进程标识

>[!NOTE]
>
> Azure 机器学习预览版不支持 IoT Edge 中默认启用的进程标识安全功能。 
> 以下是禁用它的步骤。 不过，这不适合在生产中使用。 这些步骤只是在 Linux 上是必需的，因为在执行 Windows Edge 运行时安装时你已完成了这些步骤。

若要在 IoT Edge 设备上禁用进程标识，需在 IoT Edge 守护程序配置的 **connect** 部分中为 **workload_uri** 和 **management_uri** 提供 IP 地址和端口。

先获取 IP 地址。 在命令行中输入 `ifconfig`，复制 **docker0** 接口的 IP 地址。

编辑 IoT Edge 守护程序配置文件：

```cmd/sh
sudo nano /etc/iotedge/config.yaml
```

使用你的 IP 地址更新配置的 **connect** 节。 例如：
```yaml
connect:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

在配置的 **listen** 节中输入相同的地址。 例如：

```yaml
listen:
  management_uri: "http://172.17.0.1:15580"
  workload_uri: "http://172.17.0.1:15581"
```

创建包含 management_uri 地址的环境变量 IOTEDGE_HOST（若要对其进行永久设置，请将其添加到 `/etc/environment`）。例如：

```cmd/sh
export IOTEDGE_HOST="http://172.17.0.1:15580"
```


## <a name="create-the-azure-ml-container"></a>创建 Azure ML 容器
在本部分中，你将下载已训练的模型文件并将它们转换为 Azure ML 容器。

在运行“Azure ML 模型管理”的计算机上，从 GitHub 上的 Azure ML IoT Toolkit 下载并保存 [iot_score.py](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/iot_score.py) 和 [model.pkl](https://github.com/Azure/ai-toolkit-iot-edge/blob/master/IoT%20Edge%20anomaly%20detection%20tutorial/model.pkl)。 这些文件定义了将部署到 Iot Edge 设备的已训练的机器学习模型。

使用已训练的模型创建可以部署到 IoT Edge 设备的容器。 使用以下命令：

   * 注册模型。
   * 创建清单。
   * 创建名为 *machinelearningmodule* 的 Docker 容器映像。
   * 将映像部署到 Azure Kubernetes 服务 (AKS) 群集。

```cmd
az ml service create realtime --model-file model.pkl -f iot_score.py -n machinelearningmodule -r python
```

### <a name="view-the-container-repository"></a>查看容器存储库

检查容器映像是否已成功创建并存储在与机器学习环境关联的 Azure 容器注册表中。

1. 在 [Azure 门户](https://portal.azure.com)中，转到“所有服务”并选择“容器注册表”。
2. 选择你的注册表。 名称应当以 **mlcr** 开头，并且它属于你用来设置模块管理的资源组、位置和订阅。
3. 选择“访问密钥”
4. 复制“登录服务器”、“用户名”和“密码”的值。  你需要使用这些值来从 Edge 设备访问注册表。
5. 选择“存储库”
6. 选择“machinelearningmodule”
7. 你现在已获得了容器的完整映像路径。 请记下此映像路径供下一部分使用。 它应当类似于以下内容：**<registry_name>.azurecr.io/machinelearningmodule:1**

## <a name="deploy-to-your-device"></a>部署到设备

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。

1. 转到“IoT Edge”，然后选择 IoT Edge 设备。

1. 选择“设置模块”。

1. 在“注册表设置”部分，添加从 Azure 容器注册表复制的凭据。 

   ![添加注册表凭据](./media/tutorial-deploy-machine-learning/registry-settings.png)

1. 如果以前已将 tempSensor 模块部署到 IoT Edge 设备，则它可能会自动填充。 如果它尚未出现在模块列表中，请添加它。

    1. 单击“添加”，然后选择“IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`。
    4. 选择“保存”。

1. 添加你创建的机器学习模块。

    1. 单击“添加”，然后选择“IoT Edge 模块”。
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

1. 在“复查部署”步骤中，选择“提交”。

1. 返回到“设备详细信息”页，并选择“刷新”。  你应该会看到新的“machinelearningmodule”在与“tempSensor”模块和 IoT Edge 运行时模块同时运行。

## <a name="view-generated-data"></a>查看生成的数据

可以查看由每个 IoT Edge 模块生成的消息，还可以查看传送到 IoT 中心的消息。

### <a name="view-data-on-your-iot-edge-device"></a>查看 IoT Edge 设备上的数据

在 IoT Edge 设备上，可以查看从每个单独的模块发送的消息。 

如果在 Linux 设备上执行这些命令，可能需要使用 `sudo` 以获取提升的权限。

1. 查看 IoT Edge 设备上的所有模块。

   ```cmd/sh
   iotedge list
   ```

2. 查看从特定设备发送的消息。 使用从上一命令的输出中获取的模块名称。

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>查看到达 IoT 中心的数据

可以使用 [Visual Studio Code 的 Azure IoT Toolkit 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看 IoT 中心接收的设备到云消息。

以下步骤介绍如何设置 Visual Studio Code，以便监视到达 IoT 中心的设备到云消息。 

1. 在 Visual Studio Code 中，选择“IoT 中心设备”。

2. 选择 **...**，然后从菜单中选择“设置 IoT 中心连接字符串”。

   ![IoT 中心设备“更多”菜单](./media/tutorial-deploy-machine-learning/set-connection.png)

3. 在页面顶部打开的文本框中，输入你的 IoT 中心的 iothubowner 连接字符串。 你的 IoT Edge 设备应当会出现在“IoT 中心设备”列表中。

4. 再次选择 **...**，然后选择“开始监视 D2C 消息”。

5. 观察每五秒来自 tempSensor 的消息。 消息正文包含 machinelearningmodule 提供的名为 **anomaly**、值为 true 或 false 的属性。 如果模型成功运行，**AzureMLResponse** 属性包含值“OK”。

   ![消息正文中的 Azure ML 响应](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>清理资源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果想要继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可删除本文中创建的本地配置和 Azure 资源，避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 资源组以及包含在其中的所有资源一旦删除就会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请使用中心名称和资源组名称执行以下命令：

```azurecli-interactive
az iot hub delete --name {hub_name} --resource-group IoTEdgeResources
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

<!--
   ![Delete](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)
-->
4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

在本教程中，可以部署由 Azure 机器学习提供支持的 IoT Edge 模块。 你可以继续查看任何其他教程，了解 Azure IoT Edge 可帮助你将数据转化为临界业务见解的其他方式。

> [!div class="nextstepaction"]
> [使用 C# 代码筛选传感器数据](tutorial-csharp-module.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
