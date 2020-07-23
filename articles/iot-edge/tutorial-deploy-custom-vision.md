---
title: 教程 - 使用 Azure IoT Edge 将自定义视觉分类器部署到设备
description: 本教程介绍如何通过自定义视觉和 IoT Edge 使计算机视觉模型以容器方式运行。
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/15/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 07350ffe4a57bfe4a79bfce5d821b51535867935
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "76166999"
---
# <a name="tutorial-perform-image-classification-at-the-edge-with-custom-vision-service"></a>教程：在边缘使用自定义视觉服务进行图像分类

Azure IoT Edge 可以将工作负荷从云移到边缘，让 IoT 解决方案更高效。 此功能非常适用于处理大量数据的服务，例如计算机视觉模型。 [自定义视觉服务](../cognitive-services/custom-vision-service/home.md)用于生成自定义图像分类器并将其作为容器部署到设备。 将这两项服务一起使用可以对图像或视频流进行分析，不需先将所有数据从站点转移出来。 自定义视觉提供一个可以将图像与训练的模型进行比较的分类器，以便生成见解。

例如，IoT Edge 设备上的自定义视觉可以确定某条高速公路的交通流量是高于还是低于正常，或者某个室内停车场是否有连续的停车位。 这些见解可以与其他服务共享，方便采取行动。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用自定义视觉生成图像分类器。
> * 开发用于在设备上查询自定义视觉 Web 服务器的 IoT Edge 模块。
> * 将图像分类器的结果发送到 IoT 中心。

<center>

