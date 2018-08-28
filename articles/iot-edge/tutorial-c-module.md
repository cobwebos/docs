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
ms.openlocfilehash: 6b7589652f5b180a826f3c0b1fcbe040ff3d386d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "41924735"
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
* 用于 Azure IoT edge 的 C 模块不支持 Windows 容器。 如果 IoT Edge 设备是 Windows 计算机，请将其配置为[使用 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

云资源：

* Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 

开发资源：

* [Visual Studio Code](https://code.visualstudio.com/)。 
* 用于 Visual Studio Code 的 [C/C++ 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)。
* 适用于 Visual Studio Code 的 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。
* [Docker CE](https://docs.docker.com/install/)。 

>[!Note]
>用于 Azure IoT edge 的 C 模块不支持 Windows 容器。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-a-container-registry"></a>创建容器注册表
本教程将使用适用于 VS Code 的 Azure IoT Edge 扩展来生成模块并从文件创建**容器映像**。 然后将该映像推送到用于存储和管理映像的**注册表**。 最后，从注册表部署在 IoT Edge 设备上运行的映像。  

在此教程中，可以使用任意兼容 Docker 的注册表。 可以在云中使用的两个常见 Docker 注册表服务分别是 [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)和 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。 本教程使用 Azure 容器注册表。 

以下 Azure CLI 命令在名为 **IoTEdgeResources** 的资源组中创建注册表。 将 **{acr_name}** 替换为注册表的唯一名称。 

   ```azurecli-interactive
   az acr create --resource-group IoTEdgeResources --name {acr_name} --sku Basic --admin-enabled true
   ```

检索注册表的凭据。 

   ```azurecli-interactive
   az acr credential show --name {acr_name}
   ```

复制**用户名**和某个密码的值。 本教程中稍后将 Docker 映像发布到注册表，以及向 Edge 运行时添加注册表凭据时，将使用这些值。 

## <a name="create-an-iot-edge-module-project"></a>创建 IoT Edge 模块项目
以下步骤介绍如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块项目。

### <a name="create-a-new-solution"></a>创建新的解决方案

创建可以使用自己的代码进行自定义的 C 解决方案模板。 

1. 选择“视图” > “命令面板”，打开 VS Code 命令面板。 

2. 在命令面板中，键入并运行“Azure: 登录”命令，然后按说明登录 Azure 帐户。 如果已登录，则可跳过此步骤。

3. 在命令面板中，键入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 在命令面板中提供以下信息，以便创建解决方案： 

   1. 选择要在其中创建解决方案的文件夹。 
   2. 提供解决方案的名称，或者接受默认的 **EdgeSolution**。
   3. 选择“C 模块”作为模块模板。 
   4. 将模块命名为 **CModule**。 
   5. 将在上一部分创建的 Azure 容器注册表指定为第一个模块的映像存储库。 将 **localhost:5000** 替换为 **\<注册表名称\>.azurecr.io**。 仅替换字符串的 localhost 部分，不要删除模块名称。 

   ![提供 Docker 映像存储库](./media/tutorial-c-module/repository.png)

VS Code 窗口将加载你的 IoT Edge 解决方案空间。 解决方案工作区包含五个顶级组件。 你不会在本教程中编辑 **\.vscode** 文件夹或 **\.gitignore** 文件。 **modules** 文件夹包含模块的 C 代码以及用于将模块构建为容器映像的 Dockerfile。 **\.env** 文件存储容器注册表凭据。 **deployment.template.json** 文件包含 IoT Edge 运行时用于在设备上部署模块的信息。 

如果在创建解决方案时未指定容器注册表，但接受了默认的 localhost:5000 值，则不会有 \.env 文件。 

   ![C 解决方案工作区](./media/tutorial-c-module/workspace.png)

### <a name="add-your-registry-credentials"></a>添加注册表凭据

环境文件存储容器注册表的凭据，并将其与 IoT Edge 运行时共享。 此运行时需要这些凭据才能将专用映像拉取到 IoT Edge 设备中。 

1. 在 VS Code 资源管理器中，打开 .env 文件。 
2. 使用从 Azure 容器注册表复制的 **username** 和 **password** 值更新相关字段。 
3. 保存此文件。 

### <a name="update-the-module-with-custom-code"></a>使用自定义代码更新模块

将代码添加到 C 模块，以便读取传感器中的数据、检查报告的计算机温度是否超出安全阈值，以及将该信息传递至 IoT 中心。 

5. 在此场景中，来自传感器的数据采用 JSON 格式。 若要筛选 JSON 格式的消息，请导入用于 C 的 JSON 库。本教程使用 Parson。

   1. 下载 [Parson Github 存储库](https://github.com/kgabis/parson)。 将 **parson.c** 和 **parson.h** 文件复制到 **CModule** 文件夹中。

   2. 打开“模块” > “CModule” > “CMakeLists.txt”。 在文件顶部，导入名为 **my_parson** 的充当库的 Parson 文件。

      ```
      add_library(my_parson
          parson.c
          parson.h
      )
      ```

   3. 将 **my_parson** 添加到 CMakeLists.txt 的 **target_link_libraries** 函数中的库列表。

   4. 保存 **CMakeLists.txt** 文件。

   5. 打开“模块” > “CModule” > “main.c”。 在 include 语句的列表底部，添加一个新的语句，以便包括适用于 JSON 支持的 `parson.h`：

      ```c
      #include "parson.h"
      ```

6. 在 **main.c** 文件中，在 include 节后面添加名为 `temperatureThreshold` 的全局变量。 此变量设置一个值，若要向 IoT 中心发送数据，测量的温度必须超出该值。 

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

10. 将 `SetupCallbacksForModule` 函数替换为以下代码。 

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

11. 保存 **main.c** 文件。

## <a name="build-your-iot-edge-solution"></a>生成 IoT Edge 解决方案

在上一部分，你已经创建了一个 IoT Edge 解决方案并将代码添加到了 CModule，该函数会筛选出其中报告的计算机温度处于可接受限制范围内的消息。 现在需将解决方案生成为容器映像并将其推送到容器注册表。 

1. 打开 VS Code 集成终端，方法是选择“视图” > “集成终端”。 

1. 在 Visual Studio Code 集成终端输入以下命令，登录到 Docker。 需使用 Azure 容器注册表凭据登录，只有这样才能将模块映像推送到注册表。 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   使用用户名、密码以及在第一部分从 Azure 容器注册表复制的登录服务器。 或者再次在 Azure 门户中从注册表的“访问密钥”部分检索它们。

2. 在 VS Code 资源管理器的 IoT Edge 解决方案工作区中打开 **deployment.template.json** 文件。 此文件告知 `$edgeAgent` 部署两个模块：**tempSensor** 和 **CModule**。 `CModule.image` 值设置为映像的 Linux amd64 版本。 若要详细了解部署清单，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

4. 将 CModule 模块孪生添加到部署清单。 在 `moduleContent` 节底部 `$edgeHub` 模块孪生后插入以下 JSON 内容： 

    ```json
        "CModule": {
            "properties.desired":{
                "TemperatureThreshold":25
            }
        }
    ```

   ![将 CModule 孪生添加到部署模板](./media/tutorial-c-module/module-twin.png)

4. 保存 **deployment.template.json** 文件。
5. 在 VS Code 资源管理器中右键单击“deployment.template.json”文件，然后选择“生成并推送 IoT Edge 解决方案”。 

要求 Visual Studio Code 生成解决方案时，它首先在新的 **config** 文件夹中生成 `deployment.json` 文件。 deployment.json 文件的信息收集自已更新的模板文件、用于存储容器注册表凭据的 .env 文件，以及 CModule 文件夹中的 module.json 文件。 

接下来，Visual Studio Code 在集成终端运行两个命令，即 `docker build` 和 `docker push`。 这两个命令会生成代码，将 `CModule.dll` 容器化，然后将其推送到在初始化解决方案时指定的容器注册表。 

可在 VS Code 集成终端中查看具有标记的完整容器映像地址。 映像地址根据 `module.json` 文件中的信息生成，其格式为 **\<存储库\>:\<版本\>-\<平台\>**。 在本教程中，它应该类似于 **myregistry.azurecr.io/cmodule:0.0.1-amd64**。

## <a name="deploy-and-run-the-solution"></a>部署并运行解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可以使用用于 Visual Studio Code 的 Azure IoT Toolkit 扩展来部署模块。 你已经为方案准备了部署清单，即 **deployment.json** 文件。 现在需要做的就是选择一个设备来接收部署。

1. 在 VS Code 命令面板中，运行“Azure IoT 中心: 选择 IoT 中心”。 

2. 选择包含要配置的 IoT Edge 设备的订阅和 IoT 中心。 

3. 在 VS Code 资源管理器中，展开“Azure IoT 中心设备”部分。 

4. 右键单击 IoT Edge 设备的名称，然后选择“为单个设备创建部署”。 

   ![为单个设备创建部署](./media/tutorial-c-module/create-deployment.png)

5. 选择 **config** 文件夹中的 **deployment.json** 文件，然后单击“选择 Edge 部署清单”。 不要使用 deployment.template.json 文件。 

6. 单击刷新按钮。 此时会看到新的 **CModule** 在运行，此外还有 **TempSensor** 模块以及 **$edgeAgent** 和 **$edgeHub** 在运行。 

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视到达 IoT 中心的数据，请单击“...”，然后选择“开始监视 D2C 消息”。
2. 若要监视特定设备的 D2C 消息，请右键单击列表中的设备，然后选择“开始监视 D2C 消息”。
3. 若要停止监视数据，请在命令面板中运行“Azure IoT 中心: 停止监视 D2C 消息”命令。 
4. 若要查看或更新模块孪生，请右键单击列表中的模块，然后选择“编辑模块孪生”。 若要更新模块孪生，请保存孪生 JSON 文件，然后右键单击编辑器区域并选择“更新模块孪生”。
5. 若要查看 Docker 日志，可以安装用于 VS Code 的 [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)，然后通过 Docker 资源管理器在本地找到正在运行的模块。 在上下文菜单中单击“显示日志”，在集成终端中进行查看。
 
## <a name="clean-up-resources"></a>清理资源 

如果打算继续学习下一篇建议的文章，可以保留已创建的资源和配置，以便重复使用。 还可以继续使用相同的 IoT Edge 设备作为测试设备。 

否则，可以删除本文中创建的本地配置和 Azure 资源，以避免收费。 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>后续步骤

在本教程中，创建了 IoT Edge 模块，其中包含用于筛选 IoT Edge 设备生成的原始数据的代码。 做好生成自己的模块的准备以后，即可详细了解如何[使用用于 Visual Studio Code 的 Azure IoT Edge 开发 C 模块](how-to-develop-c-module.md)。 可以继续阅读后续教程，了解如何使用 Azure IoT Edge 通过其他方式将数据转化为边缘业务见解。

> [!div class="nextstepaction"]
> [使用 SQL Server 数据库在边缘存储数据](tutorial-store-data-sql-server.md)

<!-- Links -->
[lnk-tutorial1-win]: quickstart.md
[lnk-tutorial1-lin]: quickstart-linux.md
