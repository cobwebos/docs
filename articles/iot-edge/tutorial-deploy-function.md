---
title: 使用 Azure IoT Edge 部署 Azure 函数 | Microsoft Docs
description: 将 Azure 函数作为一个模块部署到 Edge 设备。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f3ba0ccb1cb8961344b605e7ec386b6d6692262
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006871"
---
# <a name="tutorial-deploy-azure-functions-as-iot-edge-modules-preview"></a>教程：将 Azure 函数作为 IoT Edge 模块进行部署（预览版）

可以使用 Azure Functions 部署代码，以直接将业务逻辑实现到 Azure IoT Edge 设备。 本教程将引导你在模拟的 IoT Edge 设备上创建和部署用于筛选传感器数据的 Azure 函数。 使用的模拟 IoT Edge 设备是在 [Windows][lnk-tutorial1-win] 或 [Linux][lnk-tutorial1-lin] 快速入门的“在模拟设备上部署 Azure IoT Edge”中创建的。 本教程介绍如何执行以下操作：     

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 Azure 函数。
> * 使用 VS Code 和 Docker 创建 Docker 映像并将其发布到容器注册表。
> * 将模块从容器注册表部署到 IoT Edge 设备。
> * 查看筛选的数据。

>[!NOTE]
>Azure IoT Edge 上的 Azure 函数模块为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

在本教程中创建的 Azure 函数可以筛选由设备生成的温度数据。 该函数只在温度高于指定阈值的情况下，向 Azure IoT 中心上游发送消息。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

若要测试在本教程中生成的函数模块，需要一个 IoT Edge 设备。 可以使用在 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中配置的设备。

