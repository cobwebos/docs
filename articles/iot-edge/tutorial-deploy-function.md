---
title: 教程：将 Azure Functions 部署为模块 - Azure IoT Edge
description: 在本教程中，你将一个 Azure 函数开发为 IoT Edge模块，然后将其部署到边缘设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: d9b9ba3a8092992c9ebca9b3242223213b75ad80
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87439681"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules"></a>教程：将 Azure Functions 作为 IoT Edge 模块进行部署

可以使用 Azure Functions 部署代码，以直接将业务逻辑实现到 Azure IoT Edge 设备。 本教程将引导你在模拟的 IoT Edge 设备上创建和部署用于筛选传感器数据的 Azure 函数。 使用的模拟 IoT Edge 设备是在 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 使用 Visual Studio Code 创建 Azure 函数。
> * 使用 VS Code 和 Docker 创建 Docker 映像并将其发布到容器注册表。
> * 将模块从容器注册表部署到 IoT Edge 设备。
> * 查看筛选的数据。

<center>

![ - 教程体系结构：暂存以及部署函数模块](./media/tutorial-deploy-function/functions-architecture.png)
</center>

在本教程中创建的 Azure 函数可以筛选由设备生成的温度数据。 该函数只在温度高于指定阈值的情况下，向 Azure IoT 中心上游发送消息。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，应已完成上一篇教程来设置用于开发 Linux 容器的开发环境：[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)。 完成该教程后，已应准备好以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

若要通过 Azure Functions 开发 IoT Edge 模块，请在开发计算机上安装下述额外的必备组件：

* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。

## <a name="create-a-function-project"></a>创建函数项目

在先决条件部分安装的适用于 Visual Studio Code 的 Azure IoT 工具提供管理功能和一些代码模板。 在本部分，请使用 Visual Studio Code 创建包含 Azure 函数的 IoT Edge 解决方案。

### <a name="create-a-new-project"></a>创建新项目

创建可以使用自己的代码进行自定义的 C# 函数解决方案模板。

1. 在开发计算机上打开 Visual Studio Code。

2. 打开 VS Code 命令面板，方法是选择“视图”   >   “命令面板”。

3. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | Value |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称（例如 **FunctionSolution**），或者接受默认名称。 |
   | 选择模块模板 | 选择“Azure Functions - C#”。  |
   | 提供模块名称 | 将模块命名为 **CSharpFunction**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 localhost:5000 替换为 Azure 容器注册表中的“登录服务器”值 。 可以在 Azure 门户的容器注册表的“概述”页中检索登录服务器。 最终的字符串看起来类似于 \<registry name\>.azurecr.io/CSharpFunction。 |

   ![提供 Docker 映像存储库](./media/tutorial-deploy-function/repository.png)

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。

IoT Edge 扩展尝试从 Azure 中拉取容器注册表凭据并将其填充到环境文件中。 检查凭据是否已包含在内。 如果没有，请立即添加：

1. 在 VS Code 资源管理器中，打开 .env 文件。
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。
3. 保存此文件。

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以开发适用于 Linux AMD64 和 Linux ARM32v7 设备的 C 模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认值为 Linux AMD64。

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

让我们添加一些附加的代码，使模块在将消息转发到 IoT 中心之前，先在边缘上对其进行处理。

1. 在 Visual Studio Code 中，打开“模块”   > “CSharpFunction”   > “CSharpFunction.cs”  。

1. 将 **CSharpFunction.cs** 文件的内容替换为以下代码。 此代码接收有关环境温度和计算机温度的遥测，只有在计算机温度高于定义的阈值时才将消息转发到 IoT 中心。

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
                       // Send the message to the output as the temperature value is greater than the threshold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
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

1. 保存文件。

## <a name="build-and-push-your-iot-edge-solution"></a>生成并推送 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并修改了 **CSharpFunction**，该函数会筛选出报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端” 。

2. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

3. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

   首次执行此过程可能需要几分钟时间，但下次运行命令时速度会变快。

## <a name="view-your-container-image"></a>查看容器映像

将容器映像推送到容器注册表以后，Visual Studio Code 会输出一条成功消息。 若要自行确认操作是否成功，可以在注册表中查看映像。

1. 在 Azure 门户中，浏览到 Azure 容器注册表。
2. 选择“存储库”。 
3. 列表中应会显示 **csharpfunction** 存储库。 选择此存储库可查看更多详细信息。
4. 在“标记”部分，应会显示 **0.0.1-amd64** 标记。  此标记指示生成的映像的版本和平台。 这些值在 CSharpFunction 文件夹的 module.json 文件中设置。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

可以使用 Azure 门户将函数模块部署到 IoT Edge 设备，就像在快速入门中所做的一样。 也可以在 Visual Studio Code 中部署和监视模块。 以下部分使用适用于 VS Code 的 Azure IoT 工具，该工具已在先决条件中列出。 如果尚未安装该扩展，现在请安装。

1. 在 Visual Studio Code 资源管理器中的“Azure IoT 中心”部分下，展开“设备”可查看 IoT 设备的列表。 

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。

3. 浏览到包含 **CSharpFunction** 的解决方案文件夹。 打开 config 文件夹，选择 deployment.amd64.json 文件，然后选择“选择 Edge 部署清单”。 

4. 在设备下，展开“模块”可查看已部署的正在运行的模块的列表。 单击“刷新”按钮。 此时应看到新的 **CSharpFunction** 与 **SimulatedTemperatureSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 一起运行。

    新模块显示可能需要一些时间。 IoT Edge 设备必须从 IoT 中心检索其新的部署信息，启动新容器，然后将状态报告回 IoT 中心。

   ![在 VS Code 中查看部署的模块](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-the-generated-data"></a>查看生成的数据

若要查看到达 IoT 中心的所有消息，请在命令面板中运行 **Azure IoT Hub:开始监视内置事件终结点**。

也可通过筛选视图来查看从特定设备到达 IoT 中心的所有消息。 右键单击“Azure IoT 中心设备”部分中的设备，然后选择“开始监视内置事件终结点”。  

若要停止监视消息，请在命令面板中运行 **Azure IoT Hub:停止监视内置事件终结点**。

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 Azure 函数模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 进行开发](how-to-vs-code-develop-module.md)。

继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [在 Azure 流分析中使用浮动窗口查找平均值](tutorial-deploy-stream-analytics.md)
