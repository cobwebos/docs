---
title: "Azure IoT Edge C# 模块 | Microsoft Docs"
description: "使用 C# 代码创建 IoT Edge 模块，并将它部署到边缘设备"
services: iot-edge
keywords: 
author: JimacoMS2
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: fb7674d8c292e7d571a94ac4625b0858a90704b3
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>开发 C# IoT Edge 模块，并将它部署到模拟设备 - 预览

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程逐步演示了创建和部署用于筛选 IoT Edge 模拟设备（该设备是 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程“在模拟设备上部署 Azure IoT Edge”部分创建的模拟设备）上传感器数据的 IoT Edge 模块的过程。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建基于 .NET Core 2.0 的 IoT Edge 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


本教程中创建的 IoT Edge 模块将筛选设备生成的温度数据，当温度高于指定阈值时它只发送上游消息。 

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或之前的教程创建 Azure IoT Edge 设备。
* IoT 中心连接字符串适用于 IoT Edge 设备连接到的 IoT 中心。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 （可以从 Visual Studio Code 中的扩展面板安装此扩展。）
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 （可以从 Visual Studio Code 中的扩展面板安装此扩展。）
* 适用于 Visual Studio Code 的 Azure IoT Edge 扩展
* [Docker](https://docs.docker.com/engine/installation/)。 对于本教程而言，使用平台的社区版 (CE) 版本即可。 请确保将其安装在运行 VS Code 的计算机上。
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="choose-or-sign-up-for-a-docker-registry"></a>选择或注册 Docker 注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并创建 [Docker 映像](https://docs.docker.com/glossary/?term=image)。 然后，将此 Docker 映像推送到托管在 [Docker 注册表](https://docs.docker.com/glossary/?term=registry)上的 [Docker 存储库](https://docs.docker.com/glossary/?term=repository)中。 最后，将已封装为 [Docker 容器](https://docs.docker.com/glossary/?term=container)的 Docker 映像从注册表部署到 IoT Edge 设备。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 Azure 容器注册表和 Docker 中心：

- [Azure 容器注册表](https://docs.microsoft.com/en-us/azure/container-registry/)可通过[付费订阅](https://azure.microsoft.com/en-us/pricing/details/container-registry/)获取。 对于本教程，基本订阅即可。 

- 若注册一个（免费）Docker ID，[Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)将提供一个免费的专用存储库。 
    1. 若要注册 Docker ID，请按照 Docker 网站上的[注册 Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) 中的说明操作。 

    2. 要创建一个 Docker 专用存储库，请按照 Docker 网站上的[在 Docker 中心上新建一个存储库](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)中的说明操作。

在本教程中，将适时提供适用于 Azure 容器注册表和 Docker 中心的相关命令。

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块。
1. 打开 VS Code。
2. 使用“视图 | 集成终端”菜单命令打开 VS Code 集成终端。
3. 在集成终端的 dotnet 中，输入以下命令安装（或更新）AzureIoTEdgeModule 模板：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

2. 在集成终端中，输入以下命令为该新模块创建项目：

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule
    ```

    >[!NOTE]
    > 此命令在当前的工作文件夹中创建项目文件夹 FilterModule。 若要在其他位置创建，请在运行此命令前更改目录。
 
3. 使用“文件 | 打开文件夹”菜单命令，浏览到 FilterModule 文件夹，然后单击“选择文件夹”，在 VS Code 中打开此项目。
4. 在 VS Code 资源管理器中，单击 Program.cs 将其打开。
5. 将以下字段添加到 Program 类。

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. 将以下类添加到 Program 类。 这些类将为传入消息的正文定义所需的架构。

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

1. 在 Init 方法中，此代码创建并配置 DeviceClient 对象。 该对象允许模块连接到本地 Azure IoT Edge 运行时，发送并接收消息。 在 Init 方法中使用的连接字符串参数由环境变量 EdgeHubConnectionString 的 IoT Edge 运行时提供给此模块。 创建 DeviceClient 后，代码通过 input1 终结点注册回叫，接收来自 IoT Edge 中心的消息。 用新的方法替换用作处理消息的回叫方法，并在模块孪生上为所需属性更新附加回叫。 要进行此更改，请用以下代码替换 Init 方法的最后一行：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

1. 将以下方法添加到 Program 类，基于后端设备通过该模块孪生发送的所需属性，更新温度阈值字段。 所有模块都具有自己模块孪生。 模块孪生使后端设备能够配置在模块内部运行的代码。

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"].exists())
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

1. 使用以下方法替换 PipeMessage 方法。 每当模块从 Edge 中心发送消息，就会调用此方法。 它根据消息正文中的温度值以及通过模块孪生设置的温度阈值筛选消息。

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

11. 生成项目。 使用“视图 | 资源管理器”菜单命令，打开 VS Code 资源管理器。 在资源管理器中，右键单击“FilterModule.csproj”文件，再单击“生成 IoT Edge 模块”，即可编译该模块，并将二进制文件及其依赖项导出到在下一步中创建 Docker 映像的文件夹中。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 生成 Docker 映像。
    1. 在 VS Code 资源管理器中，单击”Docker”文件夹，将其打开。 然后选择适用于所用容器平台的文件夹（linux-x64 或 windows-nano）。 
    2. 右键单击“Dockerfile”文件，然后单击“生成 IoT Edge 模块 Docker 映像”。 
    3. 在“选择文件夹”框中，浏览到或输入 `./bin/Debug/netcoreapp2.0/publish`。 单击“选择文件夹作为 EXE_DIR”。
    4. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如：`<docker registry address>/filtermodule:latest`；其中“Docker 注册表地址”是 Docker ID（若使用的是 Docker 中心），或类似于 `<your registry name>.azurecr.io`（若使用的是 Azure 容器注册表）。
 
4. 登录到 Docker。 在集成终端中，输入以下命令： 

    - Docker 中心（出现提示后输入凭据）：
        
        ```csh/sh
        docker login
        ```

    - 对于 Azure 容器注册表：
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        要查找要在此命令中使用的用户名、密码和登录服务器，请转到 [Azure 门户] (https://portal.azure.com)。 在“所有资源”中，单击 Azure 容器注册表的磁贴以打开其属性，然后单击“访问密钥”。 复制“用户名”、“密码”和“登录服务器”字段中的值。 登录服务器应为以下格式：`<your registry name>.azurecr.io`。

3. 将映像推送到 Docker 存储库。 使用“视图 | 命令面板... | Edge: 推送 IoT Edge 模块 Docker 映像”菜单命令，并在 VS Code 窗口顶部的弹出文本框中输入映像名称。 使用步骤 1.c 中所用的映像名称。

## <a name="add-registry-credentials-to-edge-runtime-on-your-edge-device"></a>将注册表凭据添加到 Edge 设备的 Edge 运行时
在运行 Edge 设备的计算机上将注册表凭据添加到 Edge 运行时。 这将为此运行时提供拉取容器的访问权限。 

- 在 Windows 上，运行以下命令：
    
    ```cmd/sh
    iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

- 在 Linux 上，运行以下命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password> 
    ```

## <a name="run-the-solution"></a>运行解决方案

1. 在 [Azure 门户](https://portal.azure.com)中导航到 IoT 中心。
2. 转到“IoT Edge (预览)”，然后选择 IoT Edge 设备。
3. 选择“设置模块”。 
2. 添加“tempSensor”模块。 只有之前未将“tempSensor”模块部署到 IoT Edge 设备时才需执行此步骤。
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 将其他设置保留不变，然后单击“保存”。
9. 添加“filtermodule”
    1. 再次选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `filtermodule`。
    3. 在“映像”字段中，输入映像地址；例如 `<docker registry address>/filtermodule:latest`。
    4. 检查“编辑模块孪生”对话框。
    5. 将文本框中用于模块孪生的 JSON 替换为以下 JSON： 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. 单击“保存” 。
12. 单击“下一步”。
13. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 这些模块会将所有的消息发布到 Edge 运行时。 此运行时中的声明性规则将定义这些消息流经的位置。 本教程需要两个路由。 第一个路由会通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，`upstream` 是特殊目的地，它将告诉 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. 单击“下一步”。
5. 在“审阅模板”步骤中，单击“提交”。 
6. 返回到“IoT Edge 设备详细信息”页，并单击“刷新”。 应看到新的 filter 模块与 tempSensor 模块和 IoT Edge 运行时在同时运行。 

## <a name="view-generated-data"></a>查看生成的数据

监视从 IoT Edge 设备发送到 IoT 中心的“设备到云”消息：
1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 使用“视图 | 资源管理器”菜单命令，打开 VS Code 资源管理器。 
    3. 在资源管理器中，单击“IOT 中心设备”，然后单击“...”。单击“设置 IoT 中心连接字符串”，然后在弹出窗口中输入 IoT Edge 设备所连接的 IoT 中心的连接字符串。 

        要找到此连接字符串，请在 Azure 门户中单击 IoT 中心的磁贴，然后单击“共享访问策略”。 在“共享访问策略”中，单击“iothubowner”策略，并复制“iothubowner”窗口中的 IoT 中心连接字符串。   

1. 要监视到达 IoT 中心的数据，请使用“视图 | 命令面板... | IoT: 开始监视 D2C 消息”菜单命令。 
2. 要停止监视数据，请使用“视图 | 命令面板... | IoT: 停止监视 D2C 消息”菜单命令。 

## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 可以继续查看以下任一教程，了解 Azure IoT Edge 有助于将数据转化为边缘业务洞察力的其他方式。

> [!div class="nextstepaction"]
> [将 Azure 函数部署为模块](tutorial-deploy-function.md)
> [将 Azure 流分析部署为模块](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