必须在开发计算机上安装以下必备组件： 
* [Visual Studio Code](https://code.visualstudio.com/)。 
* [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。 
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 开发计算机上的 [Docker CE](https://docs.docker.com/install/)。 

## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “容器注册表”。

    ![创建容器注册表](./media/tutorial-deploy-function/create-container-registry.png)

2. 为注册表输入名称，然后选择一个订阅。
3. 对于资源组，建议使用包含 IoT 中心的资源组名称。 将所有资源置于同一组中可以对其集中管理。 例如，删除用于测试的资源组会删除该组中包含的所有测试资源。 
4. 将 SKU 设置为“基本”，并将“管理员用户”切换到“启用”。 
5. 选择**创建**。
6. 创建容器注册表后，请浏览到其中，然后选择“访问密钥”。 
7. 复制“登录服务器”、“用户名”和“密码”的值。 本教程后面会用到这些值。 

## <a name="create-a-function-project"></a>创建函数项目
以下步骤使用 Visual Studio Code 和 Azure IoT Edge 扩展创建 IoT Edge 函数。

1. 打开 Visual Studio Code。
2. 打开 VS Code 集成终端，方法是选择“视图” > “集成终端”。 
2. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。
3. 在命令面板中，输入并运行命令“Azure: 登录”。 按照说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。
3. 在命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 在命令面板中提供以下信息，以便创建解决方案： 

   1. 选择要在其中创建解决方案的文件夹。 
   2. 提供解决方案的名称，或者接受默认的 **EdgeSolution**。
   3. 选择“Azure Functions - C#”作为模块模板。 
   4. 将模块命名为 **CSharpFunction**。 
   5. 将在上一部分创建的 Azure 容器注册表指定为第一个模块的映像存储库。 将 **localhost:5000** 替换为复制的登录服务器值。 最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/csharpfunction。

4. VS Code 窗口将加载你的 IoT Edge 解决方案工作区：一个 \.vscode 文件夹、一个 modules 文件夹、一个部署清单模板文件， 以及一个 \.env 文件。 在 VS Code 资源管理器中，打开 **modules** > **CSharpFunction** > **EdgeHubTrigger-Csharp** > **run.csx**。

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
            // Get the body of the message and deserialize it.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold.
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object.
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert.
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message.       
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
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
   ```

6. 保存文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **CSharpFunction**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker。 然后可将模块映像推送到 Azure 容器注册表： 
     
    ```csh/sh
    docker login -u <ACR username> <ACR login server>
    ```
    使用用户名以及此前从 Azure 容器注册表复制的登录服务器。 系统会提示输入密码。 将密码粘贴到提示符处，然后按 **Enter**。

    ```csh/sh
    Password: <paste in the ACR password and press enter>
    Login Succeeded
    ```

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 deployment.template.json 文件。 此文件将需要部署到设备的具体模块告知 IoT Edge 运行时。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

3. 在部署清单中找到 **registryCredentials** 节。 使用容器注册表中的凭据更新 **username**、**password** 和 **address**。 此节为设备上的 IoT Edge 运行时提供拉取存储在专用注册表中的容器映像的权限。 实际的用户名和密码对存储在 git 忽略的 .env 文件中。

5. 保存此文件。

6. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成 IoT Edge 解决方案”。 

告知 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在名为 **config** 的新文件夹中生成 deployment.json 文件。然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将函数容器化，然后将代码推送到在初始化解决方案时指定的容器注册表。 

## <a name="view-your-container-image"></a>查看容器映像

将容器映像推送到容器注册表以后，Visual Studio Code 会输出一条成功消息。 若要自行确认操作是否成功，可以在注册表中查看映像。 

1. 在 Azure 门户中，浏览到 Azure 容器注册表。 
2. 选择“存储库”。
3. 列表中应会显示 **csharpfunction** 存储库。 选择此存储库可查看更多详细信息。
4. 在“标记”部分，应会显示 **0.0.1-amd64** 标记。 此标记指示生成的映像的版本和平台。 这些值在 CSharpFunction 文件夹的 module.json 文件中设置。 

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

可以使用 Azure 门户将函数模块部署到 IoT Edge 设备，就像在快速入门中所做的一样。 也可以在 Visual Studio Code 中部署和监视模块。 以下部分使用用于 VS Code 的 Azure IoT Edge 扩展，该扩展已在先决条件中列出。 如果尚未安装该扩展，现在请安装。 

1. 打开 VS Code 命令面板，方法是选择“视图” > “命令面板”。

2. 搜索并运行“Azure: 登录”命令。 按照说明登录 Azure 帐户。 

3. 在命令面板中，搜索并运行“Azure IoT 中心: 选择 IoT 中心”命令。 

4. 选择包含 IoT 中心的订阅，然后选择要访问的 IoT 中心。

5. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

6. 右键单击 IoT Edge 设备的名称，然后选择“为 IoT Edge 设备创建部署”。 

7. 浏览到包含 **CSharpFunction** 的解决方案文件夹。 打开 config 文件夹，选择 deployment.json 文件，然后选择“选择 Edge 部署清单”。

8. 刷新“Azure IoT 中心设备”部分。 此时会看到新的 **CSharpFunction** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

   ![在 VS Code 中查看部署的模块](./media/tutorial-deploy-function/view-modules.png)

## <a name="view-generated-data"></a>查看生成的数据

若要查看到达 IoT 中心的所有消息，可在命令面板中运行“Azure IoT 中心: 开始监视 D2C 消息”。

也可通过筛选视图来查看从特定设备到达 IoT 中心的所有消息。 右键单击“Azure IoT 中心设备”部分的设备，然后选择“开始监视 D2C 消息”。

若要停止监视消息，请在命令面板中运行“Azure IoT 中心: 停止监视 D2C 消息”命令。 


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)]

删除基于 IoT 设备平台（Linux 或 Windows）的 IoT Edge 服务运行时。

#### <a name="windows"></a>Windows

删除 IoT Edge 运行时。

```Powershell
stop-service iotedge -NoWait
sleep 5
sc.exe delete iotedge
```

删除在设备上创建的容器。 

```Powershell
docker rm -f $(docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

#### <a name="linux"></a>Linux

删除 IoT Edge 运行时。

```bash
sudo apt-get remove --purge iotedge
```

删除在设备上创建的容器。 

```bash
sudo docker rm -f $(sudo docker ps -a --no-trunc --filter "name=edge" --filter "name=tempSensor" --filter "name=CSharpFunction")
```

删除容器运行时。

```bash
sudo apt-get remove --purge moby
```



## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 Azure 函数模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 Azure 函数](how-to-develop-csharp-function.md)。 

继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [在 Azure 流分析中使用浮动窗口查找平均值](tutorial-deploy-stream-analytics.md)

<!--Links-->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