![示意图 - 架构、阶段和部署分类器教程](./media/tutorial-deploy-custom-vision/custom-vision-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

>[!TIP]
>本教程是 [Raspberry Pi 3 上的自定义视觉和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi) 示例项目的简化版本。 本教程旨在在云 VM 上运行，并使用静态图像训练和测试图像分类器，这对于刚开始评估 IoT Edge 上的自定义视觉的用户非常有用。 该示例项目使用物理硬件并设置一个实时相机源，用于训练和测试图像分类器，这对于想要尝试更详细的真实应用场景的用户非常有用。

在开始学习本教程之前，应已完成上一篇教程，了解如何设置用于开发 Linux 容器的环境：[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)。 完成该教程后，应已准备好以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

要使用自定义视觉设备开发 IoT Edge 模块，请在开发计算机上安装下述额外的必备组件：

* [Python](https://www.python.org/downloads/)
* [Git](https://git-scm.com/downloads)
* [适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

## <a name="build-an-image-classifier-with-custom-vision"></a>使用自定义视觉生成图像分类器

若要生成图像分类器，需创建自定义视觉项目并提供训练图像。 若要详细了解在此部分执行的步骤，请参阅[如何使用自定义视觉生成分类器](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md)。

生成并训练图像分类器以后，即可将它作为 Docker 容器导出并部署到 IoT Edge 设备。

### <a name="create-a-new-project"></a>创建新项目

1. 在 Web 浏览器中，导航到[自定义影像服务网页](https://customvision.ai/)。

2. 选择“登录”，使用访问 Azure 资源时所用的帐户进行登录。 

3. 选择“新建项目”。 

4. 使用以下值来创建项目：

   | 字段 | 值 |
   | ----- | ----- |
   | 名称 | 为项目提供一个名称，例如 **EdgeTreeClassifier**。 |
   | 说明 | 可选项目说明。 |
   | 资源 | 选择一个包含自定义视觉服务资源的 Azure 资源组，或者如果尚未添加 Azure 资源组，则选择“新建”  。 |
   | 项目类型 | **分类** |
   | 分类类型 | **多类(一个图像一个标记)** |
   | 域 | **常规(精简)** |
   | 导出功能 | **基本平台（Tensorflow、CoreML、ONNX 等）** |

5. 选择“创建项目”。 

### <a name="upload-images-and-train-your-classifier"></a>上传图像并训练分类器

创建图像分类器需要一组训练图像和测试图像。

1. 将示例图像从 [Cognitive-CustomVision-Windows](https://github.com/Microsoft/Cognitive-CustomVision-Windows) 存储库克隆或下载到本地开发计算机。

   ```cmd/sh
   git clone https://github.com/Microsoft/Cognitive-CustomVision-Windows.git
   ```

2. 返回到自定义视觉项目，选择“添加图像”。 

3. 浏览到已克隆到本地的 git 存储库，导航到第一个图像文件夹 **Cognitive-CustomVision-Windows / Samples / Images / Hemlock**。 选择文件夹中的所有 10 个图像，然后单击“打开”。 

4. 将标记“铁杉”  添加到此组图像，然后按 **Enter** 应用该标记。

5. 选择“上传 10 个文件”。 

   ![将 hemlock 标记的文件上传到自定义视觉](./media/tutorial-deploy-custom-vision/upload-hemlock.png)

6. 成功上传图像以后，选择“完成”。 

7. 再次选择“添加图像”。 

8. 浏览到第二个图像文件夹 **Cognitive-CustomVision-Windows / Samples / Images / Japanese Cherry**。 选择文件夹中的所有 10 个图像，然后单击“打开”。 

9. 将标记“日本樱花”  添加到此组图像，然后按 **Enter** 应用该标记。

10. 选择“上传 10 个文件”。  成功上传图像以后，选择“完成”。 

11. 将两组图像标记并上传后，请选择“训练”，对分类器进行训练。 

### <a name="export-your-classifier"></a>导出分类器

1. 训练分类器以后，请在分类器的“性能”页上选择“导出”。 

   ![导出已训练的图像分类器](./media/tutorial-deploy-custom-vision/export.png)

2. 选择“DockerFile”  作为平台。 

3. 选择“Linux”  作为版本。  

4. 选择“导出”。  

   ![导出为带 Linux 容器的 DockerFile](./media/tutorial-deploy-custom-vision/export-2.png)

5. 导出完成后，选择“下载”，将  .zip 包保存到本地计算机。 从包中提取所有文件。 将使用这些文件创建一个包含图像分类服务器的 IoT Edge 模块。 

此时，你已完成自定义视觉项目的创建和训练。 将在下一部分使用导出的文件，但现在已完成自定义视觉网页的操作。 

## <a name="create-an-iot-edge-solution"></a>创建 IoT Edge 解决方案

现在，开发计算机上有了适用于容器版图像分类器的文件。 在此部分，请将图像分类器容器配置为以 IoT Edge 模块方式运行。 此外还创建另一个将与图像分类器一起部署的模块。 第二个模块将请求发布到分类器，并将结果以消息形式发送到 IoT 中心。 

### <a name="create-a-new-solution"></a>创建新的解决方案

可以通过解决方案以逻辑方式为单个 IoT Edge 部署开发和组织多个模块。 解决方案包含适用于一个或多个模块的代码，另外还包含部署清单，用于声明如何在 IoT Edge 设备上配置模块。 

1. 选择“视图”   >   “命令面板”，打开 VS Code 命令面板。 

1. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。 在命令面板中提供以下信息，以便创建解决方案： 

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称（例如 **CustomVisionSolution**），或者接受默认名称。 |
   | 选择模块模板 | 选择“Python 模块”。  |
   | 提供模块名称 | 将模块命名为 **classifier**。<br><br>必须确保此模块名称为小写。 IoT Edge 在引用模块时区分大小写，而此解决方案使用的库会将所有请求格式化为小写。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是在上一步预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。<br><br>最终的字符串看起来类似于 \<注册表名称\>.azurecr.io/classifier  。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-deploy-custom-vision/repository.png)

Visual Studio Code 窗口会加载 IoT Edge 解决方案工作区。

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。

1. 在 VS Code 资源管理器中，打开 .env 文件。
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。
3. 保存此文件。

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发模块。 需要选择面向每个解决方案的体系结构，因为每种体系结构类型的容器的生成和运行方式均不相同。 默认值为 Linux AMD64，我们将在本教程中使用此功能。 

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。 

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。 

### <a name="add-your-image-classifier"></a>添加图像分类器

Visual Studio Code 中的 Python 模块模板包含一些可以在运行后对 IoT Edge 进行测试的示例代码。 在本方案中不需使用该代码。 请按照此部分的步骤将示例代码替换为以前导出的图像分类器容器。 

1. 在文件资源管理器中，浏览到已下载并提取的自定义视觉包。 复制已提取包中的所有内容。 其中应该有两个文件夹（**app** 和 **azureml**）和两个文件（**Dockerfile** 和 **README**）。 

2. 在文件资源管理器中，浏览到曾经要求 Visual Studio Code 在其中创建 IoT Edge 解决方案的目录。 

3. 打开 classifier 模块文件夹。 如果在上一部分使用了建议的名称，则文件夹结构将类似于 **CustomVisionSolution / modules / classifier**。 

4. 将文件粘贴到 **classifier** 文件夹中。 

5. 返回到 Visual Studio Code 窗口。 解决方案工作区现在会显示模块文件夹中的图像分类器文件。 

   ![包含图像分类器文件的解决方案工作区](./media/tutorial-deploy-custom-vision/workspace.png)

6. 打开 classifier 文件夹中的 **module.json** 文件。 

7. 更新 **platforms** 参数，使其指向你添加的新 Dockerfile，并删除 AMD64 之外的所有选项，这是我们在本教程中唯一使用的体系结构。 

   ```json
   "platforms": {
       "amd64": "./Dockerfile"
   }
   ```

8. 保存所做更改。 

### <a name="create-a-simulated-camera-module"></a>创建模拟摄像头模块

在实际的自定义视觉部署中，会有一个提供实时图像或视频流的摄像头。 在本方案中，请生成一个可以向图像分类器发送测试图像的模块来模拟摄像头。 

#### <a name="add-and-configure-a-new-module"></a>添加并配置新模块

在此部分，请将新模块添加到同一 CustomVisionSolution 并提供创建模拟摄像头所需的代码。 

1. 在同一 Visual Studio Code 窗口中，使用命令面板运行 **Azure IoT Edge:** Add IoT Edge Module”。 在命令面板中提供新模块所需的以下信息： 

   | Prompt | 值 | 
   | ------ | ----- |
   | 选择部署模板文件 | 选择 CustomVisionSolution 文件夹中的 deployment.template.json 文件。 |
   | 选择模块模板 | 选择“Python 模块”  |
   | 提供模块名称 | 将模块命名为 **cameraCapture** |
   | 为模块提供 Docker 映像存储库 | 将 **localhost:5000** 替换为 Azure 容器注册表的登录服务器值。<br><br>最终的字符串看起来类似于 **\<registryname\>.azurecr.io/cameracapture**。 |

   VS Code 窗口会加载解决方案工作区中的新模块，并会更新 deployment.template.json 文件。 现在会看到两个模块文件夹：classifier 和 cameraCapture。 

2. 打开 **modules** / **cameraCapture** 文件夹中的 **main.py** 文件。 

3. 将整个文件替换为以下代码。 此示例代码将 POST 请求发送到在 classifier 模块中运行的图像处理服务。 我们提供的此模块容器包含可在请求中使用的示例图像。 此示例代码随后将响应打包为 IoT 中心消息并发送到输出队列。  

    ```python
    # Copyright (c) Microsoft. All rights reserved.
    # Licensed under the MIT license. See LICENSE file in the project root for
    # full license information.

    import time
    import sys
    import os
    import requests
    import json
    from azure.iot.device import IoTHubModuleClient, Message

    # global counters
    SENT_IMAGES = 0

    # global client
    CLIENT = None

    # Send a message to IoT Hub
    # Route output1 to $upstream in deployment.template.json
    def send_to_hub(strMessage):
        message = Message(bytearray(strMessage, 'utf8'))
        CLIENT.send_message_to_output(message, "output1")
        global SENT_IMAGES
        SENT_IMAGES += 1
        print( "Total images sent: {}".format(SENT_IMAGES) )

    # Send an image to the image classifying server
    # Return the JSON response from the server with the prediction result
    def sendFrameForProcessing(imagePath, imageProcessingEndpoint):
        headers = {'Content-Type': 'application/octet-stream'}

        with open(imagePath, mode="rb") as test_image:
            try:
                response = requests.post(imageProcessingEndpoint, headers = headers, data = test_image)
                print("Response from classification service: (" + str(response.status_code) + ") " + json.dumps(response.json()) + "\n")
            except Exception as e:
                print(e)
                print("No response from classification service")
                return None

        return json.dumps(response.json())

    def main(imagePath, imageProcessingEndpoint):
        try:
            print ( "Simulated camera module for Azure IoT Edge. Press Ctrl-C to exit." )

            try:
                global CLIENT
                CLIENT = IoTHubModuleClient.create_from_edge_environment()
            except Exception as iothub_error:
                print ( "Unexpected error {} from IoTHub".format(iothub_error) )
                return

            print ( "The sample is now sending images for processing and will indefinitely.")

            while True:
                classification = sendFrameForProcessing(imagePath, imageProcessingEndpoint)
                if classification:
                    send_to_hub(classification)
                time.sleep(10)

        except KeyboardInterrupt:
            print ( "IoT Edge module sample stopped" )

    if __name__ == '__main__':
        try:
            # Retrieve the image location and image classifying server endpoint from container environment
            IMAGE_PATH = os.getenv('IMAGE_PATH', "")
            IMAGE_PROCESSING_ENDPOINT = os.getenv('IMAGE_PROCESSING_ENDPOINT', "")
        except ValueError as error:
            print ( error )
            sys.exit(1)

        if ((IMAGE_PATH and IMAGE_PROCESSING_ENDPOINT) != ""):
            main(IMAGE_PATH, IMAGE_PROCESSING_ENDPOINT)
        else: 
            print ( "Error: Image path or image-processing endpoint missing" )
    ```

4. 保存 **main.py** 文件。 

5. 打开 **requrements.txt** 文件。 

6. 为将要包括在容器中的库添加新的一行。

   ```Text
   requests
   ```

7. 保存 **requirements.txt** 文件。


#### <a name="add-a-test-image-to-the-container"></a>向容器添加测试图像

我们将使用单个测试图像，而不会通过实际摄像头来提供此方案的图像源。 测试图像包含在 GitHub 存储库中，你在本教程前面已下载适用于训练图像的该库。 

1. 导航到 **Cognitive-CustomVision-Windows** / **Samples** / **Images** / **Test** 中的测试图像。 

2. 复制 **test_image.jpg** 

3. 浏览到 IoT Edge 解决方案目录，将测试图像粘贴到 **modules** / **cameraCapture** 文件夹中。 该图像应该置于你在上一部分编辑的 main.py 文件所在的文件夹中。 

4. 在 Visual Studio Code 中打开适用于 cameraCapture 模块的 **Dockerfile.amd64** 文件。

5. 在建立工作目录 `WORKDIR /app` 的行后面，添加以下代码行：

   ```Dockerfile
   ADD ./test_image.jpg .
   ```

6. 保存 Dockerfile。

### <a name="prepare-a-deployment-manifest"></a>准备部署清单

到目前为止，你已在本教程中训练了一个用于对树的图像分类的自定义视觉模型，并且已将该模型打包成一个 IoT Edge 模块。 然后，你创建了另一个模块，该模块可以查询图像分类服务器并将其结果报告回 IoT 中心。 现在可以创建部署清单了。该清单会告知 IoT Edge 设备如何同时启动并运行这两个模块。 

适用于 Visual Studio Code 的 IoT Edge 扩展在每个 IoT Edge 解决方案中提供了一个模板，用于创建部署清单。 

1. 在解决方案文件夹中打开 **deployment.template.json** 文件。 

2. 找到 **modules** 节，其中应包含三个模块：两个你创建的，即 classifier 和 cameraCapture，第三个是默认包含的，即 SimulatedTemperatureSensor。 

3. 删除 **SimulatedTemperatureSensor** 模块及其所有参数。 包括此模块是为了提供测试方案的示例数据，但我们在此部署中不需要它。 

4. 如果为图像分类模块提供了 **classifier** 之外的名称，现在请检查该名称，确保其采用全小写形式。 cameraCapture 模块调用 classifier 模块时使用的请求库可以将所有请求格式化为小写，而 IoT Edge 区分大小写。 

5. 使用以下 JSON 更新 cameraCapture 模块的 **createOptions** 参数。 此信息在模块容器中创建环境变量，该容器是在 main.py 进程中检索的。 将此信息包含在部署清单中即可更改图像或终结点，不需重新生成模块图像。 

    ```json
    "createOptions": "{\"Env\":[\"IMAGE_PATH=test_image.jpg\",\"IMAGE_PROCESSING_ENDPOINT=http://classifier/image\"]}"
    ```

    如果为自定义视觉模块提供了 *classifier* 之外的名称，请更新图像处理终结点值，使之匹配。 

6. 在文件底部，更新 $edgeHub 模块的 **routes** 参数。 你希望将预测结果从 cameraCapture 路由到 IoT 中心。

    ```json
        "routes": {
          "CameraCaptureToIoTHub": "FROM /messages/modules/cameraCapture/outputs/* INTO $upstream"
        },
    ```

    如果为第二个模块提供了 *cameraCapture* 之外的名称，请更新路由值，使之匹配。 

7. 保存 **deployment.template.json** 文件。

## <a name="build-and-deploy-your-iot-edge-solution"></a>生成并部署 IoT Edge 解决方案

创建两个模块并配置部署清单模板以后，即可生成容器映像并将其推送到容器注册表。 

映像位于注册表中以后，即可将解决方案部署到 IoT Edge 设备。 可以通过 IoT 中心设置设备上的模块，但是也可以通过 Visual Studio Code 访问 IoT 中心和设备。 在此部分，请先设置对 IoT 中心的访问权限，然后使用 VS Code 将解决方案部署到 IoT Edge 设备。

首先，生成解决方案并将其推送到容器注册表。 

1. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。   可以在 VS Code 的集成终端中观察此操作的进度。 
2. 请注意，已为解决方案添加了一个新文件夹：**config**。展开该文件夹，打开其中的 **deployment.json** 文件。
3. 查看 deployment.json 文件中的信息。 系统会自动根据已配置的部署模板文件以及解决方案中的信息（包括 .env 文件和 module.json 文件）创建（或更新）deployment.json 文件。 

然后选择设备并部署解决方案。

1. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。  
2. 右键单击要将其作为部署目标的设备，然后选择“为单个设备创建部署”。  
3. 在文件资源管理器中导航到解决方案中的 **config** 文件夹，然后选择 **deployment.json**。 单击“选择 Edge 部署清单”。  

如果部署成功，则会在 VS Code 输出中输出确认消息。 在 VS Code 资源管理器中，展开用于此部署的 IoT Edge 设备的详细信息。 如果模块没有立即显示，请将光标悬停在“Azure IoT 中心设备”标题上，以便启用刷新按钮。  模块启动并将信息报告回 IoT 中心可能需要一些时间。 

也可查看设备本身上的所有模块是否都已启动并运行。 在 IoT Edge 设备上运行以下命令，以便查看模块的状态。 模块启动可能需要一些时间。

   ```bash
   iotedge list
   ```

## <a name="view-classification-results"></a>查看分类结果

可以通过两种方式来查看模块的结果，一种是生成和发送消息时在设备本身上查看，另一种是在消息到达 IoT 中心时通过 Visual Studio Code 查看。 

通过设备查看 cameraCapture 模块的日志，了解发送消息以后系统确认 IoT 中心接收到消息这一过程。 

   ```bash
   iotedge logs cameraCapture
   ```

在 Visual Studio Code 中右键单击 IoT Edge 设备的名称，选择“开始监视内置事件终结点”。  

自定义视觉模块中的结果是从 cameraCapture 模块作为消息发送的，其中包括图像为铁杉树或樱花树的概率。 由于图像为铁杉，因此会看到概率为 1.0。 

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你训练了一个自定义视觉模型并将其作为模块部署到了 IoT Edge 设备中。 然后，你生成了一个模块，该模块可以查询图像分类服务并将其结果报告回 IoT 中心。 

若要尝试此方案的更深入的版本（带实时摄像头源），请参阅 GitHub 项目：[Custom Vision and Azure IoT Edge on a Raspberry Pi 3](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)（Raspberry Pi 3 上的自定义视觉和 Azure IoT Edge）。 

继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)
