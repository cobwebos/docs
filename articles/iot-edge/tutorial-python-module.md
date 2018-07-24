---
title: Azure IoT Edge Python 教程 | Microsoft Docs
description: 本教程介绍如何使用 Python 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d56fccbb378736dc8235bf8b8f17afffc085c49f
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002001"
---
# <a name="tutorial-develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device"></a>教程：开发 Python IoT Edge 模块并将其部署到模拟设备

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows][lnk-quickstart-win] 或 [Linux][lnk-quickstart-lin] 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行以下操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 IoT Edge Python 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。


## <a name="prerequisites"></a>先决条件

* 在用于 [Linux](quickstart-linux.md) 的快速入门中创建的 Azure IoT Edge 设备。

   >[!Note]
   >用于 Azure IoT edge 的 Python 模块不支持 Windows 或 ARM 设备。 

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
4. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 
5. 将“管理员用户”切换到“启用”。
6. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤使用 Visual Studio Code 和 Azure IoT Edge 扩展创建 IoT Edge Python 模块。

### <a name="create-a-new-solution"></a>创建新的解决方案

使用 Python 包 **cookiecutter** 创建一个 Python 解决方案模板，以便在其上生成项目。 

1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。

2. 在集成终端中输入以下命令，以便安装（或更新）用于在 VS Code 中创建 IoT Edge 解决方案模板的 **cookiecuttere**：

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. 选择“视图” > “命令面板”，打开 VS Code 命令面板。 

4. 在命令面板中，输入并运行“Azure: 登录”命令，然后按说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

5. 在命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 在命令面板中提供以下信息，以便创建解决方案： 

   1. 选择要在其中创建解决方案的文件夹。 
   2. 提供解决方案的名称，或者接受默认的 **EdgeSolution**。
   3. 选择“Python 模块”作为模块模板。 
   4. 将模块命名为 **PythonModule**。 
   5. 将在上一部分创建的 Azure 容器注册表指定为第一个模块的映像存储库。 将 **localhost:5000** 替换为复制的登录服务器值。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/pythonmodule。
 
VS Code 窗口将加载你的 IoT Edge 解决方案工作区：modules 文件夹、一个部署清单模板文件，以及一个 \.env 文件。 

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

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **PythonModule**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker。 然后可将模块映像推送到 Azure 容器注册表： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 也可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 deployment.template.json 文件。 

   此文件告知 **$edgeAgent** 部署两个模块：**tempSensor**，用于模拟设备数据，以及 **PythonModule**。 **PythonModule.image** 值设置为映像的 Linux amd64 版本。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

   此文件也包含注册表凭据。 在模板文件中，用户名和密码会使用占位符填充。 生成部署清单时，这些字段会被更新为添加到 .env 文件的值。 

3. 将 **PythonModule** 模块孪生添加到部署清单。 在 **moduleContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 
    ```json
        "PythonModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 保存此文件。

5. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 Python 代码容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

可以使用在 VS Code 集成终端中运行的 `docker build` 命令查看具有标记的完整容器映像地址。 映像地址根据 module.json 文件中的信息生成，其格式为 \<存储库\>:\<版本\>-\<平台\>。 在本教程中，它应该类似于 registryname.azurecr.io/pythonmodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

可以使用 Azure 门户将 Python 模块部署到 IoT Edge 设备，就像在快速入门中所做的一样。 也可以在 Visual Studio Code 中部署和监视模块。 以下部分使用用于 VS Code 的 Azure IoT Edge 扩展，该扩展已在先决条件中列出。 如果尚未安装该扩展，现在请安装。 

1. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

2. 搜索并运行“Azure: 登录”命令。 按照说明登录 Azure 帐户。 

3. 在命令面板中，搜索并运行“Azure IoT 中心: 选择 IoT 中心”命令。 

4. 选择包含 IoT 中心的订阅，然后选择要访问的 IoT 中心。

5. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

6. 右键单击 IoT Edge 设备的名称，然后选择“为 IoT Edge 设备创建部署”。 

7. 浏览到包含 **PythonModule** 的解决方案文件夹。 打开 config 文件夹，选择 deployment.json 文件，然后选择“选择 Edge 部署清单”。

8. 刷新“Azure IoT 中心设备”部分。 此时会看到新的 **PythonModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视抵达 IoT 中心的数据，请选择省略号 (**...**)，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT 中心: 停止监视 D2C 消息”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，请安装适用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 可在 Docker 资源管理器中本地查找正在运行的模块。 在上下文菜单中单击“显示日志”，在集成终端中进行查看。 

## <a name="clean-up-resources"></a>清理资源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 删除这些项时，资源组以及其中包含的所有资源会被永久删除。 请确保不要意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，请只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请使用中心名称和资源组名称执行以下命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。

2. 在“按名称筛选”文本框中，输入包含你的 IoT 中心的资源组的名称。 

3. 在结果列表中你的资源组的右侧，选择 (**...**)，然后选择“删除资源组”。

4. 系统会要求确认是否删除资源组。 重新键入资源组的名称进行确认，然后选择“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [将 Azure 函数部署为模块](tutorial-deploy-function.md)
> [将 Azure 流分析部署为模块](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-quickstart-win]: quickstart.md
[lnk-quickstart-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
