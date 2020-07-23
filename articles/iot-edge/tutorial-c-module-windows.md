---
title: 有关开发适用于 Windows 的 C 模块的教程 - Azure IoT Edge | Microsoft Docs
description: 本教程介绍如何使用 C 代码创建 IoT Edge 模块并将其部署到运行 IoT Edge 的 Windows 设备
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 09d039801107a44df4f3bf3745a1e074e6d708b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "76760958"
---
# <a name="tutorial-develop-a-c-iot-edge-module-for-windows-devices"></a>教程：开发适用于 Windows 设备的 C IoT Edge 模块

使用 Visual Studio 开发 C 代码并将其部署到运行 Azure IoT Edge 的 Windows 设备。

可以使用 Azure IoT Edge 模块部署代码，直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 使用 Visual Studio 创建基于 C SDK 的 IoT Edge 模块。
> * 使用 Visual Studio 和 Docker 创建 Docker 映像并将其发布到注册表。
> * 将模块部署到 IoT Edge 设备。
> * 查看生成的数据。

在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>解决方案适用范围

本教程演示如何使用 **Visual Studio 2019** 在 **C** 中开发模块，以及如何将其部署到 **Windows 设备**。 若要开发适用于 Linux 设备的模块，请转到[开发适用于 Linux 设备的 C IoT Edge 模块](tutorial-c-module.md)。

使用下表了解开发 C 模块并将其部署到 Windows 设备的选项：

| C | Visual Studio Code | Visual Studio 2017/2019 |
| -- | ------------------ | ------------------ |
| **Windows AMD64** |  | ![在 Visual Studio 中开发 WinAMD64 的 C 模块](./media/tutorial-c-module/green-check.png) |

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，应已完成上一篇教程来设置用于开发 Windows 容器的开发环境：[开发适用于 Windows 设备的 IoT Edge 模块](tutorial-develop-for-windows.md)。 完成该教程后，应已准备好以下必备组件：

