---
title: 教程：开发适用于 Linux 的 C# 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 C# 代码创建 IoT Edge 模块并将其部署到 Linux IoT Edge 设备。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 305804682ede9833619837807d6f174ec670560e
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414259"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>教程：开发适用于 Linux 设备的 C# IoT Edge 模块

使用 Visual Studio Code 开发 C# 代码并将其部署到运行 Azure IoT Edge 的 Linux 设备。 

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建基于 .NET Core 2.1 SDK 的 IoT Edge 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案作用域

本教程演示如何使用 **Visual Studio Code** 在 **C#** 中开发模块，以及如何将其部署到 **Linux 设备**。 如果要开发适用于 Windows 设备的模块，请转到[开发适用于 Windows 设备的 C# IoT Edge 模块](tutorial-csharp-module-windows.md)。

使用下表了解开发 C 模块并将其部署到 Linux 的选项： 

| C# | Visual Studio Code | Visual Studio | 
| -- | ------------------ | ------------- |
| **Linux AMD64** | ![VS Code 中适用于 LinuxAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) | ![Visual Studio 中适用于 LinuxAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![VS Code 中适用于 LinuxARM32 的 C# 模块](./media/tutorial-c-module/green-check.png) | ![Visual Studio 中适用于 LinuxARM64 的 C# 模块](./media/tutorial-c-module/green-check.png) |

>[!NOTE]
>[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)中提供对 Linux ARM64 设备的支持。 有关详细信息，请参阅[在 Visual Studio Code（预览版）中开发和调试 ARM64 IoT Edge 模块](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)。

## <a name="prerequisites"></a>先决条件

在开始本教程之前，你应该已经完成了上一个教程[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)，设置了开发环境。 完成该教程后，你应该已经具备以下系统必备组件： 

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* [运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)。
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

若要完成这些教程，请在开发计算机上准备好下面这些附加的系统必备组件： 

* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-module-project"></a>创建模块项目

以下步骤使用 Visual Studio Code 和 Azure IoT Tools 扩展为 C# 创建 IoT Edge 模块项目。 创建项目模板后，添加新代码，以便模块根据报告的属性筛选出消息。 

### <a name="create-a-new-project"></a>创建新项目

创建可以使用自己的代码自定义的 C# 解决方案模板。 

1. 在 Visual Studio Code 中，选择“查看” > “命令面板”，以打开 VS Code 命令面板。   

2. 在命令面板中，输入并运行命令“Azure:  Sign in”并按照说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

3. 在命令面板中，输入并运行“Azure IoT Edge: **New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“C# 模块”。  |
   | 提供模块名称 | 将模块命名为 **CSharpModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br>最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/csharpmodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-csharp-module/repository.png)


### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 使用 Azure 容器注册表的“访问密钥”部分中的凭据  。 

1. 在 VS Code 资源管理器中，打开 **.env** 文件。 
2. 使用 Azure 容器注册表中的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 C# 模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认设置为 Linux AMD64。 

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。 

2. 在命令面板中，从选项列表中选择目标体系结构。 本教程将使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认设置 **amd64**。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

1. 在 VS Code 资源管理器中，打开 **modules** > **CSharpModule** > **Program.cs**。

2. 在 **CSharpModule** 命名空间的顶部，为稍后要使用的类型添加三个 **using** 语句：

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. 将 **temperatureThreshold** 变量添加到 **Program** 类。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. 将 **MessageBody**、**Machine** 和 **Ambient** 类添加到 **Program** 类。 这些类将为传入消息的正文定义所需的架构。

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

5. 找到 **Init** 函数。 此函数可创建并配置 **ModuleClient** 对象，使模块能够连接到本地 Azure IoT Edge 运行时以发送和接收消息。 创建 **ModuleClient** 后，代码将从模块孪生的所需属性中读取 **temperatureThreshold** 值。 代码注册一个回调，以通过 **input1** 终结点从 IoT Edge 中心接收消息。 将 **SetInputMessageHandlerAsync** 方法替换为新方法，并添加 **SetDesiredPropertyUpdateCallbackAsync** 方法用于更新所需属性。 若要进行此更改，请使用以下代码替换 Init 方法的最后一行  ：

    ```csharp
    // Register a callback for messages that are received by the module.
    // await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read the TemperatureThreshold value from the module twin's desired properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);
    
    // Attach a callback for updates to the module twin's desired properties.
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

    // Register a callback for messages that are received by the module.
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

6. 将 **onDesiredPropertiesUpdate** 方法添加到 **Program** 类。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
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

7. 将 **PipeMessage** 方法替换为 **FilterMessages** 方法。 每当模块从 IoT Edge 中心接收消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。 它还将 **MessageType** 属性添加到消息，其值设置为“警报”。  

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

8. 保存 Program.cs 文件。

9. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 

10. 将 **CSharpModule** 模块孪生添加到部署清单。 在 **modulesContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![将模块孪生添加到部署模板](./media/tutorial-csharp-module/module-twin.png)

11. 保存 deployment.template.json 文件。


## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 CSharpModule，该模块会筛选出其中报告的计算机温度处于可接受限制范围内的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端”   。

1. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。 
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会出现一条安全警告，其中建议使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。  

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

使用 Visual Studio Code 资源管理器和 Azure IoT Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

请确保 IoT Edge 设备已启动并正在运行。

1. 在 Visual Studio Code 资源管理器中展开“Azure IoT 中心设备”部分，查看 IoT 设备的列表。 

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。  

5. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。  不要使用 deployment.template.json 文件。 

6. 单击“刷新”按钮。 此时会看到新的 **CSharpModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。  

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态  。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。

1. 在 Visual Studio Code 资源管理器中右键单击 IoT Edge 设备的名称，选择“开始监视内置事件终结点”。 

2. 查看抵达 IoT 中心的消息。 消息可能需要在一段时间后才会抵达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 CModule 代码所做的更改将等到计算机温度达到 25 度时才发送消息。 IoT 中心还会将消息类型“警报”添加到达到该温度阈值的任何消息。  

   ![查看抵达 IoT 中心的消息](./media/tutorial-csharp-module/view-d2c-message.png)
 
## <a name="edit-the-module-twin"></a>编辑模块孪生

我们使用部署清单中的 CSharpModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio Code 中，展开 IoT Edge 设备下的详细信息以查看正在运行的模块。 

2. 右键单击“CSharpModule”并选择“编辑模块孪生”   。 

3. 在所需属性中找到“TemperatureThreshold”  。 将其值更改为比上次报告的温度高出 5 到 10 度的新温度。 

4. 保存模块孪生文件。

5. 右键单击模块孪生编辑窗格中的任意位置，然后选择“更新模块孪生”。  

5. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。 

## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备后，可以详细了解如何[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio Code 开发模块](how-to-vs-code-develop-module.md)。 可以继续学习后续教程，了解如何借助 Azure IoT Edge 部署 Azure 云服务，在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)
