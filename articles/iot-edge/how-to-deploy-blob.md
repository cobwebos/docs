---
title: 将模块上的 Blob 存储部署到设备 - Azure IoT Edge
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80804616"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>将 IoT Edge 上的 Azure Blob 存储模块部署到设备

有多种方法可以将模块部署到 IoT Edge 设备，并且所有这些方法都适用于 IoT Edge 上的 Azure Blob 存储模块。 两种最简单的方法是使用 Azure 门户或 Visual Studio Code 模板。

## <a name="prerequisites"></a>先决条件

- Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
- 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md)。
- [Visual Studio Code](https://code.visualstudio.com/) 和 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)（如果从 Visual Studio Code 部署）。

## <a name="deploy-from-the-azure-portal"></a>从 Azure 门户部署

Azure 门户引导你创建部署清单并将部署推送到 IoT Edge 设备。

### <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”  。
1. 从设备列表中单击目标设备的 ID。
1. 选择“设置模块”  。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个引导你完成创建部署清单的向导。 该向导将三个步骤整理成多个选项卡：“模块”、“路由”和“查看 + 创建”    。

#### <a name="add-modules"></a>添加模块

1. 在页面的“IoT Edge 模块”部分，单击“添加”下拉列表并选择“IoT Edge 模块”，显示“添加 IoT Edge 模块”页面     。

2. 在“模块设置”选项卡上，提供模块的名称，然后指定容器映像 URI  ：

   示例:
  
   - **IoT Edge 模块名称**：`azureblobstorageoniotedge`
   - **映像 URI**：`mcr.microsoft.com/azure-blob-storage:latest`

   ![模块孪生设置](./media/how-to-deploy-blob/addmodule-tab1.png)

   如本程序所述，在“模块设置”、“容器创建选项”和“模块孪生设置”选项卡上指定值之前，请勿选择“添加”     。

   > [!IMPORTANT]
   > 调用模块时，Azure IoT Edge 区分大小写，存储 SDK 也默认为小写。 虽然在 [Azure 市场](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)中此模块的名称为 **AzureBlobStorageonIoTEdge**，但将名称更改为小写有助于确保与 IoT Edge 上的 Azure Blob 存储模块建立的连接不会中断。

3. 打开“容器创建选项”  选项卡。

   ![模块孪生设置](./media/how-to-deploy-blob/addmodule-tab3.png)

   将以下 JSON 复制并粘贴到框中，以便为设备上的存储提供存储帐户信息和装载。
  
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

4. 使用以下信息更新已复制到**容器创建选项**中的 JSON：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - 根据容器操作系统替换 `<storage mount>`。 提供 blob 模块将在其中存储其数据的 IoT Edge 设备上[卷](https://docs.docker.com/storage/volumes/)的名称或指向现有目录的绝对路径。 存储装载将你在设备上提供的位置映射到模块中设置的位置。

     - 对于 Linux 容器，格式为** \<your storage path or volume> ：/blobroot**。 例如：
         - 使用[卷装载](https://docs.docker.com/storage/volumes/)：`my-volume:/blobroot`
         - 使用[绑定装载](https://docs.docker.com/storage/bind-mounts/)：`/srv/containerdata:/blobroot`。 确保按步骤[为容器用户授予目录访问权限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 对于 Windows 容器，格式为** \<your storage path or volume> ： C：/BlobRoot**。 例如：
         - 使用[卷装载](https://docs.docker.com/storage/volumes/)：`my-volume:C:/BlobRoot`。
         - 使用[绑定装载](https://docs.docker.com/storage/bind-mounts/)：`C:/ContainerData:C:/BlobRoot`。
         - 可以映射 SMB 网络位置，而不使用本地驱动器。有关详细信息，请参阅[使用 SMB 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > 请勿更改存储装载值的后半部分，该部分指向 IoT Edge 模块上 Blob 存储中的特定位置。 对于 Linux 容器，存储装载必须始终以“:/blobroot”  结尾；对于 Windows 容器，必须以“:C:/BlobRoot”  结尾。

5. 在“模块孪生设置”选项卡上，复制以下 JSON 并将其粘贴到框中  。

   ![模块孪生设置](./media/how-to-deploy-blob/addmodule-tab4.png)

   使用适当的值配置每个属性，如占位符所示。 如果使用 IoT Edge 模拟器，请按照 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 中所述，将这些属性的值设置为相关环境变量。

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   有关在部署模块后配置 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的信息，请参阅[编辑模块孪生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)。 若要详细了解所需属性，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。

6. 选择“添加”   。

7. 在完成时选择“下一步:  路由”继续转到路由部分。

#### <a name="specify-routes"></a>指定路由

保留默认路由并选择“下一步:  查看 + 创建”继续转到查看部分。

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 另外还有两个模块已声明但却未添加，它们是： **$edgeAgent** 和 **$edgeHub**。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

查看部署信息，然后选择“创建”  。

### <a name="verify-your-deployment"></a>验证部署

创建部署后，返回到 IoT 中心的“IoT Edge”页。 

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
1. 在设备详细信息，检查 Blob 存储模块是否列出为“在部署中指定”和“由设备报告”。  

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="deploy-from-visual-studio-code"></a>从 Visual Studio Code 部署

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 使用以下步骤创建带有 Blob 存储模块的新 IoT Edge 解决方案，并配置部署清单。

1. 选择“查看” > “命令面板”   。

1. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

   按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 Visual Studio Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“现有模块(输入完整映像 URL)”。  |
   | 提供模块名称 | 输入模块的全小写名称，例如 **azureblobstorageoniotedge**。<br/><br/>必须对 IoT Edge 上的 Azure Blob 存储模块使用小写名称。 引用模块时，IoT Edge 区分大小写，存储 SDK 默认为小写。 |
   | 提供模块的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。

1. 在新的解决方案工作区中打开 deployment.template.json  ，然后找到“模块”  部分。 进行以下配置更改：

   1. 删除 SimulatedTemperatureSensor  模块，因为此部署不需要该模块。

   1. 将以下代码复制并粘贴到 `createOptions` 字段中：

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

      ![更新模块 createOptions - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 不含空格。

1. 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

1. 根据容器操作系统替换 `<storage mount>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储装载将你在设备上提供的位置映射到模块中设置的位置。  

     - 对于 Linux 容器，格式为** \<your storage path or volume> ：/blobroot**。 例如：
         - 使用[卷装载](https://docs.docker.com/storage/volumes/)：`my-volume:/blobroot`
         - 使用[绑定装载](https://docs.docker.com/storage/bind-mounts/)：`/srv/containerdata:/blobroot`。 确保按步骤[为容器用户授予目录访问权限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 对于 Windows 容器，格式为** \<your storage path or volume> ： C：/BlobRoot**。 例如
         - 使用[卷装载](https://docs.docker.com/storage/volumes/)：`my-volume:C:/BlobRoot`。
         - 使用[绑定装载](https://docs.docker.com/storage/bind-mounts/)：`C:/ContainerData:C:/BlobRoot`。
         - 可以映射 SMB 网络位置，而不使用本地驱动器。有关详细信息，请参阅[使用 SMB 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > 请勿更改存储装载值的后半部分，该部分指向 IoT Edge 模块上 Blob 存储中的特定位置。 对于 Linux 容器，存储装载必须始终以“:/blobroot”  结尾；对于 Windows 容器，必须以“:C:/BlobRoot”  结尾。

1. 通过将以下 JSON 添加到 *deployment.template.json* 文件，为模块配置 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)。 为每个属性配置适当的值并保存文件。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关环境变量，可以在 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 和 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 的说明部分中找到这些值

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

1. 右键单击“deployment.template.json”  并选择“生成 IoT Edge 部署清单”  。

1. Visual Studio Code 会获取你在 *deployment.template.json* 中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config  文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="deploy-multiple-module-instances"></a>部署多个模块实例

若要在 IoT Edge 模块上部署 Azure Blob 存储的多个实例，需要提供不同的存储路径，并更改模块绑定到的 `HostPort` 值。 Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。

编辑“容器创建选项”  （在 Azure 门户中）或 **createOptions** 字段（在 Visual Studio Code 的 *deployment.template.json* 文件中），更改 `HostPort` 值：

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。

## <a name="configure-proxy-support"></a>配置代理支持

如果组织使用代理服务器，则需要为 edgeAgent 和 edgeHub 运行时模块配置代理支持。 此过程涉及两项任务：

- 在设备上配置运行时守护程序和 IoT Edge 代理。
- 为部署清单 JSON 文件中的模块设置 HTTPS_PROXY 环境变量。

[将 IoT Edge 设备配置为通过代理服务器进行通信](how-to-configure-proxy-support.md)中介绍了此过程。

此外，blob 存储模块还需要清单部署文件中的 HTTPS_PROXY 设置。 可以直接编辑部署清单文件，也可以使用 Azure 门户。

1. 在 Azure 门户中导航到 IoT 中心，然后从左窗格菜单中选择“IoT Edge”。 

1. 选择要配置模块的设备。

1. 选择“设置模块”  。

1. 在此页的“IoT Edge 模块”部分，选择 blob 存储模块  。

1. 在“更新 IoT Edge 模块”页上，选择“环境变量”选项卡。  

1. 添加 `HTTPS_PROXY` 作为“名称”，  添加代理 URL 作为“值”。 

      ![设置 HTTPS_PROXY 环境变量](./media/how-to-deploy-blob/https-proxy-config.png)

1. 单击“更新”  ，然后单击“查看 + 创建”  。

1. 请注意，代理会添加到部署清单中的模块，此时请选择“创建”  。

1. 通过从设备详细信息页中选择模块来验证设置，然后在“IoT Edge 模块详细信息”  页的下半部分选择“环境变量”  选项卡。

      ![设置 HTTPS_PROXY 环境变量](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>后续步骤

详细了解 [IoT Edge 上的 Azure Blob 存储](how-to-store-data-blob.md)。

若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
