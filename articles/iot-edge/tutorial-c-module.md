---
title: Azure IoT Edge C 教程 | Microsoft Docs
description: 本教程介绍如何使用 C 代码创建 IoT Edge 模块并将其部署到边缘设备
services: iot-edge
author: shizn
manager: timlt
ms.author: xshi
ms.date: 07/30/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: c9d1931f1b78bb19f5e321a19baca45265ea7ab4
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413155"
---
# <a name="tutorial-develop-a-c-iot-edge-module-and-deploy-to-your-simulated-device"></a>教程：开发 C IoT Edge 模块并将其部署到模拟设备

可以使用 IoT Edge 模块部署代码，以直接将业务逻辑实现到 IoT Edge 设备。 本教程详细介绍如何创建并部署用于筛选传感器数据的 IoT Edge 模块。 本教程介绍如何执行下列操作：    

> [!div class="checklist"]
> * 使用 Visual Studio Code 在 C 中创建 IoT Edge 模块
> * 使用 Visual Studio Code 和 Docker 创建 Docker 映像并将其发布到容器注册表 
> * 将模块部署到 IoT Edge 设备
> * 查看生成的数据


在本教程中创建的 IoT Edge 模块可以筛选由设备生成的温度数据。 它只在温度高于指定阈值的情况下，向上游发送消息。 在边缘进行的此类分析适用于减少传递到云中和存储在云中的数据量。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。

云资源：

* Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 用于 Visual Studio Code 的 [C/C++ 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* [Docker CE](https://docs.docker.com/install/)。 


## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源” > “容器” > “Azure 容器注册表”。
2. 为注册表提供一个名称，选择一个订阅，选择一个资源组，然后将 SKU 设置为“基本”。 
3. 选择“创建”。
4. 创建容器注册表以后，导航到其中，然后选择“访问键”。 
5. 将“管理员用户”切换到“启用”。
6. 复制“登录服务器”、“用户名”和“密码”的值。 本教程中稍后将 Docker 映像发布到注册表，以及向 Edge 运行时添加注册表凭据时，将使用这些值。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块项目。
1. 在 Visual Studio Code 中选择“视图” > “集成终端”，打开 VS Code 集成终端。
2. 选择“视图” > “命令面板”，打开 VS Code 命令面板。 
3. 在命令面板中，键入并运行“Azure: 登录”命令，然后按说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。
4. 在命令面板中，键入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 在命令面板中提供以下信息，以便创建解决方案： 
   1. 选择要在其中创建解决方案的文件夹。 
   2. 提供解决方案的名称，或者接受默认的 **EdgeSolution**。
   3. 选择“C 模块”作为模块模板。 
   4. 将模块命名为 **CModule**。 
   5. 将在上一部分创建的 Azure 容器注册表指定为第一个模块的映像存储库。 将 **localhost:5000** 替换为复制的登录服务器值。 最终的字符串看起来类似于 **\<注册表名称\>.azurecr.io/cmodule**。
 
4. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 有一个 **modules** 文件夹、一个 **.vscode** 文件夹、一个部署清单模板文件，以及一个 **.env** 文件。 默认模块代码作为管道模块实现。 

5. 若要筛选 JSON 格式的消息，需导入用于 C 的 JSON 库。 可以选择任意 JSON 库或编写你自己的库，以便在 C 模块中分析 JSON。 以下步骤使用 [Parson](https://github.com/kgabis/parson) 作为示例。
   1. 从 [Parson Github 存储库](https://github.com/kgabis/parson)下载 **parson.c** 和 **parson.h**。 将这两个文件复制并粘贴到 **CModule** 文件夹中。
   2. 打开“模块” > “CModule” > “CMakeLists.txt”。 添加以下行，将 parson 库导入为 my_parson。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 在 **CMakeLists.txt** 的 `target_link_libraries` 中，向其添加 `my_parson`。
   4. 打开“模块” > “CModule” > “main.c”。 在 include 节底部添加以下代码，以便包括适用于 JSON 支持的 `parson.h`：

      ```c
      #include "parson.h"
      ```

6. 在 include 节后面添加全局变量 `temperatureThreshold`。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

    ```c
    static double temperatureThreshold = 25;
    ```

7. 将整个 `CreateMessageInstance` 函数替换为以下代码。 此函数分配适用于回调的上下文。 

    ```c
    static MESSAGE_INSTANCE* CreateMessageInstance(IOTHUB_MESSAGE_HANDLE message)
    {
        MESSAGE_INSTANCE* messageInstance = (MESSAGE_INSTANCE*)malloc(sizeof(MESSAGE_INSTANCE));
        if (NULL == messageInstance)
        {
            printf("Failed allocating 'MESSAGE_INSTANCE' for pipelined message\r\n");
        }
        else
        {
            memset(messageInstance, 0, sizeof(*messageInstance));

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
        }

        return messageInstance;
    }
    ```

8. 将整个 `InputQueue1Callback` 函数替换为以下代码。 此函数实现实际的消息传送筛选器。 

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

9. 添加 `moduleTwinCallback` 函数。 此方法从孪生模块接收所需属性的更新，然后更新 **temperatureThreshold** 变量，使之匹配。 所有模块都有自己的孪生模块，因此可以直接从云配置在模块中运行的代码。

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

10. 在函数 `SetupCallbacksForModule` 中为 `moduleTwinCallback` 添加新的回调。 将整个 `SetupCallbacksForModule` 函数替换为以下代码。

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

11. 保存此文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 CModule，该函数会筛选出其中报告的计算机温度低于可接受阈值的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker，以便将模块映像推送到 ACR： 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 或者再次在 Azure 门户中从注册表的“访问密钥”部分检索它们。

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件告知 `$edgeAgent` 部署两个模块：**tempSensor** 和 **CModule**。 `CModule.image` 值设置为映像的 Linux amd64 版本。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

3. 在 **deployment.template.json** 文件中有一个名为 **registryCredentials** 的节，用于存储 Docker 注册表凭据。 实际的用户名和密码对存储在属于 gitignore 文件的 .env 文件中。

4. 将 CModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容： 
    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

4. 保存此文件。
5. 在 VS Code 资源管理器中右键单击 **deployment.template.json** 文件，然后选择“生成 IoT Edge 解决方案”。 

要求 Visual Studio Code 生成解决方案时，它首先获取部署模板中的信息，然后在新的 **config** 文件夹中生成 `deployment.json` 文件。 然后，它在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 `CModule.dll` 容器化，然后将其推送到在初始化解决方案时指定的容器注册表。 

可在 VS Code 集成终端中查看具有标记的完整容器映像地址。 映像地址根据 `module.json` 文件中的信息生成，其格式为 **\<存储库\>:\<版本\>-\<平台\>**。 在本教程中，它应该类似于 **registryname.azurecr.io/cmodule:0.0.1-amd64**。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

1. 为 Azure IoT 工具包扩展配置适用于 IoT 中心的连接字符串： 
    1. 选择“视图” > “资源管理器”，打开 VS Code 资源管理器。 
    2. 在资源管理器中，单击“AZURE IOT 中心设备”，然后单击“...”。单击“选择 IoT 中心”。 按照说明登录 Azure 帐户并选择 IoT 中心。 
       注意，也可通过单击“设置 IoT 中心连接字符串”进行设置。 在弹出窗口中输入 IoT Edge 设备连接到的 IoT 中心的连接字符串。

2. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备，然后单击“为 IoT Edge 设备创建部署”。 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。

3. 单击刷新按钮。 此时会看到新的 **CModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视到达 IoT 中心的数据，请单击“...”，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT 中心: 停止监视 D2C 消息”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，可以安装用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)，然后通过 Docker 资源管理器在本地找到正在运行的模块。 在上下文菜单中单击“显示日志”，在集成终端中进行查看。
 
## <a name="clean-up-resources"></a>清理资源 

<!--[!INCLUDE [iot-edge-quickstarts-clean-up-resources](../../includes/iot-edge-quickstarts-clean-up-resources.md)] -->

如果想要继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。

否则，可删除本文中创建的本地配置和 Azure 资源，避免收费。 

> [!IMPORTANT]
> 删除 Azure 资源和资源组的操作不可逆。 资源组以及包含在其中的所有资源一旦删除就会被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要只删除 IoT 中心，请使用中心名称和资源组名称执行以下命令：

```azurecli-interactive
az iot hub delete --name MyIoTHub --resource-group TestResources
```


若要按名称删除整个资源组，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中键入包含 IoT 中心的资源组的名称。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。



## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 C 模块](how-to-develop-c-module.md)。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
