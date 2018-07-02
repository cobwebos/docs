---
title: Azure IoT Edge C# 教程 | Microsoft Docs
description: 本教程介绍如何使用 C# 代码创建 IoT Edge 模块并将其部署到边缘设备
services: iot-edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/27/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 8cd4f87e690d64e1f69ef39f3422ebc73d79974a
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37053086"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>教程：开发 C# IoT Edge 模块并将其部署到模拟设备

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用的模拟 IoT Edge 设备是在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建基于 .NET Core 2.0 的 IoT Edge 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free)。


## <a name="prerequisites"></a>先决条件

* 已通过 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)快速入门创建 Azure IoT Edge 设备。
* IoT Edge 设备的主键连接字符串。  
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C#（由 OmniSharp 提供支持）扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 开发计算机上的 [Docker CE](https://docs.docker.com/install/)。 


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
以下步骤介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块项目。
1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
2. 选择“视图” > “命令面板”，打开 VS Code 命令面板。 
3. 在命令面板中，键入并运行“Azure: 登录”命令，然后按说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。
4. 在命令面板中，键入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 在命令面板中提供以下信息，以便创建解决方案： 
   1. 选择要在其中创建解决方案的文件夹。 
   2. 提供解决方案的名称，或者接受默认的 **EdgeSolution**。
   3. 选择“C# 模块”作为模块模板。 
   4. 将模块命名为 **CSharpModule**。 
   5. 将在上一部分创建的 Azure 容器注册表指定为第一个模块的映像存储库。 将 **localhost:5000** 替换为复制的登录服务器值。 最终的字符串看起来类似于 **\<注册表名称\>.azurecr.io/csharpmodule**。
 
4. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 有一个 **modules** 文件夹、一个 **.vscode** 文件夹、一个部署清单模板文件，以及一个 **.env** 文件。 打开 **modules** > **CSharpModule** > **Program.cs**。

5. 在 **CSharpModule** 命名空间的顶部，为以后要使用的类型添加三个 `using` 语句：

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

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

8. 在 Init 方法中，此代码创建并配置 ModuleClient 对象。 该对象允许模块连接到本地 Azure IoT Edge 运行时，发送并接收消息。 在 **Init** 方法中使用的连接字符串由 IoT Edge 运行时提供给模块。 创建 **ModuleClient** 后，代码通过模块孪生的所需属性读取 TemperatureThreshold，并且通过 **input1** 终结点注册一个回调方法，用于接收来自 IoT Edge 中心的消息。 将 `SetInputMessageHandlerAsync` 方法替换为新方法，然后添加 `SetDesiredPropertyUpdateCallbackAsync` 方法，以便进行所需的属性更新。 若要进行此更改，请使用以下代码替换 Init 方法的最后一行 ：

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine($"Property TemperatureThreshold not exist: {e.Message}"); 
    }

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
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
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
                await moduleClient.SendEventAsync("output1", filteredMessage);
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
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

11. 保存此文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 CSharpModule，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker，以便将模块映像推送到 ACR： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 或者再次在 Azure 门户中从注册表的“访问密钥”部分检索它们。

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件告知 `$edgeAgent` 部署两个模块：**tempSensor** 和 **CSharpModule**。 `CSharpModule.image` 值设置为映像的 Linux amd64 版本。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

3. 在 **deployment.template.json** 文件中有一个名为 **registryCredentials** 的节，用于存储 Docker 注册表凭据。 实际的用户名和密码对存储在属于 gitignore 文件的 .env 文件中。

4. 将 CSharpModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容： 
    ```json
        "CSharpModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 保存此文件。
5. 在 VS Code 资源管理器中右键单击 **deployment.template.json** 文件，然后选择“生成 IoT Edge 解决方案”。 

要求 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在新的 **config** 文件夹中生成 `deployment.json` 文件。 然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 `CSharpModule.dll` 容器化，然后将其推送到在初始化解决方案时指定的容器注册表。 

可在 VS Code 集成终端中查看具有标记的完整容器映像地址。 映像地址根据 `module.json` 文件中的信息生成，其格式为 **\<存储库\>:\<版本\>-\<平台\>**。 在本教程中，它应该类似于 **registryname.azurecr.io/csharpmodule:0.0.1-amd64**。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 选择“视图” > “资源管理器”，打开 VS Code 资源管理器。 
    2. 在资源管理器中，单击“AZURE IOT 中心设备”，然后单击“...”。单击“选择 IoT 中心”。 按照说明登录 Azure 帐户并选择 IoT 中心。 
       注意，也可通过单击“设置 IoT 中心连接字符串”进行设置。 在弹出窗口中输入 IoT Edge 设备连接到的 IoT 中心的连接字符串。

2. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备，然后单击“为 IoT Edge 设备创建部署”。 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。

3. 单击刷新按钮。 此时会看到新的 **CSharpModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视到达 IoT 中心的数据，请单击“...”，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT 中心: 停止监视 D2C 消息”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，可以安装用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)，然后通过 Docker 资源管理器在本地找到正在运行的模块。 在上下文菜单中单击“显示日志”，在集成终端中进行查看。
 
## <a name="clean-up-resources"></a>清理资源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果想要继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可删除本文中创建的本地配置和 Azure 资源，避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 资源组以及包含在其中的所有资源一旦删除就会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请使用中心名称和资源组名称执行以下命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。



## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 C# 模块](how-to-develop-csharp-module.md)。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
