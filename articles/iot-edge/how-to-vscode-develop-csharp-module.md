---
title: "使用 Visual Studio Code 通过 Azure IoT Edge 开发 C# 模块 | Microsoft Docs"
description: "在 VS Code 中在不切换上下文的情况下开发 C# 模块并通过 Azure IoT Edge 进行部署"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/06/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 269f77e5015175e45e0078926ef06699811889a4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="use-visual-studio-code-to-develop-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 通过 Azure IoT Edge 开发 C# 模块
本文提供有关使用 [Visual Studio Code](https://code.visualstudio.com/) 作为主要开发工具来开发和部署 IoT Edge 模块的详细说明。 

## <a name="prerequisites"></a>先决条件
本教程假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一个物理设备，或者，你可以在开发计算机上模拟 IoT Edge 设备。

在开始本指南之前，请确保已完成以下教程。
- 在 [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 中在模拟设备上部署 Azure IoT Edge
- [开发 C# IoT Edge 模块并将其部署到模拟设备](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)

下面是一个清单，其中显示了在完成前面的教程后应会得到的项目。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 控制脚本](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeModule 模板 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 包含至少一个 IoT Edge 设备的活动 IoT 中心。

另外，建议安装 [Docker support for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) 以便更好地管理模块映像和容器。

## <a name="deploy-an-azure-iot-edge-module-in-vs-code"></a>在 VS Code 中部署 Azure IoT Edge 模块

### <a name="list-your-iot-hub-devices"></a>列出 IoT 中心设备
可以在 VS Code 中使用两种方法列出 IoT 中心设备。 可以选择任何一种继续操作。

#### <a name="sign-in-your-azure-account-in-vscode-and-choose-your-iot-hub"></a>在 VSCode 中登录自己的 Azure 帐户，并选择 IoT 中心
1. 在命令面板（按 F1 或 Ctrl + Shift + P）中，键入并选择 **Azure: Sign in**。 然后单击弹出窗口中的“复制并打开”。* 在浏览器中粘贴 (Ctrl + V) 代码，并单击“继续”按钮。 然后使用 Azure 帐户登录。 在 VS Code 状态栏中可以看到帐户信息。
2. 在命令面板（按 F1 或 Ctrl + Shift + P）中，键入并选择 **IoT: Select IoT Hub**。 首先，请选择在上一篇教程中创建了 IoT 中心的订阅。 然后选择包含 IoT Edge 设备的 IoT 中心。


#### <a name="set-iot-hub-connection-string"></a>设置 IoT 中心连接字符串
1. 在命令面板（按 F1 或 Ctrl + Shift + P）中，键入并选择 **IoT: Set IoT Hub Connection String**。 确保将连接字符串粘贴到 **iothubowner** 策略（可以在 Azure 门户中 IoT 中心的“共享访问策略”中找到它）的下面。
 

在 IoT 中心设备资源管理器的左侧栏中，可以查看设备列表。

### <a name="start-your-iot-edge-runtime-and-deploy-a-module"></a>启动 IoT Edge 运行时并部署模块
在设备上安装并启动 Azure IoT Edge 运行时。 部署一个可将遥测数据发送到 IoT 中心的模拟传感器模块。
1. 在命令面板中，选择 **Edge: Setup Edge** 并选择 IoT Edge 设备 ID。 或者在设备列表中右键单击“Edge 设备 ID”，并选择“安装 Edge”。
2. 在命令面板中，选择 **Edge: Start Edge** 以启动 Edge 运行时。 在集成终端中可以看到相应的输出。
3. 在 Docker 资源管理器中检查 Edge 运行时状态。 绿色表示它正在运行。 已成功启动 IoT Edge 运行时。
4. 现在，Edge 运行时正在运行，意味着电脑可以模拟 Edge 设备。 下一步是模拟 sensorthing，以便不断地将消息发送到 Edge 设备。 在命令面板中，键入并选择 **Edge: Generate Edge configuration file**。 选择要在其中创建此文件的文件夹。 在生成的 deployment.json 文件中，将行 "<registry>/<image>:<tag>" 替换为 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
5. 选择 **Edge: Create deployment for Edge device**，并选择用于创建新部署的 Edge 设备 ID。 或者，可以在设备列表中右键单击 Edge 设备 ID，并选择“为 Edge 设备创建部署”。 
6. 应会看到，IoT Edge 已开始使用模拟传感器在 Docker 资源管理器中运行。 在 Docker 资源管理器中右键单击容器。 可以监视每个模块的 docker 日志。
7. 右键单击 Edge 设备 ID，可以在 VS Code 中监视 D2C 消息。
8. 若要停止 IoT Edge 运行时和传感器模块，可以在命令面板中键入并选择 **Edge: Stop Edge**。

## <a name="develop-and-deploy-a-c-module-in-vs-code"></a>在 VS Code 中开发和部署 C# 模块
在学习教程[开发 C# 模块](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module)时，我们已在 VS Code 中更新、生成并发布了模块映像，然后访问 Azure 门户部署了 C# 模块。 本部分介绍如何使用 VS Code 来部署和监视 C# 模块。

### <a name="start-a-local-docker-registry"></a>启动本地 Docker 注册表
在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本部分使用[本地 Docker 注册表](https://docs.docker.com/registry/deploying/)，在早期开发过程中更容易使用它进行测试。
在 VS Code **集成终端** (Ctrl + `) 中，运行以下命令启动本地注册表。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上面的示例显示了仅适用于测试的注册表配置。 随时可用于生产的注册表必须由 TLS 提供保护，并且理想状态下应当使用某种访问控制机制。 建议使用 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)来部署随时可用于生产的 IoT Edge 模块。

### <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块。 如果在前一篇教程中已完成本部分，则可以放心地跳过本部分。
1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
3. 在集成终端的 dotnet 中，输入以下命令安装（或更新）AzureIoTEdgeModule 模板：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 为新模块创建一个项目。 以下命令在当前的工作文件夹中创建项目文件夹 FilterModule：

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```
 
3. 选择“文件” > “打开文件夹”。
4. 浏览到 FilterModule 文件夹，然后单击“选择文件夹”，在 VS Code 中打开此项目。
5. 在 VS Code 资源管理器中，单击 Program.cs 将其打开。
6. 将 `temperatureThreshold` 变量添加到 **Program** 类。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

7. 将 `MessageBody`、`Machine` 和 `Ambient` 类添加到 **Program** 类。 这些类将为传入消息的正文定义所需的架构。

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
    ```

8. 在 Init 方法中，此代码创建并配置 DeviceClient 对象。 该对象允许模块连接到本地 Azure IoT Edge 运行时，发送并接收消息。 在 **Init** 方法中使用的连接字符串由 IoT Edge 运行时提供给模块。 创建 DeviceClient 后，代码通过 input1 终结点注册回叫，接收来自 IoT Edge 中心的消息。 将 `SetInputMessageHandlerAsync` 方法替换为新方法，然后添加 `SetDesiredPropertyUpdateCallbackAsync` 方法，以便进行所需的属性更新。 要进行此更改，请用以下代码替换 Init 方法的最后一行：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

9. 将 `onDesiredPropertiesUpdate` 方法添加到 **Program** 类。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

10. 将 `PipeMessage` 方法替换为 `FilterMessages` 方法。 每当模块从 IoT Edge 中心接收消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。 它还将 **MessageType** 属性添加到消息，其值设置为“警报”。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        int counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            byte[] messageBytes = message.GetBytes();
            string messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 若要生成项目，请右键单击资源管理器中的“FilterModule.csproj”文件，然后单击“生成 IoT Edge 模块”。 此流程编译模块并将二进制文件及其依赖项导出到用于创建 Docker 映像的文件夹中。


### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 在 VS Code 资源管理器中展开 **Docker** 文件夹。 然后展开适用于所用容器平台的文件夹（linux-x64 或 windows-nano）。
2. 右键单击“Dockerfile”文件，然后单击“生成 IoT Edge 模块 Docker 映像”。 
3. 在“选择文件夹”窗口，浏览到或输入 `./bin/Debug/netcoreapp2.0/publish`。 单击“选择文件夹作为 EXE_DIR”。
4. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如：`<your container registry address>/filtermodule:latest`。 若要部署到本地注册表，则它应为 `localhost:5000/filtermodule:latest`。
5. 将映像推送到 Docker 存储库。 使用 **Edge: Push IoT Edge module Docker image** 命令，并在 VS Code 窗口顶部的弹出文本框中输入映像 URL。 使用上一步骤中所用的同一映像 URL。

### <a name="deploy-your-iot-edge-modules"></a>部署 IoT Edge 模块

1. 打开 `deployment.json` 文件，将 **modules** 节替换为以下内容：
    ```json
    "tempSensor": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview",
            "createOptions": ""
        }
    },
    "filtermodule": {
        "version": "1.0",
        "type": "docker",
        "status": "running",
        "restartPolicy": "always",
        "settings": {
            "image": "localhost:5000/filtermodule:latest",
            "createOptions": ""
        }
    }
    ```

2. 将 **routes** 节替换为以下内容：
    ```json
    {
        "routes": {
            "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
            "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        }
    }
    ```
   > [!NOTE]
   > 此运行时中的声明性规则将定义这些消息流经的位置。 本教程需要两个路由。 第一个路由通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，upstream 是一个特殊目标，它告知 Edge 中心要将消息发送到 IoT 中心。

3. 保存此文件。
4. 在命令面板中，选择 **Edge: Create deployment for Edge device**。 然后，选择 IoT Edge 设备 ID 来创建部署。 或者，在设备列表中右键单击该设备 ID 并选择“为 Edge 设备创建部署”。
5. 选择更新的 `deployment.json`。 在输出窗口中，可以看到部署的相应输出。
6. 在命令面板中启动 Edge 运行时。 **Edge: Start Edge**
7. 可以看到，IoT Edge 运行时已开始使用模拟传感器和筛选器模块在 Docker 资源管理器中运行。
8. 右键单击 Edge 设备 ID，可以在 VS Code 中监视 D2C 消息。


## <a name="next-steps"></a>后续步骤

在本教程中，我们已在 VS Code 中创建了一个 IoT Edge 模块，并将其部署到 IoT Edge 设备。 接下来可以继续学习下列教程之一来了解在 VS Code 中开发 Azure IoT Edge 时的其他方案。

> [!div class="nextstepaction"]
> [在 VS Code 中调试 C# 模块](how-to-vscode-debug-csharp-module.md)
