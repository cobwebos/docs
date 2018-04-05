---
title: Azure IoT Edge C# 模块 | Microsoft Docs
description: 使用 C# 代码创建 IoT Edge 模块，并将它部署到边缘设备
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 11c737adb6578437a3708bb97397a24114e39585
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>开发 C# IoT Edge 模块，并将它部署到模拟设备 - 预览

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建基于 .NET Core 2.0 的 IoT Edge 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或第一个教程创建 Azure IoT Edge 设备。
* IoT Edge 设备的主键连接字符串。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* Visual Studio Code 所在计算机上的 [Docker](https://docs.docker.com/engine/installation/)。 对于本教程而言，使用社区版 (CE) 即可。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “Azure 容器注册表”。
2. 为注册表提供一个名称，选择一个订阅，选择一个资源组，然后将 SKU 设置为“基本”。 
3. 选择**创建**。
4. 创建容器注册表以后，导航到其中，然后选择“访问键”。 
5. 将“管理员用户”切换到“启用”。
6. 复制“登录服务器”、“用户名”和“密码”的值。 本教程中稍后将 Docker 映像发布到注册表，以及向 Edge 运行时添加注册表凭据时，将使用这些值。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块。
1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
2. 在集成终端的 dotnet 中，输入以下命令安装（或更新）AzureIoTEdgeModule 模板：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. 为新模块创建一个项目。 以下命令通过容器存储库创建项目文件夹 FilterModule。 如果使用 Azure 容器注册表，第二个参数应采用 `<your container registry name>.azurecr.io` 的形式。 在当前的工作文件夹中输入以下命令：

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. 选择“文件” > “打开文件夹”。
5. 浏览到 FilterModule 文件夹，然后单击“选择文件夹”，在 VS Code 中打开此项目。
6. 在 VS Code 资源管理器中，单击 Program.cs 将其打开。

   ![打开 Program.cs][1]

7. 在 **FilterModule** 命名空间的顶部，为以后要使用的类型添加三个 `using` 语句：

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. 将 `temperatureThreshold` 变量添加到 **Program** 类。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. 将 `MessageBody`、`Machine` 和 `Ambient` 类添加到 **Program** 类。 这些类将为传入消息的正文定义所需的架构。

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

10. 在 Init 方法中，此代码创建并配置 DeviceClient 对象。 该对象允许模块连接到本地 Azure IoT Edge 运行时，发送并接收消息。 在 **Init** 方法中使用的连接字符串由 IoT Edge 运行时提供给模块。 创建 **DeviceClient** 后，代码通过Module Twin中的所需属性读取TemperatureThreshold的值， 并且通过 **input1** 终结点注册一个回调方法，用于接收来自 IoT Edge 中心的消息。 将 `SetInputMessageHandlerAsync` 方法替换为新方法，然后添加 `SetDesiredPropertyUpdateCallbackAsync` 方法，以便进行所需的属性更新。 若要进行此更改，请使用以下代码替换 Init 方法的最后一行 ：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    if (moduleTwinCollection["TemperatureThreshold"] != null)
    {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. 将 `onDesiredPropertiesUpdate` 方法添加到 **Program** 类。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

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

12. 将 `PipeMessage` 方法替换为 `FilterMessages` 方法。 每当模块从 IoT Edge 中心接收消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。 它还将 **MessageType** 属性添加到消息，其值设置为“警报”。 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
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
            var deviceClient = (DeviceClient)userContext;
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

13. 保存此文件。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 在 VS Code 集成终端输入以下命令，登录到 Docker： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   若要查找在此命令中使用的用户名、密码和登录服务器，请转到 [Azure 门户] (https://portal.azure.com)。 在“所有资源”中，单击 Azure 容器注册表的磁贴以打开其属性，然后单击“访问密钥”。 复制“用户名”、“密码”和“登录服务器”字段中的值。 

2. 在 VS Code 资源管理器中，右键单击 module.json 文件，然后单击“生成和推送 IoT Edge 模块 Docker 映像”。 在 VS Code 窗口顶部的弹出下拉框中，选择容器平台，即为 Linux 容器选择“amd64”，或为 Windows 容器选择“windows-amd64”。 然后，VS Code 生成代码、将 `FilterModule.dll` 容器化并推送到指定的容器注册表。


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
4. 检查 **tempSensor** 模块是否已自动填充。 否则，请使用以下步骤来添加该模块：
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 将其他设置保留不变，然后单击“保存”。
5. 添加在前面部分创建的 **filterModule** 模块。 
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
6. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 这些模块会将所有消息发布到 Edge 运行时。 此运行时中的声明性规则将定义消息流经的位置。 本教程需要两个路由。 第一个路由会通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，`upstream` 是特殊目的地，它将告诉 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. 单击“资源组名称” 的 Azure 数据工厂。
9. 在“审阅模板”步骤中，单击“提交”。 
10. 返回到“IoT Edge 设备详细信息”页，并单击“刷新”。 应看到新的 filter 模块与 tempSensor 模块和 IoT Edge 运行时在同时运行。 

## <a name="view-generated-data"></a>查看生成的数据

监视从 IoT Edge 设备发送到 IoT 中心的“设备到云”消息：
1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 选择“视图” > “资源管理器”，打开 VS Code 资源管理器。 
    2. 在资源管理器中，单击“IOT 中心设备”，然后单击“...”。单击“设置 IoT 中心连接字符串”，然后在弹出窗口中输入 IoT Edge 设备所连接的 IoT 中心的连接字符串。 

        要找到此连接字符串，请在 Azure 门户中单击 IoT 中心的磁贴，然后单击“共享访问策略”。 在“共享访问策略”中，单击“iothubowner”策略，并复制“iothubowner”窗口中的 IoT 中心连接字符串。   

2. 若要监视到达 IoT 中心的数据，请选择“视图” > “命令面板”，然后搜索“IoT: 开始监视 D2C 消息”菜单命令。 
3. 若要停止监视数据，请使用“IoT: 停止监视 D2C 消息”菜单命令。 

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
