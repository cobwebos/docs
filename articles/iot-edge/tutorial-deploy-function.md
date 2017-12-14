---
title: "使用 Azure IoT Edge 部署 Azure Functions | Microsoft 文档"
description: "将 Azure Functions 作为一个模块部署到 Edge 设备"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: v-jamebr
ms.date: 11/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: fb295b37819788ed14f54e4123ae0fe1b52d0210
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
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
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。 （可以从 Visual Studio Code 中的扩展面板安装此扩展。）
* [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 （可以从 Visual Studio Code 中的扩展面板安装此扩展。）
* [Docker](https://docs.docker.com/engine/installation/)。 对于本教程，适用于所用平台的 Community Edition (CE) 版本即可。 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd)。 

## <a name="set-up-a-docker-registry"></a>设置 Docker 注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来创建 Azure Functions，并使用它生成 [Docker 映像](https://docs.docker.com/glossary/?term=image)。 然后，将此 Docker 映像推送到由 [Docker 注册表](https://docs.docker.com/glossary/?term=registry)托管的 [Docker 存储库](https://docs.docker.com/glossary/?term=repository)。 最后，将已封装为 [Docker 容器](https://docs.docker.com/glossary/?term=container)的 Docker 映像从注册表部署到 IoT Edge 设备。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 Azure 容器注册表和 Docker 中心：

- [Azure 容器注册表](https://docs.microsoft.com/en-us/azure/container-registry/)可通过[付费订阅](https://azure.microsoft.com/en-us/pricing/details/container-registry/)获取。 对于本教程，基本订阅即可。 

- 若注册一个（免费）Docker ID，[Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)将提供一个免费的专用存储库。 
    1. 若要注册 Docker ID，请按照 Docker 网站上的[注册 Docker ID](https://docs.docker.com/docker-id/#register-for-a-docker-id) 中的说明操作。 

    2. 若要创建一个 Docker 专用存储库，请按照 Docker 网站上的[在 Docker 中心上新建一个存储库](https://docs.docker.com/docker-hub/repos/#creating-a-new-repository-on-docker-hub)中的说明操作。

在本教程中，将适时提供适用于 Azure 容器注册表和 Docker 中心的相关命令。

## <a name="create-a-function-project"></a>创建函数项目
以下步骤将介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建 IoT Edge 函数。
1. 打开 VS Code。
2. 使用“视图 | 集成终端”菜单命令打开 VS Code 集成终端。
3. 在集成终端的 .net 中，输入以下命令以安装（或更新）AzureIoTEdgeFunction 模板：

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 在集成终端中，输入以下命令为该新模块创建项目：

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction
    ```

    >[!NOTE]
    > 此命令将在当前的工作文件夹中创建项目文件夹 FilterFunction。 若要在其他位置创建，请在运行此命令前更改目录。

3. 使用“文件 | 打开文件夹”菜单命令，浏览到 FilterFunction 文件夹，然后单击“选择文件夹”，以在 VS Code 中打开此项目。
4. 在 VS Code 资源管理器中，单击 EdgeHubTrigger-Csharp 文件夹，然后单击 run.csx 文件，以将其打开。
5. 在 `#r "Microsoft.Azure.Devices.Client"` 语句后面添加以下语句：

    ```csharp
    #r "Newtonsoft.Json"
    ```

5. 在现有的 `using` 语句后面添加以下 using 语句：

    ```csharp
    using Newtonsoft.Json;
    ```

1. 添加以下类。 这些类将为传入消息的正文定义所需的架构。

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

1. 将 Run 方法的主体替换为以下代码。 此代码将根据消息正文中的温度值及温度阈值筛选消息。

    ```csharp
    const int temperatureThreshold = 25;

    byte[] messageBytes = messageReceived.GetBytes();
    var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

    if (!string.IsNullOrEmpty(messageString))
    {
        // Get the body of the message and deserialize it
        var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);
        
        if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
        {
            // We will send the message to the output as the temperature value is greater than the threashold
            var filteredMessage = new Message(messageBytes);
            // We need to copy the properties of the original message into the new Message object
            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
            {
                filteredMessage.Properties.Add(prop.Key, prop.Value);
            }
            // We are adding a new property to the message to indicate it is an alert
            filteredMessage.Properties.Add("MessageType", "Alert");
            // Send the message        
            await output.AddAsync(filteredMessage);
            log.Info("Received and transferred a message with temperature above the threshold");
        }
    }
    ```

11. 保存文件。

## <a name="publish-a-docker-image"></a>发布 Docker 映像

1. 生成 Docker 映像。
    1. 在 VS Code 资源管理器中，单击”Docker”文件夹，将其打开。 然后选择适用于所用容器平台的文件夹（linux-x64 或 windows-nano）。 
    2. 右键单击“Dockerfile”文件，然后单击“生成 IoT Edge 模块 Docker 映像”。 
    3. 在“选择文件夹”框中，导航到项目文件夹“FilterFunction”，然后单击“选择文件夹作为 EXE_DIR”。 
    4. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如 `<docker registry address>/filterfunction:latest`；其中“Docker 注册表地址”是 Docker ID（若使用的是 Docker 中心），或类似于 `<your registry name>.azurecr.io`（若使用的是 Azure 容器注册表）。
 
4. 登录到 Docker。 在集成终端中，输入以下命令： 

    - Docker 中心（出现提示后输入凭据）：
        
        ```csh/sh
        docker login
        ```

    - Azure 容器注册表：
        
        ```csh/sh
        docker login -u <username> -p <password> <Login server>
        ```
        
        若要查找此命令要使用的用户名、密码和登录服务器，请转到 [Azure 门户] (https://portal.azure.com)。 在“所有资源”中，单击 Azure 容器注册表的磁贴以打开其属性，然后单击“访问密钥”。 复制“用户名”、“密码”和“登录服务器”字段中的值。 登录服务器应为以下格式：`<your registry name>.azurecr.io`。

3. 将映像推送到 Docker 存储库。 使用“视图 | 命令面板... | Edge: 推送 IoT Edge 模块 Docker 映像”菜单命令，并在 VS Code 窗口顶部的弹出文本框中输入映像名称。 使用步骤 1.c 所使用的同一个映像名称。

## <a name="add-registry-credentials-to-your-edge-device"></a>将注册表凭据添加到 Edge 设备
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

1. 在 **Azure 门户**中导航到 IoT 中心。
2. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
1. 选择“设置模块”。 
2. 添加“tempSensor”模块。 只有之前未将“tempSensor”模块部署到 IoT Edge 设备时才需执行此步骤。
    1. 选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `tempSensor`。
    3. 在“映像 URI”字段中，输入 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`。
    4. 将其他设置保留不变，然后单击“保存”。
1. 添加“filterfunction”模块。
    1. 再次选择“添加 IoT Edge 模块”。
    2. 在“名称”字段中，输入 `filterfunction`。
    3. 在“映像”字段中，输入映像地址；例如 `<docker registry address>/filterfunction:latest`。
    74. 单击“保存” 。
2. 单击“下一步”。
3. 在“指定路由”步骤中，将以下 JSON 复制到文本框。 这些模块会将所有消息发布到 Edge 运行时。 此运行时中的声明性规则将定义这些消息流经的位置。 本教程需要两个路由。 第一个路由会通过“input1”终结点（该终结点是通过 FilterMessages 处理程序配置的终结点）将消息从温度传感器传输到筛选器模块。 第二个路由会将消息从筛选器模块传输到 IoT 中心。 在此路由中，`upstream` 是特殊目的地，它将告诉 Edge 中心将消息发送到 IoT 中心。 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterfunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterfunction/outputs/* INTO $upstream"
       }
    }
    ```

4. 单击“下一步”。
5. 在“审阅模板”步骤中，单击“提交”。 
6. 返回到“IoT Edge 设备详细信息”页，并单击“刷新”。 应看到新的 filterfunction 模块与 tempSensor 模块和 IoT Edge 运行时在同时运行。 

## <a name="view-generated-data"></a>查看生成的数据

监视从 IoT Edge 设备发送到 IoT 中心的“设备到云”消息：
1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 使用“视图 | 资源管理器”菜单命令，打开 VS Code 资源管理器。 
    3. 在资源管理器中，单击“IOT 中心设备”，然后单击“...”。单击“设置 IoT 中心连接字符串”，然后在弹出窗口中输入 IoT Edge 设备所连接的 IoT 中心的连接字符串。 

        要找到此连接字符串，请在 Azure 门户中单击 IoT 中心的磁贴，然后单击“共享访问策略”。 在“共享访问策略”中，单击“iothubowner”策略，并复制“iothubowner”窗口中的 IoT 中心连接字符串。   

1. 要监视到达 IoT 中心的数据，请使用“视图 | 命令面板... | IoT: 开始监视 D2C 消息”菜单命令。 
2. 要停止监视数据，请使用“视图 | 命令面板... | IoT: 停止监视 D2C 消息”菜单命令。 

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了 Azure Functions，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 若要继续探索 Azure IoT Edge，请学习如何将 IoT Edge 用作网关。 

> [!div class="nextstepaction"]
> [创建 IoT Edge 网关设备](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
