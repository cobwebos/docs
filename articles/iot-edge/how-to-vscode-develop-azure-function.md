---
title: 使用 Visual Studio Code 开发 Azure Functions 并将其部署到 Azure IoT Edge | Microsoft Docs
description: 在 VS Code 中在不切换上下文的情况下开发 C# Azure Functions 并通过 Azure IoT Edge 进行部署
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 47d420b4b283b390f67719233c4bea59495a589a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2018
---
# <a name="use-visual-studio-code-to-develop-and-deploy-azure-functions-to-azure-iot-edge"></a>使用 Visual Studio Code 开发 Azure Functions 并将其部署到 Azure IoT Edge

本文提供了有关使用 [Visual Studio Code](https://code.visualstudio.com/) 作为主要开发工具在 IoT Edge 上开发和部署 Azure Functions 的详细说明。 

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一个物理设备，或者，你可以在开发计算机上模拟 IoT Edge 设备。

在开始本指南之前，请确保已完成以下教程。
- 在 [Windows](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-windows) 或 [Linux](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 中在模拟设备上部署 Azure IoT Edge
- [部署 Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function)

下面是一个清单，其中显示了在完成前面的教程后应会得到的项目。

- [Visual Studio Code](https://code.visualstudio.com/)。 
- [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
- [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 
- [Python 2.7](https://www.python.org/downloads/)
- [IoT Edge 控制脚本](https://pypi.python.org/pypi/azure-iot-edge-runtime-ctl)
- AzureIoTEdgeFunction 模板 (`dotnet new -i Microsoft.Azure.IoT.Edge.Function`)
- 包含至少一个 IoT Edge 设备的活动 IoT 中心。

另外，建议安装 [Docker support for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) 以便更好地管理模块映像和容器。

> [!NOTE]
> 目前，IoT Edge 上的 Azure Functions 仅支持 C#。

## <a name="deploy-azure-iot-functions-in-vs-code"></a>在 VS Code 中部署 Azure IoT Functions
在教程[部署 Azure Functions](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-function) 中，将在 VS Code 中更新、生成并发布函数模块映像，然后访问 Azure 门户来部署 Azure Functions。 本部分将介绍如何使用 VS Code 来部署和监视 Azure Functions。

### <a name="start-a-local-docker-registry"></a>启动本地 Docker 注册表
对于本文，可以使用任何兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本部分使用[本地 Docker 注册表](https://docs.docker.com/registry/deploying/)，在早期开发过程中更容易使用它进行测试。
在 VS Code **集成终端** (Ctrl + `) 中，运行以下命令启动本地注册表。  

```cmd/sh
docker run -d -p 5000:5000 --name registry registry:2 
```

> [!NOTE]
> 上面的示例显示了仅适用于测试的注册表配置。 随时可用于生产的注册表必须由 TLS 提供保护，并且理想状态下应当使用某种访问控制机制。 建议使用 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)来部署随时可用于生产的 IoT Edge 模块。

### <a name="create-a-function-project"></a>创建函数项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块。 如果在前一篇教程中已完成本部分，则可以放心地跳过本部分。

1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
2. 要在 dotnet 中安装（或更新）**AzureIoTEdgeFunction** 模板，请在集成终端中运行以下命令：

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Function
   ```
3. 为新模块创建一个项目。 以下命令会在当前的工作文件夹中创建项目文件夹 FilterFunction：

   ```cmd/sh
   dotnet new aziotedgefunction -n FilterFunction
   ```
 
4. 选择“文件”>“打开文件夹”，然后浏览到 **FilterFunction** 文件夹并在 VS Code 中打开此项目。
5. 浏览到 **FilterFunction** 文件夹，然后单击“选择文件夹”，在 VS Code 中打开此项目。
6. 在 VS Code 资源管理器中，展开 **EdgeHubTrigger-Csharp** 文件夹，然后打开 **run.csx** 文件。
7. 将此文件的内容替换为以下代码：

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

8. 保存文件。

### <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>创建 Docker 映像并将其发布到注册表

1. 在 VS Code 资源管理器中展开 **Docker** 文件夹。 然后展开适用于所用容器平台的文件夹（linux-x64 或 windows-nano）。
2. 右键单击“Dockerfile”文件，然后单击“生成 IoT Edge 模块 Docker 映像”。 

    ![生成 docker 映像](./media/how-to-vscode-develop-csharp-function/build-docker-image.png)

3. 导航到 **FilterFunction** 项目文件夹，然后单击“选择文件夹作为 EXE_DIR”。 
4. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如：`<your container registry address>/filterfunction:latest`。 若要部署到本地注册表，则它应为 `localhost:5000/filterfunction:latest`。
5. 将映像推送到 Docker 存储库。 使用 **Edge: Push IoT Edge module Docker image** 命令，并在 VS Code 窗口顶部的弹出文本框中输入映像 URL。 使用上一步骤中所用的同一映像 URL。
    ![推送 docker 映像](./media/how-to-vscode-develop-csharp-function/push-image.png)

### <a name="deploy-your-function-to-iot-edge"></a>将函数部署到 IoT Edge

1. 打开 `deployment.json` 文件，将 **modules** 部分替换为以下内容：
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
   "filterfunction": {
      "version": "1.0",
      "type": "docker",
      "status": "running",
      "restartPolicy": "always",
      "settings": {
         "image": "localhost:5000/filterfunction:latest",
         "createOptions": ""
      }
   }
   ```

2. 将 **routes** 节替换为以下内容：
   ```json
       "routes":{
           "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
           "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
   ```
   > [!NOTE]
   > 此运行时中的声明性规则将定义这些消息流经的位置。 本文需要两个路由。 第一个路由会通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器函数。 第二个路由会将消息从筛选器函数传输到 IoT 中心。 在此路由中，upstream 是一个特殊目的地，它告诉 Edge 中心将消息发送到 IoT 中心。

3. 保存此文件。
4. 在命令面板中，选择 **Edge: Create deployment for Edge device**。 然后，选择 IoT Edge 设备 ID 来创建部署。 或者，在设备列表中右键单击该设备 ID 并选择“为 Edge 设备创建部署”。

    ![创建部署](./media/how-to-vscode-develop-csharp-function/create-deployment.png)

5. 选择更新的 `deployment.json`。 在输出窗口中，可以看到部署的相应输出。
6. 在命令面板中启动 Edge 运行时。 **Edge: Start Edge**
7. 可以看到，你的 IoT Edge 运行时已开始使用模拟传感器和筛选器函数在 Docker 资源管理器中运行。

    ![正在运行的解决方案](./media/how-to-vscode-develop-csharp-function/solution-running.png)

8. 右键单击 Edge 设备 ID，可以在 VS Code 中监视 D2C 消息。

    ![监视消息](./media/how-to-vscode-develop-csharp-function/monitor-d2c-messages.png)


## <a name="next-steps"></a>后续步骤

[在 VS Code 中调试 Azure Functions](how-to-vscode-debug-azure-function.md)
