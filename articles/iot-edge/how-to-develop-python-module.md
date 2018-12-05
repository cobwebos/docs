---
title: 调试 Azure IoT Edge 的 Python 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发、生成和调试 Azure IoT Edge 的 Python 模块
services: iot-edge
keywords: ''
author: shizn
manager: philmea
ms.author: xshi
ms.date: 09/13/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: c3cf2b703760debb368e26d629ee73f56ce93d39
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441242"
---
# <a name="use-visual-studio-code-to-develop-and-debug-python-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 开发和调试 Azure IoT Edge 的 Python 模块

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文展示了如何使用 Visual Studio Code (VS Code) 作为主要工具来开发和调试 Python 模块。

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 使用 IoT Edge 安全守护程序在开发计算机上模拟 IoT Edge 设备。

> [!NOTE]
> 本调试文章演示了如何在模块容器中附加进程并使用 VS Code 对其进行调试。 只能调试 Linux amd64 容器中的 Python 模块。 如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[Debugging](https://code.visualstudio.com/Docs/editor/debugging)（调试）的信息。 

因为本文使用 Visual Studio Code 作为主要开发工具，所以请安装 VS Code。 然后添加必要的扩展：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* 适用于 Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。 
* [Python](https://www.python.org/downloads/)。
* [Pip](https://pip.pypa.io/en/stable/installing/#installation) 用于安装 Python 包（通常包含在 Python 安装中）。
* 使用以下命令安装 Cookiecutter
   
    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

若要创建模块，需使用 Docker 生成模块映像，并使用容器注册表来保存模块映像：
* 开发计算机上的 [Docker Community Edition](https://docs.docker.com/install/)。 
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。 

若要在设备上测试模块，你至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 快速入门中的步骤进行操作。 

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

请执行以下步骤，使用 Visual Studio Code 和 Azure IoT Edge 扩展创建基于 Azure IoT Python SDK 的 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。 

1. 在 Visual Studio Code 中，选择“视图” > “集成终端”。

2. 选择“视图” > “命令面板”。 

3. 在“命令面板”中，输入并运行“Azure IoT Edge: New IoT Edge Solution”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

4. 浏览到要创建新解决方案的文件夹。 选择“选择文件夹”。 

5. 输入解决方案的名称。 

6. 选择“Python 模块”作为解决方案中第一个模块的模板。

7. 输入模块的名称。 选择容器注册表中唯一的名称。 

8. 提供模块的映像存储库的名称。 VS Code 使用 localhost:5000 自动填充模块名。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<registry name\>.azurecr.io。 仅替换字符串的 localhost 部分，不要删除模块名。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/\<modulename\>。

   ![提供 Docker 映像存储库](./media/how-to-develop-c-module/repository.png)

VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

该解决方案中有四个项： 
* 一个 .vscode 文件夹，包含调试配置。
* 一个 modules 文件夹，包含每个模块的子文件夹。 现在，只有一个模块。 但是可以在命令面板中使用“Azure IoT Edge: Add IoT Edge Module”命令添加更多模块。 
* 一个 .env 文件，列出环境变量。 如果 Azure 容器注册表是注册表，则其中将包含 Azure 容器注册表用户名和密码。 

   > [!NOTE]
   > 仅当为模块提供了映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。 

* 一个 deployment.template.json 文件，列出新模块以及模拟可用于测试的数据的示例 tempSensor 模块。 有关部署清单如何工作的详细信息，请参阅[了解如何使用部署清单部署模块和建立路由](module-composition.md)。 
* deployment.debug.template.json 文件包含具有适当容器选项的模块映像的调试版本。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认 Python 模块代码位于“模块”> [你的模块名称] >“main.py”。 设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试而无需触及任何代码。 生成该模块仅为简单从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 IoT 中心。 

准备好使用自己的代码自定义 Python 模板时，请使用 [Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。 

## <a name="build-and-deploy-your-module-for-debugging"></a>生成并部署模块以便调试

每个模块文件夹中都有多个适用于不同容器类型的 Docker 文件。 使用以扩展名 .debug 结尾的任何文件来生成用于测试的模块。 目前，Python 模块仅支持在 Linux amd64 容器中进行调试。 

1. 在 VS Code 中，导航到 `deployment.debug.template.json` 文件。 此文件包含具有正确创建选项的模块映像的调试版本。 

3. 导航到 `main.py`，在导入部分后添加以下代码
    
    ```python
    import ptvsd
    ptvsd.enable_attach(('0.0.0.0',  5678))
    ```

4. 将以下单行代码添加到要调试的回叫中

    ```python
    ptvsd.break_into_debugger()
    ```

   例如，如果想要调试 `receive_message_callback` 方法。 可插入单行代码，如下所示。

    ```python
    def receive_message_callback(message, hubManager):
        ptvsd.break_into_debugger()
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

2. 在 VS Code 命令面板中，输入并运行“Azure IoT Edge: Build and Push IoT Edge solution”命令。
3. 从命令面板中选择解决方案的 `deployment.debug.template.json` 文件。 
4. 在 Azure IoT 中心 Device Explorer 中，右键单击某个 IoT Edge 设备 ID。 然后，选择“为单个设备创建部署”。 
5. 打开解决方案的 config 文件夹。 然后选择 `deployment.debug.amd64.json` 文件。 选择“选择 Edge 部署清单”。 

将在 VS Code 集成终端中看到已成功创建部署且具有一个部署 ID。

在 VS Code Docker 资源管理器中检查容器状态，也可通过在终端运行 `docker ps` 命令进行检查。

## <a name="start-debugging-python-module-in-vs-code"></a>开始在 VS Code 中调试 Python 模块
VS Code 将调试配置信息保存在 `launch.json` 文件中，该文件位于工作区的 `.vscode` 文件夹中。 创建新的 IoT Edge 解决方案时就会生成此 `launch.json` 文件。 每次添加支持调试的新模块时，它都会随之更新。 

1. 导航到 VS Code 调试视图。 选择模块的调试配置文件。 调试选项名应类似于“ModuleName 远程调试 (Python)”

2. 导航到 `main.c`。 在已放入 `ptvsd.break_into_debugger()` 的回叫方法中添加一个断点。

3. 选择“开始调试”或选择 F5。 选择要附加到的进程。

4. 在 VS Code 调试视图中，将在左侧面板中看到变量。 

上面的示例展示了如何调试容器上的 Python IoT Edge 模块。 它在模块容器 createOptions 中添加了公开的端口。 调试 Python 模块完成后，建议为生产就绪的 IoT Edge 模块删除这些公开的端口。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)。

若要开发用于 IoT Edge 设备的模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
