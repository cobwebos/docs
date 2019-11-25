---
title: Deploy the Azure Blob Storage module to devices - Azure IoT Edge | Microsoft Docs
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: e5241e078cd8d36a9e43b4b55a649c1e24c85345
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456866"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Deploy the Azure Blob Storage on IoT Edge module to your device

There are several ways to deploy modules to an IoT Edge device and all of them work for Azure Blob Storage on IoT Edge modules. 两种最简单的方法是使用 Azure 门户或 Visual Studio Code 模板。

## <a name="prerequisites"></a>必备组件

- Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
- 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md)。
- [Visual Studio Code](https://code.visualstudio.com/) and the [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) if deploying from Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Deploy from the Azure portal

The Azure portal guides you through creating a deployment manifest and pushing the deployment to an IoT Edge device.

### <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 The Azure portal has a wizard that walks you through creating a deployment manifest, instead of building the JSON document manually. 创建分为三步：添加模块、指定路由和评审部署。

#### <a name="add-modules"></a>添加模块

1. 在页面的“部署模块”部分中，选择“添加”。

1. From the types of modules in the drop-down list, select **IoT Edge Module**.

1. Provide a name for the module and then specify the container image:

   - **Name** - azureblobstorageoniotedge
   - **Image URI** - mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge is case-sensitive when you make calls to modules, and the Storage SDK also defaults to lowercase. Although the name of the module in the [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) is **AzureBlobStorageonIoTEdge**, changing the name to lowercase helps to ensure that your connections to the Azure Blob Storage on IoT Edge module aren't interrupted.

1. The default **Container Create Options** values define the port bindings that your container needs, but you also need to add your storage account information and a mount for the storage on your device. Replace the default JSON in the portal with the JSON below:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. 使用以下信息更新复制的 JSON：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - Replace `<storage mount>` according to your container operating system. 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 The storage mount maps a location on your device that you provide to a set location in the module.

     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. 例如
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. 例如
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Set [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) properties for your module by copying the following JSON and pasting it into the **Set module twin's desired properties** box. Configure each property with an appropriate value, save it, and continue with the deployment. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![set container create options, deviceAutoDeleteProperties and deviceToCloudUploadProperties properties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about desired properties, see [Define or update desired properties](module-composition.md#define-or-update-desired-properties).

1. 选择“保存”。

1. 选择“下一步”转到路由部分。

#### <a name="specify-routes"></a>指定路由

Keep the default routes, and select **Next** to continue to the review section.

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 There are also two modules declared that you didn't add: **$edgeAgent** and **$edgeHub**. 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

审阅部署信息，然后选择“提交”。

### <a name="verify-your-deployment"></a>Verify your deployment

提交部署后，返回到 IoT 中心的“IoT Edge”页。

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
1. 在设备详细信息，检查 Blob 存储模块是否列出为“在部署中指定”和“由设备报告”。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="deploy-from-visual-studio-code"></a>Deploy from Visual Studio Code

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 Use the following steps to create a new IoT Edge solution with a blob storage module and to configure the deployment manifest.

1. 选择“视图” > “命令面板”。

1. 在命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

   按命令面板中的提示创建解决方案。

   | 字段 | Value |
   | ----- | ----- |
   | 选择文件夹 | Choose the location on your development machine for Visual Studio Code to create the solution files. |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“现有模块(输入完整映像 URL)”。 |
   | 提供模块名称 | Enter an all-lowercase name for your module, like **azureblobstorageoniotedge**.<br /><br />必须对 IoT Edge 上的 Azure Blob 存储模块使用小写名称。 引用模块时，IoT Edge 区分大小写，存储 SDK 默认为小写。 |
   | 提供模块的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。

1. 在新的解决方案工作区中打开 deployment.template.json，然后找到“模块”部分。 进行以下配置更改：

   1. Delete the **SimulatedTemperatureSensor** module, as it's not necessary for this deployment.

   1. Copy and paste the following code into the `createOptions` field:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Update module createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. 请将 `<your storage account name>` 替换为容易记住的名称。 Account names should be 3 to 24 characters long, with lowercase letters and numbers. No spaces.

1. 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

1. Replace `<storage mount>` according to your container operating system. 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 The storage mount maps a location on your device that you provide to a set location in the module.  

      
     - For Linux containers, the format is *\<storage path or volume>:/blobroot*. 例如
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:/blobroot** 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **/srv/containerdata:/blobroot**. Make sure to follow the steps to [grant directory access to the container user](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - For Windows containers, the format is *\<storage path or volume>:C:/BlobRoot*. 例如
         - use [volume mount](https://docs.docker.com/storage/volumes/): **my-volume:C:/blobroot**. 
         - use [bind mount](https://docs.docker.com/storage/bind-mounts/): **C:/ContainerData:C:/BlobRoot**.
         - Instead of using your local drive, you can map your SMB network location, for more information see [using SMB share as your local storage](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Do not change the second half of the storage mount value, which points to a specific location in the module. The storage mount should always end with **:/blobroot** for Linux containers and **:C:/BlobRoot** for Windows containers.

1. Configure [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) for your module by adding the following JSON to the *deployment.template.json* file. Configure each property with an appropriate value and save the file. If you are using the IoT Edge simulator, set the values to the related environment variables for these properties, which you can find in the explanation section of [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) and [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![set desired properties for azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   For information on configuring deviceToCloudUploadProperties and deviceAutoDeleteProperties after your module has been deployed, see [Edit the Module Twin](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). For more information about container create options, restart policy, and desired status, see [EdgeAgent desired properties](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. 保存 *deployment.template.json* 文件。

1. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。

1. Visual Studio Code takes the information that you provided in *deployment.template.json* and uses it to create a new deployment manifest file. 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="deploy-multiple-module-instances"></a>Deploy multiple module instances

If you want to deploy multiple instances of the Azure Blob Storage on IoT Edge module, you need to provide a different storage path and change the `HostPort` value that the module binds to. Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。

Edit **Container Create Options** (in the Azure portal) or the **createOptions** field (in the *deployment.template.json* file in Visual Studio Code) to change the `HostPort` value:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。

## <a name="next-steps"></a>后续步骤
Learn more about [Azure Blob Storage on IoT Edge](how-to-store-data-blob.md)

若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
