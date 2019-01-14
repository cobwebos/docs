---
title: 教程：将 Azure 函数部署到设备 - Azure IoT Edge | Microsoft Docs
description: 在本教程中，你将一个 Azure 函数开发为 IoT Edge模块，然后将其部署到边缘设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: d8912b67656cb9862f31b03eb9a899dfc650f377
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053359"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>教程：将 Azure Functions 作为 IoT Edge 模块进行部署

可以使用 Azure Functions 部署代码，以直接将业务逻辑实现到 Azure IoT Edge 设备。 本教程将引导你在模拟的 IoT Edge 设备上创建和部署用于筛选传感器数据的 Azure 函数。 使用的模拟 IoT Edge 设备是在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 Azure 函数。
> * 使用 VS Code 和 Docker 创建 Docker 映像并将其发布到容器注册表。
> * 将模块从容器注册表部署到 IoT Edge 设备。
> * 查看筛选的数据。

<center>
![关系图 - 教程体系结构，暂存以及部署函数模块](./media/tutorial-deploy-function/functions-architecture.png)
</center>

>[!NOTE]
>Azure IoT Edge 上的 Azure Function 模块为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

在本教程中创建的 Azure 函数可以筛选由设备生成的温度数据。 该函数只在温度高于指定阈值的情况下，向 Azure IoT 中心上游发送消息。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机设置为 Edge 设备。

云资源：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* [适用于 Visual Studio Code 的 Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>创建容器注册表

本教程将使用适用于 Visual Studio Code 的 Azure IoT 工具来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

可以使用任意兼容 Docker 的注册表来保存容器映像。 两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

    ![在 Azure 门户中创建容器注册表](./media/tutorial-deploy-function/create-container-registry.png)

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

## <a name="create-a-function-project"></a>创建函数项目

在先决条件部分安装的适用于 Visual Studio Code 的 Azure IoT 工具提供管理功能和一些代码模板。 在本部分，请使用 Visual Studio Code 创建包含 Azure 函数的 IoT Edge 解决方案。 

1. 在开发计算机上打开 Visual Studio Code。

2. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

3. 在命令面板中，输入并运行 **Azure IoT Edge:New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称（例如 **FunctionSolution**），或者接受默认名称。 |
   | 选择模块模板 | 选择“Azure Functions - C#”。 |
   | 提供模块名称 | 将模块命名为 **CSharpFunction**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/CSharpFunction。 |

   ![提供 Docker 映像存储库](./media/tutorial-deploy-function/repository.png)

4. VS Code 窗口将加载你的 IoT Edge 解决方案工作区：一个 \.vscode 文件夹、一个 modules 文件夹、一个部署清单模板文件， 以及一个 \.env 文件。 在 VS Code 资源管理器中，打开“模块” > “CSharpFunction” > “CSharpFunction.cs”。

5. 将 **CSharpFunction.cs** 文件的内容替换为以下代码。 此代码接收有关环境温度和计算机温度的遥测，只有在计算机温度高于定义的阈值时才将消息转发到 IoT 中心。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;

   namespace Functions.Samples
   {
       public static class CSharpFunction
       {
           [FunctionName("CSharpFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       var filteredMessage = new Message(messageBytes);
                       // Copy the properties of the original message into the new Message object.
                       foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                       {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                       // Add a new property to the message to indicate it is an alert.
                       filteredMessage.Properties.Add("MessageType", "Alert");
                       // Send the message.       
                       await output.AddAsync(filteredMessage);
                       logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
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
   }
   ```

6. 保存文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **CSharpFunction**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

在此部分，你为容器注册表提供凭据两次。 第一次是从开发计算机进行本地登录，这样 Visual Studio Code 就能将映像推送到注册表。 第二次是在 IoT Edge 解决方案的 **.env** 文件中，目的是为 IoT Edge 设备提供从注册表拉取映像的权限。 

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端”。 

2. 在集成终端输入以下命令，登录到容器注册表。 使用用户名以及此前从 Azure 容器注册表复制的登录服务器。
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```

    当系统提示输入密码时，请粘贴容器注册表的密码，然后按 **Enter**。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件将需要部署到设备的具体模块告知 IoT Edge 运行时。 请注意，Function 模块 **CSharpFunction** 与提供测试数据的 **tempSensor** 模块一起列出。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

   ![查看部署清单中的模块](./media/tutorial-deploy-function/deployment-template.png)

3. 打开 IoT Edge 解决方案工作区中的 **.env** 文件。 此 git 忽略的文件存储容器注册表凭据，因此不需将它们置于部署清单模板中。 为容器注册表提供“用户名”和“密码”。 

5. 保存此文件。

6. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将函数容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

## <a name="view-your-container-image"></a>查看容器映像

将容器映像推送到容器注册表以后，Visual Studio Code 会输出一条成功消息。 若要自行确认操作是否成功，可以在注册表中查看映像。 

1. 在 Azure 门户中，浏览到 Azure 容器注册表。 
2. 选择“存储库”。
3. 列表中应会显示 **csharpfunction** 存储库。 选择此存储库可查看更多详细信息。
4. 在“标记”部分，应会显示 **0.0.1-amd64** 标记。 此标记指示生成的映像的版本和平台。 这些值在 CSharpFunction 文件夹的 module.json 文件中设置。 

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

可以使用 Azure 门户将函数模块部署到 IoT Edge 设备，就像在快速入门中所做的一样。 也可以在 Visual Studio Code 中部署和监视模块。 以下部分使用适用于 VS Code 的 Azure IoT 工具，该工具已在先决条件中列出。 如果尚未安装该扩展，现在请安装。 

1. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

2. 搜索并运行 **Azure:Sign in** 命令。 按照说明登录 Azure 帐户。 

3. 在命令面板中，搜索并运行 **Azure IoT Hub:Select IoT Hub** 命令。 

4. 选择包含 IoT 中心的订阅，然后选择要访问的 IoT 中心。

5. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

6. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

7. 浏览到包含 **CSharpFunction** 的解决方案文件夹。 打开 config 文件夹，选择 deployment.json 文件，然后选择“选择 Edge 部署清单”。

8. 刷新“Azure IoT 中心设备”部分。 此时会看到新的 **CSharpFunction** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 新模块显示可能需要一些时间。 IoT Edge 设备必须从 IoT 中心检索其新的部署信息，启动新容器，然后将状态报告回 IoT 中心。 

   ![在 VS Code 中查看部署的模块](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>查看生成的数据

若要查看到达 IoT 中心的所有消息，请在命令面板中运行 **Azure IoT Hub:Start Monitoring D2C Message**。

也可通过筛选视图来查看从特定设备到达 IoT 中心的所有消息。 右键单击“Azure IoT 中心设备”部分的设备，然后选择“开始监视 D2C 消息”。

若要停止监视消息，请在命令面板中运行 **Azure IoT Hub:Start Monitoring D2C Message** 命令。 


## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 Azure 函数模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 Azure 函数](how-to-develop-csharp-function.md)。 

继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [在 Azure 流分析中使用浮动窗口查找平均值](tutorial-deploy-stream-analytics.md)

