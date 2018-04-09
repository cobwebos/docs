---
title: 使用 Azure IoT Edge 部署 Azure Functions | Microsoft 文档
description: 将 Azure Functions 作为一个模块部署到 Edge 设备
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3d7dd0986878c747f92afc712301453bc8772ef2
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>将 Azure Functions 作为 IoT Edge 模块进行部署 - 预览版
可以使用 Azure Functions 部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程逐步演示了创建和部署 Azure Functions 以筛选 IoT Edge 模拟设备（该设备是 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 教程“在模拟设备上部署 Azure IoT Edge”部分中创建的）上的传感器数据的过程。 本教程介绍如何执行下列操作：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 Azure Functions
> * 使用 VS Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


本教程中创建的 Azure Functions 可筛选设备生成的温度数据，并在温度高于指定阈值时只将上游消息发送到 Azure IoT 中心。 

## <a name="prerequisites"></a>先决条件

* 已通过快速入门或之前的教程创建 Azure IoT Edge 设备。
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [Docker](https://docs.docker.com/engine/installation/)。 对于本教程，适用于所用平台的 Community Edition (CE) 版本即可。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “Azure 容器注册表”。
2. 为注册表提供一个名称，选择一个订阅，选择一个资源组，然后将 SKU 设置为“基本”。 
3. 选择**创建**。
4. 创建容器注册表以后，导航到其中，然后选择“访问键”。 
5. 将“管理员用户”切换到“启用”。
6. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值。 

## <a name="create-a-function-project"></a>创建函数项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建 IoT Edge 函数。
1. 打开 Visual Studio Code。
2. 要打开 VS Code 集成终端，请选择“视图” > “集成终端”。
3. 要在 dotnet 中安装（或更新）**AzureIoTEdgeFunction** 模板，请在集成终端中运行以下命令：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 为新模块创建一个项目。 以下命令通过容器存储库创建项目文件夹 **FilterFunction**。 如果使用 Azure 容器注册表，第二个参数应采用 `<your container registry name>.azurecr.io` 的形式。 在当前的工作文件夹中输入以下命令：

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. 选择“文件” > “打开文件夹”，然后浏览到 FilterFunction 文件夹并在 VS Code 中打开项目。
4. 在 VS Code 资源管理器中，展开 EdgeHubTrigger-Csharp 文件夹，然后打开 run.csx 文件。
5. 将此文件的内容替换为以下代码：

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
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

11. 保存文件。

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 在 VS Code 集成终端输入以下命令，登录到 Docker： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   若要查找在此命令中使用的用户名、密码和登录服务器，请转到 [Azure 门户] (https://portal.azure.com)。 在“所有资源”中，单击 Azure 容器注册表的磁贴以打开其属性，然后单击“访问密钥”。 复制“用户名”、“密码”和“登录服务器”字段中的值。 

2. 在 VS Code 资源管理器中，右键单击 module.json 文件，然后单击“生成和推送 IoT Edge 模块 Docker 映像”。 在 VS Code 窗口顶部的弹出下拉框中，选择容器平台，即为 Linux 容器选择“amd64”，或为 Windows 容器选择“windows-amd64”。 然后，VS Code 将函数代码容器化并推送到指定的容器注册表。


3. 可在 VS Code 集成终端中获取具有标记的完整容器映像地址。 有关生成和推送定义的详细信息，可参考 `module.json` 文件。

## <a name="add-registry-credentials-to-your-edge-device"></a>将注册表凭据添加到 Edge 设备
在运行 Edge 设备的计算机上将注册表凭据添加到 Edge 运行时。 这将为此运行时提供拉取容器的访问权限。 

- 在 Windows 上，运行以下命令：
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- 在 Linux 上，运行以下命令：
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>运行解决方案

1. 在 **Azure 门户**中导航到 IoT 中心。
2. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
1. 选择“设置模块”。 
2. 如果已将“tempSensor”模块部署到此设备，则该模块可能会自动填充。 如果尚未部署，请按照以下步骤操作，添加模块：
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 将其他设置保留不变，然后单击“保存”。
1. 添加“filterFunction”模块。
    1. 再次选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `filterFunction`。
    3. 在“映像 URI”字段中，输入映像地址；例如 `<your container registry address>/filterfunction:0.0.1-amd64`。 可从上一节中找到完整映像地址。
    74. 单击“ **保存**”。
2. 单击“资源组名称” 的 Azure 数据工厂。
3. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 第一个路由会通过“input1”终结点将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，`$upstream` 是特殊目的地，它将告诉 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. 单击“资源组名称” 的 Azure 数据工厂。
5. 在“审阅模板”步骤中，单击“提交”。 
6. 返回到“IoT Edge 设备详细信息”页，并单击“刷新”。 应看到新的 filterfunction 模块与 tempSensor 模块和 IoT Edge 运行时在同时运行。 

## <a name="view-generated-data"></a>查看生成的数据

监视从 IoT Edge 设备发送到 IoT 中心的“设备到云”消息：
1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 在 Azure 门户中，导航到 IoT 中心，并选择“共享访问策略”。 
    2. 选择“iothubowner”，然后复制“连接字符串 - 主键”的值。
    3. 在 VS Code 资源管理器中，单击“IOT 中心设备”，然后单击“...”。 
    4. 选择“设置 IoT 中心连接字符串”，然后在弹出窗口中输入 Iot 中心连接字符串。 

2. 要监视到达 IoT 中心的数据，请选择“视图” > “命令面板...”，然后搜索“IoT: 开始监视 D2C 消息”。 
3. 要停止监视数据，请在命令面板中使用“IoT: Stop monitoring D2C message”命令。 

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了 Azure Functions，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 若要继续探索 Azure IoT Edge，请学习如何将 IoT Edge 用作网关。 

> [!div class="nextstepaction"]
> [创建 IoT Edge 网关设备](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
