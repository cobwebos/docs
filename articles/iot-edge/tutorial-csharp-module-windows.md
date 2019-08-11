---
title: 有关开发适用于 Windows 的 C# 模块的教程 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 C# 代码创建 IoT Edge 模块并将其部署到 Windows IoT Edge 设备。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5a1e487b52cb6f146ccc7fd3208ecc90de9aa080
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840158"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>教程：开发适用于 Windows 设备的 C# IoT Edge 模块

使用 Visual Studio 开发 C# 代码并将其部署到运行 Azure IoT Edge 的 Windows 设备。 

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio 创建基于 C# SDK 的 IoT Edge 模块。
> * 使用 Visual Studio 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案适用范围

本教程演示如何使用 **Visual Studio 2019** 以 **C#** 开发模块，以及如何将其部署到 **Windows 设备**。 若要开发适用于 Linux 设备的模块，请转到[开发适用于 Linux 设备的 C# IoT Edge 模块](tutorial-csharp-module.md)。 

使用下表了解开发 C 模块并将其部署到 Windows 设备的选项： 

| C# | Visual Studio Code | Visual Studio 2017/2019 | 
| -- | ------------------ | ------------------ |
| **Windows AMD64 开发** | ![在 VS Code 中开发 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) | ![在 Visual Studio 中开发 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) |
| **Windows AMD64 调试** |   | ![在 Visual Studio 中调试 WinAMD64 的 C# 模块](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，应已完成上一篇教程[开发适用于 Windows 设备的 IoT Edge 模块](tutorial-develop-for-windows.md)来设置开发环境。 完成该教程后，应已准备好以下必备组件： 

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Windows 设备](quickstart.md)。
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 扩展的 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。
* 配置为运行 Windows 容器的 [Docker CE](https://docs.docker.com/install/)。

> [!TIP]
> 如果使用的是 Visual Studio 2017（版本 15.7 或更高版本），请从 Visual Studio 市场下载并安装适用于 VS 2017 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

## <a name="create-a-module-project"></a>创建模块项目

以下步骤使用 Visual Studio 和 Azure IoT Edge Tools 扩展创建 IoT Edge 模块项目。 创建项目模板后，请添加新代码，使模块根据其报告属性筛选出消息。 

### <a name="create-a-new-project"></a>创建新项目

Azure IoT Edge Tools 为 Visual Studio 中支持的所有 IoT Edge 模块语言提供项目模板。 这些模板包含将工作模块部署到测试 IoT Edge 所需的所有文件和代码，或为你提供使用自己的业务逻辑自定义模板的起点。 

1. 启动 Visual Studio 2019 并选择“创建新项目”  。

2. 在“新建项目”窗口中，搜索“IoT Edge”  项目，然后选择“Azure IoT Edge (Windows amd64)”  项目。 单击“下一步”。  

   ![创建新的 Azure IoT Edge 项目](./media/tutorial-csharp-module-windows/new-project.png)

3. 在“配置新项目”窗口中，重命名项目和解决方案，使名称具有描述性，例如 **CSharpTutorialApp**。 单击“创建”以创建项目。  

   ![配置新的 Azure IoT Edge 项目](./media/tutorial-csharp-module-windows/configure-project.png)

4. 在 IoT Edge 应用程序和模块窗口中，使用以下值配置项目： 

   | 字段 | 值 |
   | ----- | ----- |
   | 选择模板 | 选择“C# 模块”。  | 
   | 模块项目名称 | 将模块命名为 **CSharpModule**。 | 
   | Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 系统已基于模块项目名称值预先填充容器映像。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br> 最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/csharpmodule。 |

   ![配置目标设备、模块类型和容器注册表的项目](./media/tutorial-csharp-module-windows/add-application-and-module.png)

5. 选择“确定”以应用更改。  

### <a name="add-your-registry-credentials"></a>添加注册表凭据

部署清单与 IoT Edge 运行时共享容器注册表的凭据。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 使用 Azure 容器注册表的“访问密钥”部分提供的凭据。  

1. 在 Visual Studio 解决方案资源管理器中打开 **deployment.template.json** 文件。 

2. 在 $edgeAgent 所需属性中找到 **registryCredentials** 属性。 

3. 使用凭据更新该属性，遵循以下格式： 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. 保存 deployment.template.json 文件。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

默认模块代码在输入队列上接收消息，并通过输出队列传递消息。 让我们添加一些额外的代码，以便模块在将边缘的消息转发到 IoT 中心之前对其进行处理。 更新模块，以便分析每条消息中的温度数据，并且只有在温度超过特定阈值时才将消息发送到 IoT 中心。 

1. 在 Visual Studio 中打开“CSharpModule” > “Program.cs”。  

2. 在 **CSharpModule** 命名空间的顶部，为稍后要使用的类型添加三个 **using** 语句：

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

3. 将 **temperatureThreshold** 变量添加到 counter 变量后面的 **Program** 类中。 temperatureThreshold 变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

4. 将 **MessageBody**、**Machine** 和 **Ambient** 类添加到变量声明后面的 **Program** 类中。 这些类将为传入消息的正文定义所需的架构。

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

5. 查找 **Init** 方法。 此方法创建并配置 **ModuleClient** 对象，使模块能够连接到本地 Azure IoT Edge 运行时以发送和接收消息。 代码还会注册一个回调，以通过 **input1** 终结点从 IoT Edge 中心接收消息。

   将整个 Init 方法替换为以下代码：
   
   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```
   
   更新的此 Init 方法仍使用 ModuleClient 来与 IoT Edge 运行时建立连接，但同时添加了新的功能。 它读取模块孪生的所需属性，以检索 **temperatureThreshold** 值。 然后，它创建一个回调用于侦听将来对模块孪生所需属性做出的任何更新。 使用此回调可以远程更新模块孪生中的温度阈值，而更改将合并到模块中。 

   更新的 Init 方法还会更改现有的 **SetInputMessageHandlerAsync** 方法。 在示例代码中，*input1* 上的传入消息将使用 *PipeMessage* 函数进行处理，但我们想要改用将在以下步骤中创建的 *FilterMessages* 函数。 

6. 将新的 **onDesiredPropertiesUpdate** 方法添加到 **Program** 类。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

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

7. 删除示例 **PipeMessage** 方法，并将其替换为新的 **FilterMessages** 方法。 每当模块从 IoT Edge 中心接收消息，就会调用此方法。 此方法筛选掉那些所报告温度低于温度阈值（通过孪生模块进行设置）的消息。 它还将 **MessageType** 属性添加到消息，其值设置为“警报”。  

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

9. 在 IoT Edge 解决方案中打开 **deployment.template.json** 文件。 此文件告知 IoT Edge 代理部署哪些模块（在本例中为 **SimulatedTemperatureSensor** 和 **CSharpModule**），并告知 IoT Edge 中心如何在它们之间路由消息。

10. 将 **CSharpModule** 模块孪生添加到部署清单。 在 **modulesContent** 节底部的 **$edgeHub** 模块孪生后面插入以下 JSON 内容： 

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![将模块孪生添加到部署模板](./media/tutorial-csharp-module-windows/module-twin.png)

11. 保存 deployment.template.json 文件。


## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **CSharpModule**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在开发计算机上使用以下命令登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会出现一条安全警告，其中建议使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 Visual Studio 解决方案资源管理器中，右键单击要生成的项目名称。 默认名称为 **AzureIotEdgeApp1**；由于生成的是 Windows 模块，因此扩展名应是 **Windows.Amd64**。 

3. 选择“生成并推送 IoT Edge 模块”。  

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。 

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

请确保 IoT Edge 设备已启动并正在运行。 

1. 在 Visual Studio Cloud Explorer 中，展开资源以查看 IoT 设备列表。 

2. 右键单击要接收部署的 IoT Edge 设备的名称。 

3. 选择“创建部署”。 

4. 在文件资源管理器中，选择解决方案的 config 文件夹中的 **deployment.windows-amd64** 文件。 

5. 刷新 Cloud Explorer，以查看设备下面列出的已部署模块。 

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。 

可以使用 IoT Edge Tools 扩展查看抵达 IoT 中心的消息。 

1. 在 Visual Studio Cloud Explorer 中，选择 IoT Edge 设备的名称。 

2. 在“操作”列表中，选择“开始监视内置事件终结点”。   

3. 查看抵达 IoT 中心的消息。 消息可能需要在一段时间后才会抵达，因为在发送消息之前，对 CSharpModule 代码所做的更改需要等到机器温度达到 25 度才会生效。 IoT 中心还会将消息类型“警报”添加到达到该温度阈值的任何消息。  

   ![查看抵达 IoT 中心的消息](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>编辑模块孪生

我们已使用 CSharpModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio 中打开 **deployment.windows-amd64.json** 文件。 （不是 deployment.template 文件。 如果在解决方案资源管理器中的 config 文件内未看到部署清单，请在 Cloud Explorer 工具栏中选择“显示所有文件”图标。） 

2. 找到 CSharpModule 孪生，将 **temperatureThreshold** 参数的值更改为比上次报告的温度高出 5 到 10 度的新温度。 

3. 保存 **deployment.windows-amd64.json** 文件。

4. 再次遵循部署步骤，将更新的部署清单应用到设备。 

5. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。 

## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 准备好生成自己的模块时，可以详细了解如何[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio 开发模块](how-to-visual-studio-develop-module.md)。 可以继续学习后续教程，了解 Azure IoT Edge 如何帮助你部署 Azure 云服务，以在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)
