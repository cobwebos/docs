---
title: 开发和调试 Azure IoT Edge 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发、生成和调试使用 C#、Python、Node.js、Java 或 C 的 Azure IoT Edge 模块
services: iot-edge
keywords: ''
author: shizn
ms.author: xshi
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 5b37ea92869468001581c9299b1633869671886a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457077"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 开发和调试 Azure IoT Edge 模块

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文展示了如何使用 Visual Studio Code 作为主要工具来开发和调试模块。

There are two ways to debug modules written in C#, Node.js, or Java in Visual Studio Code: You can either attach a process in a module container or launch the module code in debug mode. To debug modules written in Python or C, you can only attach to a process in Linux amd64 containers.

如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[Debugging](https://code.visualstudio.com/Docs/editor/debugging)（调试）的信息。

This article provides instructions for developing and debugging modules in multiple languages for multiple architectures. Currently, Visual Studio Code provides support for modules written in C#, C, Python, Node.js, and Java. The supported device architectures are X64 and ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).

>[!NOTE]
>Develop and debugging support for Linux ARM64 devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 有关详细信息，请参阅[在 Visual Studio Code（预览版）中开发和调试 ARM64 IoT Edge 模块](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)。

## <a name="prerequisites"></a>必备组件

可以使用运行 Windows、macOS 或 Linux 的计算机或虚拟机作为开发计算机。 On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

首先安装 [Visual Studio Code](https://code.visualstudio.com/)，然后添加以下扩展：

- [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- 特定于你正在开发的语言的 Visual Studio 扩展：
  - C#, including Azure Functions: [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
  - Python: [Python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++ extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

You'll also need to install some additional, language-specific tools to develop your module:

- C#，包括 Azure Functions：[.NET Core 2.1 SDK](https://www.microsoft.com/net/download)

- Python: [Python](https://www.python.org/downloads/) and [Pip](https://pip.pypa.io/en/stable/installing/#installation) for installing Python packages (typically included with your Python installation).

- Node.js: [Node.js](https://nodejs.org). 还需要安装 [Yeoman](https://www.npmjs.com/package/yo) 和 [Azure IoT Edge Node.js 模块生成器](https://www.npmjs.com/package/generator-azure-iot-edge-module)。

- Java: [Java SE Development Kit 10](https://aka.ms/azure-jdks) and [Maven](https://maven.apache.org/). 需要[设置`JAVA_HOME`环境变量](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)，使其指向 JDK 安装项目。

To build and deploy your module image, you need Docker to build the module image and a container registry to hold the module image:

- 开发计算机上的 [Docker Community Edition](https://docs.docker.com/install/)。

- [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

除非使用 C 开发模块，否则还需要基于 Python 的 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，以便设置本地开发环境以调试、运行和测试 IoT Edge 解决方案。 If you haven't already done so, install [Python (2.7/3.6/3.7) and Pip](https://www.python.org/) and then install **iotedgehubdev** by running this command in your terminal.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
> [!NOTE]
> Currently, iotedgehubdev uses a docker-py library that is not compatible with Python 3.8.
>
> If you have multiple Python including pre-installed python 2.7 (for example, on Ubuntu or macOS), make sure you are using the correct `pip` or `pip3` to install **iotedgehubdev**

若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要先停止 EdgeHub 和 EdgeAgent，然后再继续下一步。

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

以下步骤说明如何使用 Visual Studio Code 和 Azure IoT Tools 以首选开发语言（包括使用 C# 编写的 Azure Functions）创建 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。

1. 选择“视图” > “命令面板”。

1. 在“命令面板”中，输入并运行“Azure IoT Edge: New IoT Edge Solution”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

1. 浏览到要在其中创建新解决方案的文件夹，然后选择“选择文件夹”。

1. 输入解决方案的名称。

1. 选择首选开发语言的模块模板作为解决方案中的第一个模块。

1. 输入模块的名称。 选择容器注册表中唯一的名称。

1. 提供模块的映像存储库的名称。 Visual Studio Code 使用“localhost:5000/<你的模块名称\>”自动填充模块名。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** .

   ![提供 Docker 映像存储库](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

该解决方案中有四个项目：

- 一个 .vscode 文件夹，包含调试配置。

- 一个 modules 文件夹，包含每个模块的子文件夹。  Within the folder for each module there is a file, **module.json**, that controls how modules are built and deployed.  This file would need to be modified to change the module deployment container registry from localhost to a remote registry. At this point, you only have one module.  但是可以在命令面板中使用“Azure IoT Edge: Add IoT Edge Module”命令添加更多模块。

- 一个 .env 文件，列出环境变量。 如果 Azure 容器注册表是注册表，则其中将包含 Azure 容器注册表用户名和密码。

  > [!NOTE]
  > 仅当为模块提供了映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。

- A **deployment.template.json** file lists your new module along with a sample **SimulatedTemperatureSensor** module that simulates data you can use for testing. 有关部署清单如何工作的详细信息，请参阅[了解如何使用部署清单部署模块和建立路由](module-composition.md)。

## <a name="add-additional-modules"></a>添加其他模块

To add additional modules to your solution, run the command **Azure IoT Edge: Add IoT Edge Module** from the command palette. 也可以右键单击 Visual Studio Code 资源管理器视图中的“模块”文件夹或 `deployment.template.json` 文件，然后选择“添加 IoT Edge 模块”。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认 C# 模块代码位于以下位置：

- Azure Function (C#)：modules > &lt;你的模块名称&gt; > &lt;你的模块名称&gt;.cs
- C#：modules > &lt;你的模块名称&gt; > Program.cs
- Python：modules > &lt;你的模块名称&gt; > main.py
- Node.js：modules > &lt;你的模块名称&gt; > app.js
- Java：modules > &lt;你的模块名称&gt; > src > main > java > com > edgemodulemodules > App.java
- C：modules > &lt;你的模块名称&gt; > main.c

设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试而无需触及任何代码。 The module is built to simply take input from a source (in this case, the SimulatedTemperatureSensor module that simulates data) and pipe it to IoT Hub.

当你准备使用自己的代码自定义模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>调试没有容器的模块 (C#、Node.js、Java)

如果使用 C#、Node.js 或 Java 进行开发，则模块需要在默认模块代码中使用“ModuleClient”对象，以便它可以启动、运行和路由消息。 还将使用默认输入通道“input1”在模块接收消息时执行操作。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>为 IoT Edge 解决方案设置 IoT Edge 模拟器

在开发计算机中，可以启动 IoT Edge 模拟器（而不是安装 IoT Edge 安全守护程序）以运行 IoT Edge 解决方案。

1. 在左侧的设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“设置 IoT Edge 模拟器”以使用设备连接字符串启动模拟器。
1. 通过读取集成终端中的进度详细信息，可以看到 IoT Edge 模拟器已成功设置。

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>为单个模块应用设置 IoT Edge 模拟器

To set up and start the simulator, run the command **Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** from the Visual Studio Code command palette. 出现提示时，使用默认模块代码中的值“input1”（或代码中的等效值）作为应用程序的输入名称。 该命令触发“iotedgehubdev”CLI，然后启动 IoT Edge 模拟器并测试实用程序模块容器。 如果模拟器已成功以单模块模式启动，则可以在集成终端中看到下面的输出。 还可以看到 `curl` 命令以帮助发送消息。 稍后将使用它。

   ![为单个模块应用设置 IoT Edge 模拟器](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   可以使用 Visual Studio Code 中的 Docker 资源管理视图来查看模块的运行状态。

   ![模拟器模块状态](media/how-to-develop-csharp-module/simulator-status.png)

   edgeHubDev 容器是本地 IoT Edge 模拟器的核心。 它可以在没有 IoT Edge 安全守护程序的情况下在开发计算机上运行，并为本机模块应用或模块容器提供环境设置。 “input”容器公开 REST API 以帮助将消息桥接到模块上的目标输入通道。

### <a name="debug-module-in-launch-mode"></a>在启动模式下调试模块

1. 根据开发语言的要求准备环境以进行调试，在模块中设置断点，并选择要使用的调试配置：
   - **C#**
     - In the Visual Studio Code integrated terminal, change the directory to the ***&lt;your module name&gt;*** folder, and then run the following command to build .NET Core application.

       ```cmd
       dotnet build
       ```

     - 打开 `Program.cs` 文件并添加断点。

     - 通过选择“视图”>“调试”以导航到 Visual Studio Code 调试视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(.NET Core)”。

        > [!NOTE]
        > If your .NET Core `TargetFramework` is not consistent with your program path in `launch.json`, you'll need to manually update the program path in `launch.json` to match the `TargetFramework` in your .csproj file so that Visual Studio Code can successfully launch this program.

   - **Node.js**
     - 在 Visual Studio Code 集成终端中，将目录更改为“&lt;你的模块名称&gt;”文件夹，然后运行以下命令以安装节点包

       ```cmd
       npm install
       ```

     - 打开 `app.js` 文件并添加断点。

     - 通过选择“视图”>“调试”以导航到 Visual Studio Code 调试视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(Node.js)”。
   - **Java**
     - 打开 `App.java` 文件并添加断点。

     - 通过选择“视图”>“调试”以导航到 Visual Studio Code 调试视图。 从下拉列表中选择调试配置“&lt;你的模块名称&gt; 本地调试(Java)”。

1. 单击“开始调试”或按“F5”开始调试会话。

1. 在 Visual Studio Code 集成终端中，运行以下命令向模块发送“Hello World”消息。 这是在前面的步骤中设置 IoT Edge 模拟器时所显示的命令。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > 如果使用的是 Windows，请确保 Visual Studio Code 集成终端的 shell 为 Git Bash 或 WSL Bash。 无法从 PowerShell 或命令提示符运行 `curl` 命令。
   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而不是 `curl`）来发送消息。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

1. To stop your debugging session, select the Stop button or press **Shift + F5**, and then run **Azure IoT Edge: Stop IoT Edge Simulator** in the command palette to stop the simulator and clean up.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>使用 IoT Edge 模拟器在附加模式下进行调试 (C#、Node.js、Java、Azure Functions)

默认解决方案包含两个模块，一个是模拟的温度传感器模块，另一个是管道模块。 模拟的温度传感器向管道模块发送消息，然后将消息通过管道传送到 IoT 中心。 在创建的模块文件夹中，有适用于不同容器类型的多个 Docker 文件。 使用以扩展名“.debug”结尾的任何文件来生成用于测试的模块。

目前，仅支持以附加模式进行调试，如下所示：

- C# 模块（包括 Azure Functions 的模块）支持在 Linux amd64 容器进行调试
- Node.js 模块支持在 Linux amd64、arm32v7 容器以及 Windows amd64 容器中进行调试
- Java 模块支持在 Linux amd64 和 arm32v7 容器中进行调试

> [!TIP]
> 可以通过单击 Visual Studio 代码状态栏中的项目来切换 IoT Edge 解决方案的默认平台的选项。

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>为 IoT Edge 解决方案设置 IoT Edge 模拟器

在开发计算机中，可以启动 IoT Edge 模拟器（而不是安装 IoT Edge 安全守护程序）以运行 IoT Edge 解决方案。

1. 在左侧的设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“设置 IoT Edge 模拟器”以使用设备连接字符串启动模拟器。

1. 通过读取集成终端中的进度详细信息，可以看到 IoT Edge 模拟器已成功设置。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>生成和运行用于调试的容器并在附加模式下进行调试

1. 打开模块文件 (`Program.cs`、`app.js``App.java` 或 `<your module name>.cs`) 并添加断点。

1. 在 Visual Studio Code 资源管理器视图中，右键单击解决方案的 `deployment.debug.template.json` 文件，然后选择“在模拟器中构建并运行 IoT Edge 解决方案”。 可以在同一窗口中监视所有模块容器日志。 还可以导航到 Docker 视图以监视容器状态。

   ![监视变量](media/how-to-vs-code-develop-module/view-log.png)

1. 导航至 Visual Studio Code 调试视图，并选择模块的调试配置文件。 调试选项名称应类似于“&lt;你的模块名称&gt; 远程调试”

1. 选择“开始调试”或按“F5”。 选择要附加到的进程。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

1. To stop the debugging session, first select the Stop button or press **Shift + F5**, and then select **Azure IoT Edge: Stop IoT Edge Simulator** from the command palette.

> [!NOTE]
> 上面的示例展示了如何调试容器上的 IoT Edge 模块。 它为模块的容器 `createOptions` 设置添加了公开的端口。 完成模块的调试后，建议为可用于生产的 IoT Edge 模块删除那些公开的端口。
>
> 对于使用 C# 编写的模块（包括 Azure Functions），此示例基于 `Dockerfile.amd64.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 .NET Core 命令行调试器 (VSDBG)。 对于生产就绪的 IoT Edge 模块，建议在调试完 C# 模块后在不使用 VSDBG 的情况下直接使用 Dockerfile。

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>使用 IoT Edge 运行时调试模块

每个模块文件夹中都有多个适用于不同容器类型的 Docker 文件。 使用以扩展名“.debug”结尾的任何文件来生成用于测试的模块。

使用此方法运行时调试模块时，模块在 IoT Edge 运行时之上运行。 IoT Edge 设备和 Visual Studio Code 可以位于同一台计算机中；也有更常见的做法，Visual Studio Code 位于开发计算机中，IoT Edge 运行时和模块要在另一台物理计算机中运行。 若要从 Visual Studio Code 进行调试，必须执行以下操作：

- 设置 IoT Edge 设备，使用 .debug Dockerfile 生成 IoT Edge 模块，然后将其部署到 IoT Edge 设备。
- 公开模块的 IP 和端口以供调试程序连接。
- 更新 `launch.json`，以便 Visual Studio Code 可以附加到远程计算机中的容器中的进程。 此文件位于工作区中的 `.vscode` 文件夹中，并且每一次添加支持调试的新模块时都会更新。

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>生成和部署模块到 IoT Edge 设备

1. 在 Visual Studio 代码中，打开 `deployment.debug.template.json` 文件，其中包含模块映像的调试版本，并设置了正确的 `createOptions` 值。

1. 如果正在使用 Python 开发模块，请在继续之前按照以下步骤操作：
   - 打开文件 `main.py` 并在导入部分后添加此代码：

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - 将以下单行代码添加到要调试的回叫中：

      ```python
      ptvsd.break_into_debugger()
      ```

     For example, if you want to debug the `receive_message_listener` function, you would insert that line of code as shown below:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. 在 Visual Studio Code 命令面板中：
   1. Run the command **Azure IoT Edge: Build and Push IoT Edge solution**.

   1. 选择解决方案的 `deployment.debug.template.json` 文件。

1. 在 Visual Studio Code 资源管理器视图的“Azure IoT 中心设备”部分中：
   1. 右键单击 IoT Edge 设备 ID，然后选择“为单个设备创建部署”。

      > [!TIP]
      > 若要确认已选择的设备为 IoT Edge 设备，请选择它以展开模块列表并验证是否存在“$ edgeHub”和“$ edgeAgent”。 每个 IoT Edge 设备都包含这两个模块。

   1. 导航到解决方案的“config”文件夹，选择 `deployment.debug.amd64.json` 文件，然后选择“选择 Edge 部署清单”。

将在集成终端中看到已成功创建部署且具有一个部署 ID。

可以通过在终端中运行 `docker ps` 命令来检查容器状态。 如果 Visual Studio Code 和 IoT Edge 运行时在同一台计算机上运行，则还可以在 Visual Studio Code Docker 视图中检查状态。

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>为调试程序公开模块的 IP 和端口

如果模块和 Visual Studio Code 在同一计算机上运行，则可以跳过此部分，因为要使用 localhost 连接到容器，并且 .debug Dockerfile、模块的容器 `createOptions` 设置和 `launch.json` 文件中已具有正确的端口设置。 如果模块和 Visual Studio Code 在不同的计算机上运行，请按照开发语言的步骤操作。

- **C#，包括 Azure Functions**

  [在开发计算机和 IoT Edge 设备](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes)上配置 SSH 通道，然后编辑要附加的 `launch.json` 文件。

- **Node.js**

  - 请确保计算机上要调试的模块正在运行并可供调试程序附加，且可以从外部访问端口 9229。 可以通过在调试程序计算机上打开 `http://<target-machine-IP>:9229/json` 对此进行验证。 此 URL 应显示有关要调试的 Node.js 模块的信息。
  
  - 在开发计算机上打开 Visual Studio Code，然后编辑 `launch.json`，以便&lt;模块名称&gt;远程调试 (Node.js) 配置文件（如果模块作为 Windows 容器运行，则为&lt;模块名称&gt;远程调试（Windows 容器中的 Node.js）配置文件）的地址值是被调试计算机的 IP。

- **Java**

  - 通过运行 `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` 将 SSH 隧道生成到要调试的计算机。
  
  - 在开发计算机上打开 Visual Studio Code，然后编辑 `launch.json` 中的&lt;模块名称&gt;远程调试 (Java) 配置文件，以便连接到目标计算机。 若要了解如何编辑 `launch.json` 和使用 Visual Studio Code 调试 Java 的详细信息，请参阅[配置调试程序](https://code.visualstudio.com/docs/java/java-debugging#_configuration)中的部分。

- **Python**

  - 确保要调试的计算机上的 5678 端口已打开并且可访问。

  - 在之前插入 `main.py` 中的代码 `ptvsd.enable_attach(('0.0.0.0', 5678))` 中，将“0.0.0.0”更改为要调试的计算机的 IP 地址。 再次生成、推送和部署 IoT Edge 模块。

  - 在开发计算机中打开 Visual Studio Code，然后编辑 `launch.json`，以便&lt;模块名称&gt;远程调试 (Python) 配置文件的 `host` 值使用目标计算机的 IP 地址（而不是 `localhost`）。

### <a name="debug-your-module"></a>调试模块

1. 在 Visual Studio Code 调试视图中，选择模块的调试配置文件。 调试选项名称应类似于“&lt;你的模块名称&gt; 远程调试”

1. 打开开发语言的模块文件并添加断点：

   - **Azure Function (C#)** : Add your breakpoint to the file `<your module name>.cs`.
   - **C#** : Add your breakpoint to the file `Program.cs`.
   - **Node.js**: Add your breakpoint to the file `app.js`.
   - **Java**: Add your breakpoint to the file `App.java`.
   - **Python**: Add your breakpoint to the file `main.py`in the callback method where you added the `ptvsd.break_into_debugger()` line.
   - **C**: Add your breakpoint to the file `main.c`.

1. 选择“开始调试”或选择 F5。 选择要附加到的进程。

1. 在 Visual Studio Code 调试视图中，将在左侧面板中看到变量。

> [!NOTE]
> 上面的示例展示了如何调试容器上的 IoT Edge 模块。 它为模块的容器 `createOptions` 设置添加了公开的端口。 完成模块的调试后，建议为可用于生产的 IoT Edge 模块删除那些公开的端口。

## <a name="build-and-debug-a-module-remotely"></a>远程生成和调试模块

凭借近期为支持 SSH 连接而对 Docker 和 Moby 引擎所做的更改，以及 Azure IoT 工具（可以将环境设置注入 Visual Studio Code 命令面板和 Azure IoT Edge 终端）中的新设置，现在可以在远程设备上生成和调试模块。

请参阅此 [IoT 开发人员博客文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)获取详细信息和分步说明。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)。

若要开发用于 IoT Edge 设备的模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
