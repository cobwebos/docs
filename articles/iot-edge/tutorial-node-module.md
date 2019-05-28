---
title: 教程：开发用于 Linux 的 Node.js 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 Node.js 代码创建 IoT Edge 模块并将其部署到边缘设备
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 2725f0a34ace3a353df5f746df299aeaf0ea92b3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574248"
---
# <a name="tutorial-develop-and-deploy-a-nodejs-iot-edge-module-for-linux-devices"></a>教程：为 Linux 设备开发和部署 Node.js IoT Edge 模块

使用 Visual Studio Code 开发 Node.js 代码并将其部署到运行 Azure IoT Edge 的 Linux 设备。 

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 将使用在快速入门中创建的模拟 IoT Edge 设备。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 创建 IoT Edge Node.js 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案适用范围

本教程演示了如何使用“Visual Studio Code”在“Node.js ”中开发模块，以及如何将其部署到“Linux 设备”    。 IoT Edge 不支持 Windows 设备的 Node.js 模块。

使用下表了解用于开发和部署 Node.js 模块的选项： 

| Node.js | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![在 Linux AMD64 上使用适用于 Node.js 模块的 VS Code](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![在 Linux ARM32 上使用适用于 Node.js 模块的 VS Code](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>先决条件

在开始本教程之前，你应该已经完成了上一个教程，为 Linux 容器开发设置了开发环境：[为 Linux 设备开发 IoT Edge 模块](tutorial-develop-for-linux.md)。 通过完成其中任何一个教程，你应该具备以下先决条件： 

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* [运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)
* 容器注册表，如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 使用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 配置的 [Visual Studio Code](https://code.visualstudio.com/)。
* [Docker CE](https://docs.docker.com/install/) 已配置为运行 Linux 容器。

要在 Node.js 中开发 IoT Edge 模块，请在开发计算机上安装以下附加先决条件： 

* [Node.js 和 npm](https://nodejs.org)。 npm 包是随 Node.js 分发的，也就是说，下载 Node.js 时，npm 会自动安装在计算机上。

## <a name="create-a-module-project"></a>创建模块项目
以下步骤介绍如何使用 Visual Studio Code 和 Azure IoT 工具来创建 IoT Edge Node.js 模块。

### <a name="create-a-new-project"></a>创建新项目

使用 **npm** 创建一个 Node.js 解决方案模板，以便在其上生成项目。 

1. 在 Visual Studio Code 中选择“视图”   >   “集成终端”，打开 VS Code 集成终端。

2. 在集成终端输入以下命令，以便安装 **yeoman** 以及适用于 Node.js Azure IoT Edge 模块的生成器： 

    ```cmd/sh
    npm install -g yo generator-azure-iot-edge-module
    ```

3. 选择“视图”   >   “命令面板”，打开 VS Code 命令面板。 

3. 在命令面板中，键入并运行 **Azure:Sign in** 命令并按照说明登录到 Azure 帐户。 如果已登录，则可跳过此步骤。

4. 在命令面板中，键入并运行 **Azure IoT Edge:New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“Node.js 模块”。  |
   | 提供模块名称 | 将模块命名为 **NodeModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br>最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/nodemodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-node-module/repository.png)


### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器存储库的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 

1. 在 VS Code 资源管理器中，打开 **.env** 文件。 
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 Node.js 模块。 需要选择针对每种解决方案的体系结构，因为容器是针对每种体系结构类型构建和运行的。 默认值为 Linux AMD64。 

1. 打开命令面板并搜索 **Azure IoT Edge：为边缘解决方案设置默认目标平台**，或选择窗口底部侧栏中的快捷方式图标。 

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

每个模板都附带示例代码，该代码从 **tempSensor** 模块提取模拟传感器数据并将其路由到 IoT 中心。 在此部分，请添加代码，以便让 NodeModule 分析消息，然后再发送它们。 

1. 在 VS Code 资源管理器中，打开 **modules** > **NodeModule** > **app.js**。

2. 在所需的节点模块下面添加温度阈值变量。 温度阈值设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。

    ```javascript
    var temperatureThreshold = 25;
    ```

3. 将整个 `PipeMessage` 函数替换为 `FilterMessage` 函数。
    
    ```javascript
    // This function filters out messages that report temperatures below the temperature threshold.
    // It also adds the MessageType property to the message with the value set to Alert.
    function filterMessage(client, inputName, msg) {
        client.complete(msg, printResultFor('Receiving message'));
        if (inputName === 'input1') {
            var message = msg.getBytes().toString('utf8');
            var messageBody = JSON.parse(message);
            if (messageBody && messageBody.machine && messageBody.machine.temperature && messageBody.machine.temperature > temperatureThreshold) {
                console.log(`Machine temperature ${messageBody.machine.temperature} exceeds threshold ${temperatureThreshold}`);
                var outputMsg = new Message(message);
                outputMsg.properties.add('MessageType', 'Alert');
                client.sendOutputEvent('output1', outputMsg, printResultFor('Sending received message'));
            }
        }
    }

    ```

4. 将函数名称 `pipeMessage` 替换为 `client.on()` 函数中的 `filterMessage`。

    ```javascript
    client.on('inputMessage', function (inputName, msg) {
        filterMessage(client, inputName, msg);
        });
    ```

5. 将以下代码片段复制到 `client.open()` 函数回调中，具体说来是在 `else` 语句中的 `client.on()` 后面。 更新所需属性时，会调用此函数。

    ```javascript
    client.getTwin(function (err, twin) {
        if (err) {
            console.error('Error getting twin: ' + err.message);
        } else {
            twin.on('properties.desired', function(delta) {
                if (delta.TemperatureThreshold) {
                    temperatureThreshold = delta.TemperatureThreshold;
                }
            });
        }
    });
    ```

6. 保存 app.js 文件。

7. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。

8. 将 NodeModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容： 

   ```json
     "NodeModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![将模块孪生添加到部署模板](./media/tutorial-node-module/module-twin.png)

9. 保存 deployment.template.json 文件。


## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 NodeModule，该函数会筛选出其中报告的计算机温度处于可接受限制范围内的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 打开 VS Code 集成终端，方法是选择“视图” > “终端”   。

1. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值  。
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会出现一条安全警告，其中建议使用 `--password-stdin`。 这条最佳做法是针对生产场景建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。  

   生成和推送命令启动三个操作。 首先，该命令将在解决方案中创建一个名为“config”的新文件夹，该文件夹包含完整的部署清单，该清单由部署模板和其他解决方案文件中的信息构建而成  。 其次，该命令将运行 `docker build` 以根据目标体系结构的相应 dockerfile 构建容器映像。 然后该命令将运行 `docker push` 以将映像存储库推送到容器注册表。

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Code 资源管理器和 Azure IoT Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为你的方案准备了部署清单，即 config 文件夹中的“deployment.json”文件  。 现在需要做的就是选择一个设备来接收部署。

确保 IoT Edge 设备已启动并运行。

1. 在 Visual Studio Code 资源管理器中，展开“Azure IoT 中心设备”部分以查看你的 IoT 设备列表  。

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

3. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。  不要使用 deployment.template.json 文件。

4. 单击“刷新”按钮。 此时会看到新的 **NodeModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态  。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。

1. 在 Visual Studio Code 资源管理器中，右键单击 IoT Edge 设备的名称，然后选择“开始监视 D2C 消息”  。

2. 查看到达 IoT 中心的消息。 消息可能需要一段时间才能到达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 NodeModule 代码所做的更改将等到机器温度达到 25 度时才发送消息。 它还将消息类型“Alert”添加到任何达到该温度阈值的消息中  。 


## <a name="edit-the-module-twin"></a>编辑模块孪生

我们使用部署清单中的 NodeModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio Code 中，展开 IoT Edge 设备下的详细信息以查看正在运行的模块。 

2. 右键单击“NodeModule”并选择“编辑模块孪生”   。 

3. 在所需属性中查找“TemperatureThreshold”  。 将其值更改为比最新报告温度高 5 度至 10 度的新温度。 

4. 保存该模块孪生文件。

5. 右键单击模块孪生编辑窗格中的任意位置，然后选择“更新模块孪生”  。 

5. 监控传入的设备到云的消息。 应发现停止发送消息，直到达到新的阙值温度。 

## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备后，可以详细了解如何[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio Code 开发模块](how-to-vs-code-develop-module.md)。 可以继续学习后续教程，了解如何借助 Azure IoT Edge 部署 Azure 云服务，在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义影像服务](tutorial-deploy-custom-vision.md)
