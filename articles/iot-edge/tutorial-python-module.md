---
title: 教程：创建自定义 Python 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 Python 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/25/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a8edf8d67c55cad856eacf883a6449606e594887
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343762"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>教程：开发 Python IoT Edge 模块，并将它部署到模拟设备

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用在快速入门中创建的模拟 IoT Edge 设备。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 IoT Edge Python 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。
* 用于 IoT Edge 的 Python 模块不支持 Windows 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* 适用于 Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Docker CE](https://docs.docker.com/engine/installation/)。 
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用于安装 Python 包（通常包含在 Python 安装中）。

>[!Note]
>确保 `bin` 文件夹在平台的路径中。 通常为 `~/.local/`（针对 UNIX 和 macOS）或 `%APPDATA%\Python`（针对 Windows）。

## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用适用于 Visual Studio Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

如果还没有容器注册表，请执行以下步骤，以便在 Azure 中创建一个新的：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

2. 提供以下值，以便创建容器注册表：

   | 字段 | 值 | 
   | ----- | ----- |
   | 注册表名称 | 提供唯一名称。 |
   | 订阅 | 从下拉列表中选择“订阅”。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 |
   | 管理员用户 | 设置为“启用”。 |
   | SKU | 选择“基本”。 | 

5. 选择“创建”。

6. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 

7. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值来访问容器注册表。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤使用 Visual Studio Code 和 Azure IoT Edge 扩展创建 IoT Edge Python 模块。

### <a name="create-a-new-solution"></a>创建新的解决方案

使用 Python 包 **cookiecutter** 创建一个 Python 解决方案模板，以便在其上生成项目。 

1. 在 Visual Studio Code 中选择“视图” > “终端”，打开 VS Code 集成终端。

2. 在集成终端中输入以下命令，以便安装（或更新）用于在 VS Code 中创建 IoT Edge 解决方案模板的 **cookiecuttere**：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```
   >[!Note]
   >确保将要在其中安装 Cookiecutter 的目录位于环境的 PATH 中，这样才可以从命令提示符处调用它。 目录是安装脚本的输出的一部分，例如 `C:\Users\{user}\AppData\Roaming\Python\Python{version}\Scripts`。
   >
   >重启 Visual Studio Code，使对 PATH 所做的更改生效。 

3. 选择“视图” > “命令面板”，打开 VS Code 命令面板。 

4. 在命令面板中，输入并运行命令“Azure: Sign in”并按照说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

5. 在命令面板中，输入并运行“Azure IoT Edge: New IoT Edge solution”命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“Python 模块”。 |
   | 提供模块名称 | 将模块命名为 **PythonModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/pythonmodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-python-module/repository.png)

VS Code 窗口将加载你的 IoT Edge 解决方案空间。 解决方案工作区包含五个顶级组件。 **modules** 文件夹包含模块的 Python 代码以及用于将模块构建为容器映像的 Dockerfile。 **\.env** 文件存储容器注册表凭据。 **deployment.template.json** 文件包含 IoT Edge 运行时用于在设备上部署模块的信息。 **deployment.debug.template.json** 文件包含模块的调试版本。 你不会在本教程中编辑 **\.vscode** 文件夹或 **\.gitignore** 文件。  

如果在创建解决方案时未指定容器注册表，但接受了默认的 localhost:5000 值，则不会有 \.env 文件。 

<!--
   ![Python solution workspace](./media/tutorial-python-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器存储库的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 

1. 在 VS Code 资源管理器中，打开 .env 文件。 
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

每个模板包含示例代码，该代码从 **tempSensor** 模块提取模拟传感器数据并将其路由到 IoT 中心。 在此节中，请添加代码，以便扩展用于分析消息的 **PythonModule**，然后再发送它们。 

1. 在 VS Code 资源管理器中，打开 **modules** > **PythonModule** > **main.py**。

2. 在 **main.py** 文件顶部导入 **json** 库。

    ```python
    import json
    ```

3. 在全局计数器下添加 **TEMPERATURE_THRESHOLD** 和 **TWIN_CALLBACKS** 变量。 温度阈值设置一个值，若要向 IoT 中心发送数据，测量的计算机温度必须超出该值。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = 0
    ```

4. 将 **receive_message_callback** 函数替换为以下代码：

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we forward this message to the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print ( "    Data: <<<%s>>> & Size=%d" % (message_text, size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature %s exceeds threshold %s" % (data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

5. 添加名为 **module_twin_callback** 的新函数。 更新所需属性时，会调用此函数。

    ```python
    # module_twin_callback is invoked when the module twin's desired properties are updated.
    def module_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print ( "\nTwin callback called with:\nupdateStatus = %s\npayload = %s\ncontext = %s" % (update_state, payload, user_context) )
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print ( "Total calls confirmed: %d\n" % TWIN_CALLBACKS )
    ```

6. 在 **HubManager 类中**，向 **__init__** 方法添加新的一行，以初始化刚添加的 **module_twin_callback** 函数：

    ```python
    # Sets the callback when a module twin's desired properties are updated.
    self.client.set_module_twin_callback(module_twin_callback, self)
    ```

7. 保存此文件。

8. 在 VS Code 资源管理器中打开 **deployment.template.json** 文件。 

   此文件告知 **$edgeAgent** 部署两个模块：**tempSensor**，用于模拟设备数据，以及 **PythonModule**。 在 VS Code 状态栏中将 IoT Edge 的默认平台设置为 **amd64**，这意味着将 **PythonModule** 设置为映像的 Linux amd64 版本。 在状态栏中将默认平台从 **amd64** 更改为 **arm32v7** 或 **windows-amd64**（如果这就是 IoT Edge 设备的体系结构）。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

   此文件也包含注册表凭据。 在模板文件中，用户名和密码会使用占位符填充。 生成部署清单时，这些字段会被更新为添加到 .env 文件的值。 

9. 将 **PythonModule** 模块孪生添加到部署清单。 在 **moduleContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 

   ```json
       "PythonModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
   ```

   ![将模块孪生添加到部署模板](./media/tutorial-python-module/module-twin.png)

10. 保存此文件。

## <a name="build-and-push-your-solution"></a>生成并推送解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **PythonModule**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker。 然后可将模块映像推送到 Azure 容器注册表： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 也可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 Python 代码容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

可以使用在 VS Code 集成终端中运行的 `docker build` 命令查看具有标记的完整容器映像地址。 映像地址根据 module.json 文件中的信息生成，其格式为 \<存储库\>:\<版本\>-\<平台\>。 在本教程中，它应该类似于 registryname.azurecr.io/pythonmodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可以使用用于 Visual Studio Code 的 Azure IoT 中心工具包扩展（以前称为 Azure IoT 工具包扩展）来部署模块。 你已经为方案准备了部署清单，即 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。 

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 

3. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

4. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

   ![为单个设备创建部署](./media/tutorial-python-module/create-deployment.png)

5. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。 不要使用 deployment.template.json 文件。 

6. 单击“刷新”按钮。 此时会看到新的 **PythonModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。 

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。 

在 IoT Edge 设备本身上，可以使用 `iotedge list` 命令查看部署模块的状态。 应该看到四个模块：两个 IoT Edge 运行时模块、tempSensor 以及在本教程中创建的自定义模块。 启动所有模块可能需要数分钟，因此如果一开始没有看到全部模块，请重新运行命令。 

若要查看由任何模块生成的消息，请使用 `iotedge logs <module name>` 命令。 

可以使用 Visual Studio Code 在消息到达 IoT 中心时查看它们。 

1. 若要监视抵达 IoT 中心的数据，请选择省略号 (**...**)，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT Hub: Stop monitoring D2C message”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，请安装适用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 可在 Docker 资源管理器中本地查找正在运行的模块。 在上下文菜单中单击“显示日志”，在集成终端中进行查看。 

## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

### <a name="delete-local-resources"></a>删除本地资源

若要从设备中删除 IoT Edge 运行时和相关的资源，请使用以下命令。 

删除 IoT Edge 运行时。

   ```bash
   sudo apt-get remove --purge iotedge
   ```

删除 IoT Edge 运行时以后，已创建的容器会被停止，但仍存在于设备上。 查看所有容器。

   ```bash
   sudo docker ps -a
   ```

删除在设备上创建的运行时容器。

   ```bash
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```

按照容器名称删除在 `docker ps` 输出中列出的任何其他容器。 

删除容器运行时。

   ```bash
   sudo apt-get remove --purge moby
   ```

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [将 Azure 函数部署为模块](tutorial-deploy-function.md)
> [将 Azure 流分析部署为模块](tutorial-deploy-stream-analytics.md)
