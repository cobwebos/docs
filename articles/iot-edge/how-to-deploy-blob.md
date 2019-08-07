---
title: 将 Azure Blob 存储模块部署到设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: arduppal
ms.author: arduppal
ms.date: 06/19/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: 86040020c8f9163a327b2029008e3648723b14ec
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839683"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>将 IoT Edge 上的 Azure Blob 存储模块部署到设备

有多种方法可以将模块部署到 IoT Edge 设备，并且所有这些方法都适用于 IoT Edge 上的 Azure Blob 存储模块。 两种最简单的方法是使用 Azure 门户或 Visual Studio Code 模板。

> [!NOTE]
> IoT Edge 上的 Azure Blob 存储现为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
- 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/) 和 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)（如果从 Visual Studio Code 部署）。

## <a name="deploy-from-the-azure-portal"></a>从 Azure 门户部署

Azure 门户引导你创建部署清单并将部署推送到 IoT Edge 设备。

### <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。 它分为三步：添加模块、指定路由和评审部署。

#### <a name="add-modules"></a>添加模块

1. 在页面的“部署模块”部分中，选择“添加”。

1. 从下拉列表的模块类型中选择“IoT Edge 模块”。

1. 提供模块名称，然后指定容器映像：

   - **名称** - azureblobstorageoniotedge
   - **映像 URI** - mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > 调用模块时，Azure IoT Edge 区分大小写，存储 SDK 也默认为小写。 尽管[Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)中的模块名称是**AzureBlobStorageonIoTEdge**, 但将该名称更改为小写有助于确保与 IoT Edge 模块上的 Azure Blob 存储的连接不会中断。

1. 默认的“容器创建选项”值定义容器所需的端口绑定，但你还需要为设备上的存储目录添加存储帐户信息和绑定。 使用以下 JSON 替换门户中的默认 JSON：

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. 使用以下信息更新复制的 JSON：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - 根据容器操作系统替换 `<storage directory bind>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储目录绑定将你在设备上提供的位置映射到模块中设置的位置。

     - Linux 容器的格式为 \<存储路径 >:/blobroot。 例如， **/srv/containerdata:/blobroot** 或 **my-volume:/blobroot**。
     - Windows 容器的格式为 \<存储路径>:C:/BlobRoot。 例如，**C:/ContainerData:C:/BlobRoot** 或 **my-volume:C:/blobroot**。 请参阅[使用 smb 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage), 而不是使用本地驱动器, 有关详细信息, 请参阅使用 smb 共享

     > [!IMPORTANT]
     > 请不要更改存储目录绑定值的后半部分，该部分指向模块中的特定位置。 对于 Linux 容器，存储目录绑定应始终以 **:/blobroot** 结尾；对于 Windows 容器，应以 **:C:/BlobRoot** 结尾。

1. 通过复制以下 JSON 并将其粘贴到“设置模块孪生的所需属性”框中，为模块设置 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 属性。 为每个属性配置适当的值，保存后继续进行部署。

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

   ![设置容器创建选项、deviceAutoDeleteProperties 和 deviceToCloudUploadProperties 属性](./media/how-to-deploy-blob/iotedge-custom-module.png)

   有关在部署模块后配置 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的信息，请参阅[编辑模块孪生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 若要详细了解所需属性，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。

1. 选择**保存**。

1. 选择“下一步”转到路由部分。

#### <a name="specify-routes"></a>指定路由

保留默认路由，然后选择“下一步”，转到评审部分。

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 另外还有两个模块已声明但却未添加，它们是： **$edgeAgent** 和 **$edgeHub**。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

审阅部署信息，然后选择“提交”。

### <a name="verify-your-deployment"></a>验证部署

提交部署后，返回到 IoT 中心的“IoT Edge”页。

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
1. 在设备详细信息，检查 Blob 存储模块是否列出为“在部署中指定”和“由设备报告”。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="deploy-from-visual-studio-code"></a>从 Visual Studio Code 部署

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 使用以下步骤创建带有 Blob 存储模块的新 IoT Edge 解决方案，并配置部署清单。

1. 选择“视图” > “命令面板”。

1. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

   按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 Visual Studio Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“现有模块(输入完整映像 URL)”。 |
   | 提供模块名称 | 输入模块的全小写名称，例如 **azureblobstorageoniotedge**。<br /><br />必须对 IoT Edge 上的 Azure Blob 存储模块使用小写名称。 引用模块时，IoT Edge 区分大小写，存储 SDK 默认为小写。 |
   | 提供模块的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。

1. 在新的解决方案工作区中打开 deployment.template.json，然后找到“模块”部分。 进行以下配置更改：

   1. 删除**SimulatedTemperatureSensor**模块, 因为这不是此部署所必需的。

   1. 将以下代码复制并粘贴到 `createOptions` 字段中：

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![更新模块 createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

1. 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

1. 根据容器操作系统替换 `<storage directory bind>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储目录绑定将你在设备上提供的位置映射到模块中设置的位置。  

      - Linux 容器的格式为 \<存储路径 >:/blobroot。 例如， **/srv/containerdata:/blobroot** 或 **my-volume:/blobroot**。
      - Windows 容器的格式为 \<存储路径>:C:/BlobRoot。 例如，**C:/ContainerData:C:/BlobRoot** 或 **my-volume:C:/blobroot**。  请参阅[使用 smb 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage), 而不是使用本地驱动器, 有关详细信息, 请参阅使用 smb 共享

      > [!IMPORTANT]
      > 请不要更改存储目录绑定值的后半部分，该部分指向模块中的特定位置。 对于 Linux 容器，存储目录绑定应始终以 **:/blobroot** 结尾；对于 Windows 容器，应以 **:C:/BlobRoot** 结尾。

1. 通过将以下 JSON 添加到 *deployment.template.json* 文件，为模块配置 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)。 为每个属性配置适当的值并保存文件。

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

   ![设置 azureblobstorageoniotedge 的所需属性 - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   有关在部署模块后配置 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的信息，请参阅[编辑模块孪生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 若要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 所需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。

1. 保存 *deployment.template.json* 文件。

1. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。

1. Visual Studio Code 会获取你在 *deployment.template.json* 中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="deploy-multiple-module-instances"></a>部署多个模块实例

若要在 IoT Edge 模块上部署 Azure Blob 存储的多个实例，需要提供不同的存储路径，并更改模块绑定到的 `HostPort` 值。 Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。

编辑“容器创建选项”（在 Azure 门户中）或 **createOptions** 字段（在 Visual Studio Code 的 *deployment.template.json* 文件中），更改 `HostPort` 值：

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。

## <a name="next-steps"></a>后续步骤
[在 IoT Edge 上了解有关 Azure Blob 存储的](how-to-store-data-blob.md)详细信息

在[IoT Edge 博客上的 Azure Blob 存储](https://aka.ms/abs-iot-blogpost)中随时了解最新的更新和公告

若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
