---
title: Develop and debug modules in Visual Studio - Azure IoT Edge | Microsoft Docs
description: Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 2f8b0fe83e10beb3b65dca08e18b03f4fc11947e
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457109"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge"></a>Use Visual Studio 2019 to develop and debug modules for Azure IoT Edge

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 This article shows you how to use Visual Studio 2019 as the main tool to develop and debug modules.

用于 Visual Studio 的 Azure IoT Edge 工具提供以下优势：

- 在本地开发计算机上创建、编辑、构建、运行和调试 Azure IoT Edge 解决方案和模块。
- 通过 Azure IoT 中心将 Azure IoT Edge 解决方案部署到 Azure IoT Edge 设备。
- Code your Azure IoT modules in C or C# while having all of the benefits of Visual Studio development.
- 使用 UI 管理 Azure IoT Edge 设备和模块。

This article shows you how to use the Azure IoT Edge Tools for Visual Studio 2019 to develop your IoT Edge modules. 你还将了解如何将项目部署到 Azure IoT Edge 设备。 Currently, Visual Studio 2019 provides support for modules written in C and C#. The supported device architectures are Windows X64 and Linux X64 or ARM32. For more information about supported operating systems, languages, and architectures, see [Language and architecture support](module-development.md#language-and-architecture-support).
  
## <a name="prerequisites"></a>必备组件

本文假设你使用运行 Windows 的计算机或虚拟机作为开发计算机。 On Windows computers you can develop either Windows or Linux modules. To develop Windows modules, use a Windows computer running version 1809/build 17763 or newer. To develop Linux modules, use a Windows computer that meets the [requirements for Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). 

Because this article uses Visual Studio 2019 as the main development tool, install Visual Studio. Make sure you include the **Azure development** and **Desktop development with C++** workloads in your Visual Studio 2019 installation. You can [Modify Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019) to add the required workloads.

After your Visual Studio 2019 is ready, you also need the following tools and components:

- Download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) from the Visual Studio marketplace to create an IoT Edge project in Visual Studio 2019.

> [!TIP]
> If you are using Visual Studio 2017, please download and install [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) for VS 2017 from the Visual Studio marketplace

