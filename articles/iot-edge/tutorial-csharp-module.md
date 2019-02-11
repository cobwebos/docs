---
title: 教程：创建自定义 C# 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 C# 代码创建 IoT Edge 模块并将其部署到边缘设备。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2acf30a9f71accb4780d473ce51b3ff640f12dac
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303506"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>教程：开发 C# IoT Edge 模块并将其部署到模拟设备

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建基于 .NET Core 2.1 SDK 的 IoT Edge 模块。
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

>[!NOTE]
>也可[使用 Visual Studio 2017 开发、调试和部署 IoT Edge 模块](how-to-visual-studio-develop-csharp-module.md)。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)


## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用适用于 Visual Studio Code 的 Azure IoT 工具来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

如果还没有容器注册表，请执行以下步骤，以便在 Azure 中创建一个新的：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

2. 提供以下值，以便创建容器注册表：

   | 字段 | 值 | 
   | ----- | ----- |
   | 注册表名称 | 提供唯一名称。 |
   | 订阅 | 从下拉列表中选择“订阅”。 |
   | 资源组 | 建议对在 IoT Edge 快速入门和教程中创建的所有测试资源使用同一资源组。 例如，**IoTEdgeResources**。 |
   | 位置 | 选择靠近你的位置。 |
   | 管理员用户 | 设置为“启用”。 |
   | SKU | 选择“基本”。 | 

5. 选择“创建”。

6. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 

7. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值来访问容器注册表。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤使用 Visual Studio Code 和 Azure IoT 工具来创建基于 .NET Core 2.0 SDK 的 IoT Edge 模块项目。

### <a name="create-a-new-solution"></a>创建新的解决方案

创建可以使用自己的代码自定义的 C# 解决方案模板。 

1. 在 Visual Studio Code 中，选择“查看” > “命令面板”，以打开 VS Code 命令面板。 

2. 在命令面板中，输入并运行命令“Azure: Sign in”并按照说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

3. 在命令面板中，输入并运行“Azure IoT Edge: **New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“C# 模块”。 |
   | 提供模块名称 | 将模块命名为 **CSharpModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/csharpmodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-csharp-module/repository.png)

VS Code 窗口将加载你的 IoT Edge 解决方案空间。 解决方案工作区包含五个顶级组件。 **模块**文件夹包含模块的 C# 代码以及用于将模块构建为容器映像的 Dockerfile。 **\.env** 文件存储容器注册表凭据。 **deployment.template.json** 文件包含 IoT Edge 运行时用于在设备上部署模块的信息。 **deployment.debug.template.json** 文件包含模块的调试版本。 你不会在本教程中编辑 **\.vscode** 文件夹或 **\.gitignore** 文件。

如果在创建解决方案时未指定容器注册表，但接受了默认的 localhost:5000 值，则不会有 \.env 文件。 

<!--
   ![C# solution workspace](./media/tutorial-csharp-module/workspace.png)
-->

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 

1. 在 VS Code 资源管理器中，打开 .env 文件。 
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

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

5. 在 Init 方法中，此代码创建并配置 ModuleClient 对象。 该对象允许模块连接到本地 Azure IoT Edge 运行时，发送并接收消息。 在 **Init** 方法中使用的连接字符串由 IoT Edge 运行时提供给模块。 创建 **ModuleClient** 后，代码将从模块孪生的所需属性中读取 **temperatureThreshold** 值。 代码注册一个回调，以通过 **input1** 终结点从 IoT Edge 中心接收消息。 将 **SetInputMessageHandlerAsync** 方法替换为新方法，并添加 **SetDesiredPropertyUpdateCallbackAsync** 方法用于更新所需属性。 若要进行此更改，请使用以下代码替换 Init 方法的最后一行 ：

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

9. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件告知 IoT Edge 代理部署哪些模块（在本例中为 **tempSensor** 和 **CSharpModule**），并告知 IoT Edge 中心如何在它们之间路由消息。 Visual Studio Code 扩展会自动填充部署模板中所需的大部分信息，但确保解决方案的所有内容都是准确的： 

   1. 在 VS Code 状态栏中将 IoT Edge 的默认平台设置为 **amd64**，这意味着将 **CSharpModule** 设置为映像的 Linux amd64 版本。 在状态栏中将默认平台从 **amd64** 更改为 **arm32v7** 或 **windows-amd64**（如果这就是 IoT Edge 设备的体系结构）。 

      ![更新模块映像平台](./media/tutorial-csharp-module/image-platform.png)

   2. 验证该模板具有正确的模块名称，而不是具有在创建 IoT Edge 解决方案时你更改的默认 **SampleModule** 名称。

   3. **registryCredentials** 节会存储 Docker 注册表凭据，以便 IoT Edge 代理可以拉取模块映像。 实际的用户名和密码对存储在 git 忽略的 .env 文件中。 将凭据添加到 .env 文件中（如果尚未这样做）。  

   4. 如果想要了解有关部署清单的更多信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

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


## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **CSharpModule**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker。 然后可将模块映像推送到 Azure 容器注册表。
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 也可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 CSharpModule.dll 容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

可在 VS Code 集成终端中查看具有标记的完整容器映像地址。 映像地址根据 module.json 文件中的信息生成，其格式为 \<存储库\>:\<版本\>-\<平台\>。 在本教程中，它应该类似于 registryname.azurecr.io/csharpmodule:0.0.1-amd64。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可以使用用于 Visual Studio Code 的 Azure IoT 中心工具包扩展（以前称为 Azure IoT 工具包扩展）来部署模块。 你已经为方案准备了部署清单，即 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

1. 在 VS Code 命令面板中，运行 **Azure IoT Hub:Select IoT Hub** 命令。 

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 

3. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

4. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

   ![为单个设备创建部署](./media/tutorial-csharp-module/create-deployment.png)

5. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。 不要使用 deployment.template.json 文件。 

6. 单击“刷新”按钮。 此时会看到新的 **CSharpModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。  

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。 

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。 

在 IoT Edge 设备本身上，可以使用 `iotedge list` 命令查看部署模块的状态。 应该看到四个模块：两个 IoT Edge 运行时模块、tempSensor 以及在本教程中创建的自定义模块。 启动所有模块可能需要数分钟，因此如果一开始没有看到全部模块，请重新运行命令。 

若要查看由任何模块生成的消息，请使用 `iotedge logs <module name>` 命令。 

可以使用 Visual Studio Code 在消息到达 IoT 中心时查看它们。 

1. 若要监视抵达 IoT 中心的数据，请选择省略号 (**...**)，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT Hub: Stop monitoring D2C message”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，请安装适用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。 可在 Docker 资源管理器中本地查找正在运行的模块。 在上下文菜单中选择“显示日志”，在集成终端中进行查看。
 
## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 C# 模块](how-to-develop-csharp-module.md)。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)