* Azure 中的免费或标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个[运行 Azure IoT Edge 的 Windows 设备](quickstart.md)。
* 一个容器注册表，例如 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)。
* 配置了 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) 扩展的 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio)。
* 配置为运行 Windows 容器的 [Docker Desktop](https://docs.docker.com/docker-for-windows/install/)。
* 通过 vcpkg 安装适用于 Windows x64 的 Azure IoT C SDK：

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> 如果使用的是 Visual Studio 2017（版本 15.7 或更高版本），请从 Visual Studio 市场下载并安装适用于 VS 2017 的 [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)

## <a name="create-a-module-project"></a>创建模块项目

以下步骤使用 Visual Studio 和 Azure IoT Edge 工具扩展创建基于 C SDK 的 IoT Edge 模块项目。 创建项目模板后，请添加新代码，使模块根据其报告属性筛选出消息。

### <a name="create-a-new-project"></a>创建新项目

创建可以使用自己的代码进行自定义的 C 解决方案模板。

1. 启动 Visual Studio 2019 并选择“创建新项目”  。

2. 搜索“IoT Edge”  ，然后选择“Azure IoT Edge (Windows amd64)”项目  。 单击“下一步”。 

   ![创建新的 Azure IoT Edge 项目](./media/tutorial-c-module-windows/new-project.png)

3. 重命名项目和解决方案，使名称具有描述性，例如 **CTutorialApp**。 单击“创建”以创建项目。 

   ![配置新的 Azure IoT Edge 项目](./media/tutorial-c-module-windows/configure-project.png)

4. 使用以下值来配置项目：

   | 字段 | 值 |
   | ----- | ----- |
   | 选择模板 | 选择“C 模块”  。 |
   | 模块项目名称 | 将模块命名为 **CModule**。 |
   | Docker 映像存储库 | 映像存储库包含容器注册表的名称和容器映像的名称。 系统已基于模块项目名称值预先填充容器映像。 将 **localhost:5000** 替换为 Azure 容器注册表中的登录服务器值。 可以在 Azure 门户的容器注册表的“概览”页中检索登录服务器。 <br><br> 最终的映像存储库看起来类似于 \<registry name\>.azurecr.io/cmodule。 |

   ![针对目标设备、模块类型和容器注册表配置项目](./media/tutorial-c-module-windows/add-application-and-module.png)

5. 选择“添加”  以创建项目。

### <a name="add-your-registry-credentials"></a>添加注册表凭据

部署清单与 IoT Edge 运行时共享容器注册表的凭据。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 使用 Azure 容器注册表的“访问密钥”部分提供的凭据。 

1. 在 Visual Studio 解决方案资源管理器中打开 **deployment.template.json** 文件。

2. 在 $edgeAgent 所需属性中找到 **registryCredentials** 属性。 它应该会根据你在创建项目时提供的信息自动填写注册表地址。 用户名和密码字段应包含变量名称。 例如：

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

3. 打开模块解决方案中的 **.env** 文件。 （默认情况下，此文件会隐藏在解决方案资源管理器中，可能需要选择“显示所有文件”按钮才能显示它。）  该 .env 文件应包含与你在 deployment.template.json 文件中看到的相同的用户名和密码变量。

4. 从 Azure 容器注册表中添加“用户名”和“密码”值。  

5. 将更改保存到 .env 文件。

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

默认模块代码在输入队列上接收消息，并通过输出队列传递消息。 让我们添加一些额外的代码，以便模块在将消息转发到 IoT 中心之前，在边缘处理消息。 更新模块，以便分析每条消息中的温度数据，并且只有在温度超过特定阈值时才将消息发送到 IoT 中心。

1. 在此场景中，来自传感器的数据采用 JSON 格式。 若要筛选 JSON 格式的消息，请导入用于 C 的 JSON 库。本教程使用 Parson。

   1. 下载 [Parson GitHub 存储库](https://github.com/kgabis/parson)。 将 **parson.c** 和 **parson.h** 文件复制到 **CModule** 项目中。

   2. 在 Visual Studio 中，打开 CModule 项目文件夹中的 **CMakeLists.txt** 文件。 在文件顶部，导入名为 **my_parson** 的充当库的 Parson 文件。

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 将 `my_parson` 添加到 CMakeLists.txt 文件的 target_link_libraries 节中的库列表  。

   4. 保存 **CMakeLists.txt** 文件。

   5. 打开“CModule” > “main.c”。   在 include 语句列表的底部，添加一个新的语句，以便包括适用于 JSON 支持的 `parson.h`：

      ```c
      #include "parson.h"
      ```

2. 在 **main.c** 文件中，在 messagesReceivedByInput1Queue 变量的旁边添加名为 `temperatureThreshold` 的全局变量。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。

    ```c
    static double temperatureThreshold = 25;
    ```

3. 在 main.c 中找到 `CreateMessageInstance` 函数。 将内部的 if-else 语句替换为以下代码，以添加几行功能：

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

   else 语句中的新代码行将一个新属性添加到消息，用于将消息标记为警报。 此代码将所有消息标记为警报，因为仅当报告了较高的温度时，我们要添加的功能才会向 IoT 中心发送消息。

4. 找到 `InputQueue1Callback` 函数，并将整个函数替换为以下代码。 此函数实现实际的消息传送筛选器。 收到消息后，它会检查报告的温度是否超过阈值。 如果超过了阈值，则通过其输出队列转发消息。 如果未超过阈值，则忽略消息。

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check if the message reports temperatures higher than the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds threshold, send to output1
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
        // If message does not exceed threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

5. 添加 `moduleTwinCallback` 函数。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
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

6. 找到 `SetupCallbacksForModule` 函数。 将该函数替换为以下代码，以添加 **else if** 语句来检查模块孪生是否已更新。

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

7. 保存 main.c 文件。

8. 打开 **deployment.template.json** 文件。

9. 将 CModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容：

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![将 CModule 孪生添加到部署模板](./media/tutorial-c-module-windows/module-twin.png)

10. 保存 **deployment.template.json** 文件。

## <a name="build-and-push-your-module"></a>生成并推送模块

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 **CModule**，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。

1. 在开发计算机上使用以下命令登录到 Docker。 使用 Azure 容器注册表中的用户名、密码和登录服务器登录。 可以在 Azure 门户中从注册表的“访问密钥”部分检索这些值。 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   可能会收到一条安全警告，推荐使用 `--password-stdin`。 这条最佳做法是针对生产方案建议的，这超出了本教程的范畴。 有关详细信息，请参阅 [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) 参考。

2. 在 Visual Studio 解决方案资源管理器中，右键单击要生成的项目名称。 默认名称为 **AzureIotEdgeApp1**；由于生成的是 Windows 模块，因此扩展名应是 **Windows.Amd64**。

3. 选择“生成并推送 IoT Edge 模块”。 

   “生成并推送”命令会启动三项操作。 首先，它在解决方案中创建名为 **config** 的新文件夹，用于保存基于部署模板和其他解决方案文件中的信息生成的完整部署清单。 其次，它会运行 `docker build`，以基于目标体系结构的相应 dockerfile 生成容器映像。 然后，它会运行 `docker push`，以将映像存储库推送到容器注册表。

## <a name="deploy-modules-to-device"></a>将模块部署到设备

使用 Visual Studio Cloud Explorer 和 Azure IoT Edge Tools 扩展将模块项目部署到 IoT Edge 设备。 你已经为方案准备了部署清单，即 config 文件夹中的 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

确保 IoT Edge 设备已启动并运行。

1. 在 Visual Studio Cloud Explorer 中，展开资源以查看 IoT 设备列表。

2. 右键单击要接收部署的 IoT Edge 设备的名称。

3. 选择“创建部署”。 

4. 在文件资源管理器中，选择解决方案的 config 文件夹中的 **deployment.windows-amd64** 文件。

5. 刷新 Cloud Explorer，以查看设备下面列出的已部署模块。

## <a name="view-generated-data"></a>查看生成的数据

将部署清单应用到 IoT Edge 设备以后，设备上的 IoT Edge 运行时就会收集新的部署信息并开始在其上执行操作。 在设备上运行的未包括在部署清单中的任何模块都会停止。 设备中缺失的任何模块都会启动。

可以使用 IoT Edge Tools 扩展查看抵达 IoT 中心的消息。

1. 在 Visual Studio Cloud Explorer 中，选择 IoT Edge 设备的名称。

2. 在“操作”列表中，选择“开始监视内置事件终结点”。  

3. 查看抵达 IoT 中心的消息。 消息可能需要在一段时间后才会抵达，因为 IoT Edge 设备必须接收其新部署并启动所有模块。 然后，我们对 CModule 代码所做的更改将等到计算机温度达到 25 度时才发送消息。 IoT 中心还会将消息类型“警报”添加到达到该温度阈值的任何消息。 

   ![查看抵达 IoT 中心的消息](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>编辑模块孪生

我们已使用 CModule 模块孪生将温度阈值设置为 25 度。 可以使用模块孪生来更改功能，而无需更新模块代码。

1. 在 Visual Studio 中打开 **deployment.windows-amd64.json** 文件。 （不是 deployment.template 文件。 如果在解决方案资源管理器中的 config 文件内未看到部署清单，请在 Cloud Explorer 工具栏中选择“显示所有文件”图标。） 

2. 找到 CModule 孪生，将 **temperatureThreshold** 参数的值更改为比上次报告的温度高出 5 到 10 度的新温度。

3. 保存 **deployment.windows-amd64.json** 文件。

4. 再次遵循部署步骤，将更新的部署清单应用到设备。

5. 监视传入的设备到云消息。 应会看到，在达到新的温度阈值之前，消息会停止发送。

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。

否则，可以删除本文中使用的本地配置和 Azure 资源，以免产生费用。

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建一个 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 准备好生成自己的模块时，可以详细了解如何[开发自己的 IoT Edge 模块](module-development.md)或如何[使用 Visual Studio 开发模块](how-to-visual-studio-develop-module.md)。 有关 IoT Edge 模块的示例，包括模拟温度模块，请参阅 [IoT Edge 模块示例](https://github.com/Azure/iotedge/tree/master/edge-modules)和 [IoT C SDK 示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)。

可以继续学习后续教程，了解如何借助 Azure IoT Edge 部署 Azure 云服务，在边缘位置处理和分析数据。

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [流分析](tutorial-deploy-stream-analytics.md)
> [机器学习](tutorial-deploy-machine-learning.md)
> [自定义视觉服务](tutorial-deploy-custom-vision.md)
