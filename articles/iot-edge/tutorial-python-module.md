---
title: Azure IoT Edge Python 模块 | Microsoft Docs
description: 使用 Python 代码创建 IoT Edge 模块，并将它部署到边缘设备
author: shizn
manager: ''
ms.author: xshi
ms.date: 03/18/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 88d772306cb9e67216b380aa885284ebedc77b5f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632102"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>开发 Python IoT Edge 模块，并将它部署到模拟设备 - 预览

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 IoT Edge Python 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

> [!IMPORTANT]
> 目前 Python 模块只能在 amd64 Linux 容器中运行，无法在 Windows 容器或基于 ARM 的容器中运行。 

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或第一个教程创建 Azure IoT Edge 设备。
* IoT Edge 设备的主键连接字符串。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* Visual Studio Code 所在计算机上的 [Docker](https://docs.docker.com/engine/installation/)。 对于本教程而言，使用社区版 (CE) 即可。 
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用于安装 Python 包（通常包含在 Python 安装中）。

## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “Azure 容器注册表”。
2. 为注册表提供一个名称，选择一个订阅，选择一个资源组，然后将 SKU 设置为“基本”。 
3. 选择**创建**。
4. 创建容器注册表以后，导航到其中，然后选择“访问键”。 
5. 将“管理员用户”切换到“启用”。
6. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建 IoT Edge Python 模块。
1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
2. 在集成终端中，输入以下命令以安装（或更新）cookiecutter（我们建议在虚拟环境中或作为用户安装执行此操作，如下所示）：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. 为新模块创建一个项目。 以下命令通过容器存储库创建项目文件夹 FilterModule。 如果使用 Azure 容器注册表，`image_repository` 的参数应采用 `<your container registry name>.azurecr.io/filtermodule` 的形式。 在当前的工作文件夹中输入以下命令：

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. 选择“文件” > “打开文件夹”。
5. 浏览到“FilterModule”文件夹，然后单击“选择文件夹”，在 VS Code 中打开此项目。
6. 在 VS Code 资源管理器中，单击“main.py”将其打开。
7. 在“FilterModule”命名空间的顶部，导入 `json` 库：

    ```python
    import json
    ```

8. 在全局计数器下添加 `TEMPERATURE_THRESHOLD`、`RECEIVE_CALLBACKS` 和 `TWIN_CALLBACKS`。 温度阈值设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. 使用以下内容更新 `receive_message_callback` 函数。

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. 添加新的 `device_twin_callback` 函数。 更新所需属性时，将调用此函数。

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. 在 `HubManager` 类中，向 `__init__` 方法添加新的一行，以初始化刚添加的 `device_twin_callback` 函数。

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. 保存此文件。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 在 VS Code 集成终端输入以下命令，登录到 Docker： 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   使用用户名、密码以及在创建时从 Azure 容器注册表复制的登录服务器。

2. 在 VS Code 资源管理器中，右键单击 module.json 文件，然后单击“生成和推送 IoT Edge 模块 Docker 映像”。 在 VS Code 窗口顶部的弹出下拉框中，选择容器平台，例如，为 Linux 容器选择“amd64”。 VS Code 将 `main.py` 及所需依赖关系容器化并推送到指定的容器注册表。 首次生成映像时，可能需要几分钟时间。

3. 可在 VS Code 集成终端中获取具有标记的完整容器映像地址。 有关生成和推送定义的详细信息，可参考 `module.json` 文件。

## <a name="add-registry-credentials-to-edge-runtime"></a>将注册表凭据添加到 Edge 运行时
在运行 Edge 设备的计算机上将注册表凭据添加到 Edge 运行时。 这些凭据为此运行时提供拉取容器的访问权限。 

- 在 Windows 上，运行以下命令：
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- 在 Linux 上，运行以下命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>运行解决方案

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
2. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
3. 选择“设置模块”。 
2. 检查 **tempSensor** 模块是否已自动填充。 否则，请使用以下步骤来添加该模块：
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 将其他设置保留不变，然后单击“保存”。
9. 添加在前面部分创建的 **filterModule** 模块。 
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `filterModule`。
    3. 在“映像 URI”字段中，输入映像地址；例如 `<your container registry address>/filtermodule:0.0.1-amd64`。 可从上一节中找到完整映像地址。
    4. 勾选“启用”框，这样就能编辑孪生模块。 
    5. 将文本框中用于模块孪生的 JSON 替换为以下 JSON： 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. 单击“ **保存**”。
10. 单击“资源组名称” 的 Azure 数据工厂。
11. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 这些模块会将所有消息发布到 Edge 运行时。 此运行时中的声明性规则将定义消息流经的位置。 本教程需要两个路由。 第一个路由会通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，`upstream` 是特殊目的地，它将告诉 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. 单击“资源组名称” 的 Azure 数据工厂。
5. 在“审阅模板”步骤中，单击“提交”。 
6. 返回到“IoT Edge 设备详细信息”页，并单击“刷新”。 应看到新的 filter 模块与 tempSensor 模块和 IoT Edge 运行时在同时运行。 

## <a name="view-generated-data"></a>查看生成的数据

监视从 IoT Edge 设备发送到 IoT 中心的“设备到云”消息：
1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 选择“视图” > “资源管理器”，打开 VS Code 资源管理器。 
    3. 在资源管理器中，单击“IOT 中心设备”，然后单击“...”。单击“设置 IoT 中心连接字符串”，然后在弹出窗口中输入 IoT Edge 设备所连接的 IoT 中心的连接字符串。 

        要找到此连接字符串，请在 Azure 门户中单击 IoT 中心的磁贴，然后单击“共享访问策略”。 在“共享访问策略”中，单击“iothubowner”策略，并复制“iothubowner”窗口中的 IoT 中心连接字符串。   

1. 若要监视到达 IoT 中心的数据，请选择“视图” > “命令面板”，然后搜索“IoT: 开始监视 D2C 消息”菜单命令。 
2. 若要停止监视数据，请使用“IoT: 停止监视 D2C 消息”菜单命令。 

## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 可以继续查看以下任一教程，了解 Azure IoT Edge 有助于将数据转化为边缘业务洞察力的其他方式。

> [!div class="nextstepaction"]
> [将 Azure 函数部署为模块](tutorial-deploy-function.md)
> [将 Azure 流分析部署为模块](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
