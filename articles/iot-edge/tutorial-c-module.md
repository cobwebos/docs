---
title: 教程：开发适用于 Linux 的 C 模块 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 C 代码创建 IoT Edge 模块并将其部署到运行 IoT Edge 的 Linux 设备
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 63169423e757f3e1e73a95a1523d74c8fc59b2b2
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835128"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-linux-devices"></a>教程：开发适用于 Linux 设备的 C IoT Edge 模块

使用 Visual Studio Code 开发 C 代码并将其部署到运行 Azure IoT Edge 的 Linux 设备。 

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Visual Studio Code 在 C 中创建 IoT Edge 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到容器注册表
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案作用域

本教程演示如何使用 **Visual Studio Code** 在 **C** 中开发模块，以及如何将其部署到 **Linux 设备**。 如果要开发适用于 Windows 设备的模块，请转到[开发适用于 Windows 设备的 C IoT Edge 模块](tutorial-c-module-windows.md)。

使用下表了解开发 C 模块并将其部署到 Linux 的选项： 

| C | Visual Studio Code | Visual Studio | 
| - | ------------------ | ------------- |
| **Linux AMD64** | ![在 Linux AMD64 上使用适用于 C 模块的 VS Code](./media/tutorial-c-module/green-check.png) | ![在 Linux AMD64 上使用适用于 C 模块的 VS](./media/tutorial-c-module/green-check.png) |
| **Linux ARM32** | ![在 Linux ARM32 上使用适用于 C 模块的 VS Code](./media/tutorial-c-module/green-check.png) | ![在 Linux ARM32 上使用适用于 C 模块的 VS](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>先决条件

在开始本教程之前，你应该已经完成了上一个教程，为 Linux 容器开发设置了开发环境：[开发适用于 Linux 设备的 IoT Edge 模块](tutorial-develop-for-linux.md)。 通过完成该教程，你应该具备以下系统必备组件： 

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* [运行 Azure IoT Edge 的 Linux 设备](quickstart-linux.md)
* 容器注册表，如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 使用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 配置的 [Visual Studio Code](https://code.visualstudio.com/)。
* 配置为运行 Linux 容器的 [Docker CE](https://docs.docker.com/install/)。

若要在 C 中开发 IoT Edge 模块，请在开发计算机上安装以下附加系统必备组件： 

* 用于 Visual Studio Code 的 [C/C++ 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。

## <a name="create-a-module-project"></a>创建模块项目

以下步骤使用 Visual Studio Code 和 Azure IoT Tools 扩展为 C 创建 IoT Edge 模块项目。 创建项目模板后，添加新代码，以便模块根据报告的属性筛选出消息。 

### <a name="create-a-new-project"></a>创建新项目

创建可以使用自己的代码进行自定义的 C 解决方案模板。

1. 选择“视图”   >   “命令面板”，打开 VS Code 命令面板。

2. 在命令面板中，键入并运行 **Azure:Sign in** 命令并按照说明登录到 Azure 帐户。 如果已登录，则可跳过此步骤。

3. 在命令面板中，键入并运行 **Azure IoT Edge:New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“C 模块”。  |
   | 提供模块名称 | 将模块命名为 **CModule**。 |
   | 为模块提供 Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 容器映像是基于你在上一步中提供的名称预先填充的。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br> 最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/cmodule。 |
 
   ![提供 Docker 映像存储库](./media/tutorial-c-module/repository.png)

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。

1. 在 VS Code 资源管理器中，打开 .env 文件。
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。
3. 保存此文件。

### <a name="select-your-target-architecture"></a>选择目标体系结构

目前，Visual Studio Code 可以为 Linux AMD64 和 Linux ARM32v7 设备开发 C 模块。 你需要选择每种解决方案的目标体系结构，因为容器是针对每种体系结构类型以不同的方式生成和运行的。 默认值为 Linux AMD64。 

1. 打开命令面板并搜索 **Azure IoT Edge:Set Default Target Platform for Edge Solution**，或选择窗口底部侧栏中的快捷方式图标。 

2. 在命令面板中，从选项列表中选择目标体系结构。 在本教程中，我们使用 Ubuntu 虚拟机作为 IoT Edge 设备，因此将保留默认的“amd64”  。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

默认模块代码在输入队列上接收消息，并通过输出队列传递消息。 让我们添加一些额外的代码，以便模块在将边缘的消息转发到 IoT 中心之前对其进行处理。 更新模块，以便分析每条消息中的温度数据，并且只有在温度超过特定阈值时才将消息发送到 IoT 中心。 

1. 在此场景中，来自传感器的数据采用 JSON 格式。 若要筛选 JSON 格式的消息，请导入用于 C 的 JSON 库。本教程使用 Parson。

   1. 下载 [Parson GitHub 存储库](https://github.com/kgabis/parson)。 将 **parson.c** 和 **parson.h** 文件复制到 **CModule** 文件夹中。

   2. 打开“模块”   > “CModule”   >   “CMakeLists.txt”。 在文件顶部，导入名为 **my_parson** 的充当库的 Parson 文件。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 将 **my_parson** 添加到 CMakeLists.txt 的 **target_link_libraries** 函数中的库列表。

   4. 保存 **CMakeLists.txt** 文件。

   5. 打开“模块”   > “CModule”   >   “main.c”。 在 include 语句列表的底部，添加一个新的语句，以便包括适用于 JSON 支持的 `parson.h`：

      ```c
      #include "parson.h"
      ```

1. 在 **main.c** 文件中，在 include 节后面添加名为 `temperatureThreshold` 的全局变量。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。

    ```c
    static double temperatureThreshold = 25;
    ```

1. 在 main.c 中找到 `CreateMessageInstance` 函数。 使用以下代码替换内部 if-else 语句，该代码添加了几行功能： 

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   else 语句中的新代码行为消息添加了一个新属性，该属性将消息标记为警报。 此代码将所有消息均标记为警报，因为我们将添加仅在消息报告高温时才将其发送到 IoT 中心的功能。 

1. 将整个 `InputQueue1Callback` 函数替换为以下代码。 此函数实现实际的消息传送筛选器。 收到消息后，它会检查报告的温度是否超过阈值。 如果超过，它会通过其输出队列转发消息。 如果未超过，则会忽略该消息。 

    ```c
    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) != IOTHUB_MESSAGE_OK)
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. 添加 `moduleTwinCallback` 函数。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. 找到 `SetupCallbacksForModule` 函数。 使用以下代码替换该函数，该代码添加了 **else if** 语句来检查模块孪生是否已更新。

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = __FAILURE__;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. 保存 main.c 文件。

1. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 

1. 将 CModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容：

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![将 CModule 孪生添加到部署模板](./media/tutorial-c-module/module-twin.png)

1. 保存 **deployment.template.json** 文件。

## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 CModule，该函数会筛选出其中报告的计算机温度处于可接受限制范围内的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 打开 VS Code 终端，方法是选择“视图” > “终端”   。

1. 在终端中输入以下命令，以登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值  。
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。  

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Code 资源管理器和 Azure IoT Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为你的方案准备了部署清单，即 config 文件夹中的 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

确保 IoT Edge 设备已启动并运行。 

1. 在 Visual Studio Code 资源管理器中，展开“Azure IoT 中心设备”部分以查看你的 IoT 设备列表  。

2. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

3. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。  不要使用 deployment.template.json 文件。

4. 单击“刷新”按钮。 此时会看到新的 **CModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以通过 Visual Studio Code 资源管理器的“Azure IoT 中心设备”部分查看 IoT Edge 设备的状态  。 展开设备的详细信息，可以看到已部署的正在运行的模块的列表。

1. 在 Visual Studio Code 资源管理器中，右键单击 IoT Edge 设备的名称，然后选择“开始监视 D2C 消息”  。

2. 查看到达 IoT 中心的消息。 消息可能需要一段时间才能到达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 CModule 代码所做的更改将等到计算机温度达到 25 度时才发送消息。 它还将消息类型“Alert”添加到任何达到该温度阈值的消息中  。 

   ![查看到达 IoT 中心的消息](./media/tutorial-c-module/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>编辑模块孪生

我们使用部署清单中的 CModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio Code 中，展开 IoT Edge 设备下的详细信息以查看正在运行的模块。 

2. 右键单击“CModule”并选择“编辑模块孪生”   。 

3. 在所需属性中找到“TemperatureThreshold”  。 将其值更改为比最新报告温度高 5 度至 10 度的新温度。 

4. 保存该模块孪生文件。

5. 右键单击模块孪生编辑窗格中的任意位置，然后选择“更新模块孪生”  。 

5. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。 

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备后，即可详细了解[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio Code 开发模块](how-to-vs-code-develop-module.md)。 可以继续学习后续教程，了解 Azure IoT Edge 如何帮助你部署 Azure 云服务以处理和分析边缘数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)