- 在开发计算机上下载并安装 [Docker 社区版](https://docs.docker.com/install/)，以生成和运行模块映像。 需要将 Docker CE 设置为在 Linux 容器模式或 Windows 容器模式下运行。

- 设置本地开发环境，通过安装 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，调试、运行和测试 IoT Edge 解决方案。 Install [Python (2.7/3.6+) and Pip](https://www.python.org/) and then install the **iotedgehubdev** package by running the following command in your terminal. 确保 Azure IoT EdgeHub 开发工具版本高于 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Clone the repository and install the Vcpkg library manager, and then install the **azure-iot-sdk-c package** for Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

- 若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要停止 EdgeHub 和 EdgeAgent，然后再在 Visual Studio 中开始开发。

### <a name="check-your-tools-version"></a>检查工具版本

1. 在“工具”菜单上，选择“扩展和更新”。 依次展开“已安装”>“工具”，可在此处找到“Azure IoT Edge 工具”和“Cloud Explorer for Visual Studio”。

1. 请记下所安装的版本。 可将此版本与 Visual Studio Marketplace 中的最新版本（[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)）进行比较

1. 如果你的版本比 Visual Studio Marketplace 上的版本旧，则可在 Visual Studio 中更新工具，如以下部分中所示。

### <a name="update-your-tools"></a>更新工具

1. 在“扩展和更新”对话框中，依次展开“更新”>“Visual Studio Marketplace”，选择“Azure IoT Edge 工具”或“Cloud Explorer for Visual Studio”，再选择“更新”。

1. 下载工具更新后，请关闭 Visual Studio 以触发使用 VSIX 安装程序的工具更新。

1. 在安装程序中，选择“确定”进行启动，然后选择“修改”以更新工具。

1. 更新完成后，请选择“关闭”并重启 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>创建 Azure IoT Edge 项目

Visual Studio 中的 Azure IoT Edge 项目模板创建了一个项目，它可部署到 Azure IoT 中心的 Azure IoT Edge 设备。 First you create an Azure IoT Edge solution, and then you generate the first module in that solution. 每一个 IoT Edge 解决方案都可包含多个模块。

> [!TIP]
> Visual Studio 创建的 IoT Edge 项目结构与 Visual Studio Code 中的不同。

1. In Visual Studio new project dialog, search and select **Azure IoT Edge** project and click **Next**. In project configuration window, enter a name for your project and specify the location, and then select **Create**. 默认的项目名称为 AzureIoTEdgeApp1。

   ![创建新项目](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. In the **Add IoT Edge Application and Module** window, select either **C# Module** or **C Module** and then specify your module name and module image repository. Visual Studio 使用“localhost:5000/<模块名\>自动填充模块名。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 The login server looks like **_\<registry name\>_ .azurecr.io**. Only replace the **localhost:5000** part of the string so that the final result looks like **\<*registry name*\>.azurecr.io/ _\<your module name\>_** . The default module name is **IotEdgeModule1**

   ![Add Application and Module](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Select **OK** to create the Azure IoT Edge solution with a module that uses either C# or C.

Now you have an **AzureIoTEdgeApp1.Linux.Amd64** project or an **AzureIoTEdgeApp1.Windows.Amd64** project, and also an **IotEdgeModule1** project in your solution. Each **AzureIoTEdgeApp1** project has a `deployment.template.json` file, which defines the modules you want to build and deploy for your IoT Edge solution, and also defines the routes between modules. The default solution has a **SimulatedTemperatureSensor** module and a **IotEdgeModule1** module. The **SimulatedTemperatureSensor** module generates simulated data to the **IotEdgeModule1** module, while the default code in the **IotEdgeModule1** module directly pipes received messages to Azure IoT Hub.

The **IotEdgeModule1** project is a .NET Core 2.1 console application if it's a C# module. 它包含 IoT Edge 设备通过 Windows 容器或 Linux 容器运行时所需的 Docker 文件。 The `module.json` file describes the metadata of a module. The actual module code, which takes Azure IoT Device SDK as a dependency, is found in the `Program.cs` or `main.c` file.

## <a name="develop-your-module"></a>开发模块

The default module code that comes with the solution is located at **IotEdgeModule1** > **Program.cs** (for C#) or **main.c** (C). The module and the `deployment.template.json` file are set up so that you can build the solution, push it to your container registry, and deploy it to a device to start testing without touching any code. The module is built to take input from a source (in this case, the **SimulatedTemperatureSensor** module that simulates data) and pipe it to Azure IoT Hub.

When you're ready to customize the module template with your own code, use the [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) to build modules that address the key needs for IoT solutions such as security, device management, and reliability.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 设备连接字符串初始化iotegehubdev

1. 从 Visual Studio Cloud Explorer 的“主连接字符串”中复制 IoT Edge 设备的连接字符串。 请勿复制非 Egde 设备的连接字符串，IoT Edge 设备的图标不同于非 Edge 设备的图标。

   ![复制 Edge 设备连接字符串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Go to **Tools** > **Azure IoT Edge Tools** > **Setup IoT Edge Simulator**, paste the connection string and click **OK**.

   ![打开设置 Edge 连接字符串窗口](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 输入第一步中获取的连接字符串，然后选择“确定”。

> [!NOTE]
> 只需在开发计算机上执行一次这些步骤，所得结果将自动应用于所有后续 Azure IoT Edge 解决方案。 如果需要更改为另一个连接字符串，可以再次执行此过程。

## <a name="build-and-debug-single-module"></a>Build and debug single module

通常，需要先测试和调试每个模块，然后再在具有多个模块的整个解决方案中运行此模块。

1. Right-click **IotEdgeModule1** and select **Set as StartUp Project** from the context menu.

   ![设置启动项目](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按 F5 或单击下方的按钮以运行模块，首次运行时可能耗时 10&ndash;20 秒。

   ![运行模块](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模块已成功初始化，你应会看到 .NET Core 控制台应用已启动。

   ![运行中的模块](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. If developing in C#, set a breakpoint in the `PipeMessage()` function in **Program.cs**; if using C, set a breakpoint in the `InputQueue1Callback()` function in **main.c**. You can then test it by sending a message by running the following command in a **Git Bash** or **WSL Bash** shell. （无法从 PowerShell 或命令提示符运行 `curl` 命令。）

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![调试单个模块](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    应触发断点。 可在 Visual Studio“局部变量”窗口中监视变量。

   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而非 `curl`）发送消息。

1. 按 Ctrl + F5 或单击“停止”按钮以停止调试。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>构建并调试具有多个模块的 IoT Edge 解决方案

开发完单个模块之后，需要运行并调试具有多个模块的整个解决方案。

1. Add a second module to the solution by right-clicking **AzureIoTEdgeApp1** and selecting **Add** > **New IoT Edge Module**. The default name of the second module is **IotEdgeModule2** and will act as another pipe module.

1. Open the file `deployment.template.json` and you'll see **IotEdgeModule2** has been added in the **modules** section. 将 routes 部分替换为以下内容。 如果自定义了模块名，请确保更新这些名称以匹配模块名。

    ```json
        "routes": {
          "IotEdgeModule1ToIoTHub": "FROM /messages/modules/IotEdgeModule1/outputs/* INTO $upstream",
          "sensorToIotEdgeModule1": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule1/inputs/input1\")",
          "IotEdgeModule2ToIoTHub": "FROM /messages/modules/IotEdgeModule2/outputs/* INTO $upstream",
          "sensorToIotEdgeModule2": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IotEdgeModule2/inputs/input1\")"
        },
    ```

1. 右键单击“AzureIoTEdgeApp1”并从上下文菜单中选择“设置为启动项目”。

1. 创建断点，然后按 F5 以同时运行和调试多个模块。 You should see multiple .NET Core console app windows, which each window representing a different module.

   ![调试多个模块](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按 Ctrl+F5 或选择“停止”按钮可停止调试。

## <a name="build-and-push-images"></a>生成并推送映像

1. 请确保启动项目是 AzureIoTEdgeApp1。 选择“调试”或“发布”作为配置，以生成模块映像。

    > [!NOTE]
    > 如果选择“调试”，Visual Studio 使用 `Dockerfile.(amd64|windows-amd64).debug` 生成 Docker 映像。 生成容器映像时，它将在该映像中包含 .NET Core 命令行调试器 VSDBG。 对于生产就绪的 IoT Edge 模块，建议使用“发布”配置，此模块可在没有 VSDBG 的情况下使用 `Dockerfile.(amd64|windows-amd64)`。

1. 如果使用的是专用注册表（如 Azure 容器注册表），请使用以下 Docker 命令登录。 如果使用的是本地注册表，可[运行本地注册表](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. 如果使用的是专用注册表（如 Azure 容器注册表），则需要将注册表登录信息添加到文件 `deployment.template.json` 中的运行时设置。 将占位符替换为实际 ACR 管理员用户名、密码和注册表名。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. 右键单击“AzureIoTEdgeApp1”，选择“构建并推送 Edge 解决方案”，为每个模块生成和推送 Docker 映像。

   ![生成并推送映像](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>部署解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可使用 Cloud Explorer for Visual Studio 部署模块。 为方案和 `deployment.json` 文件准备好部署清单后，接下来只需要选择一个设备来接收部署即可。

1. 通过单击“视图” > “Cloud Explorer”打开 Cloud Explorer。 Make sure you've logged in to Visual Studio 2019.

1. 在 Cloud Explorer 中，展开订阅并找到要部署的 Azure IoT 中心和 Azure IoT Edge 设备。

1. 右键单击 IoT Edge 设备，为其创建部署。你需要选择 `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 下的部署清单文件。

   > [!NOTE]
   > 不得选择 `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. Click the refresh button to see the new modules running along with the **SimulatedTemperatureSensor** module and **$edgeAgent** and **$edgeHub**.

## <a name="view-generated-data"></a>查看生成的数据

1. To monitor the D2C message for a specific device, select the device in the list and then click **Start Monitoring Built-in Event Endpoint** in the **Action** window.

1. To stop monitoring data, select the device in the list and then select **Stop Monitoring Built-in Event Endpoint** in the **Action** window.

## <a name="next-steps"></a>后续步骤

若要开发用于 IoT Edge 设备的自定义模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
