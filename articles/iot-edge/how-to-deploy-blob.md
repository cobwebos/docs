---
title: 将模块上的 blob 存储部署到设备-Azure IoT Edge
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: kgremban
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: fe09fb47a75ff9d412ffab2daafaf241a43443b4
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729601"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>将 IoT Edge 模块上的 Azure Blob 存储部署到设备

可以通过多种方式将模块部署到 IoT Edge 设备，并且所有这些模块都适用于 IoT Edge 模块上的 Azure Blob 存储。 两种最简单的方法是使用 Azure 门户或 Visual Studio Code 模板。

## <a name="prerequisites"></a>必备组件

- Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
- 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md)。
- 如果从 Visual Studio Code 部署，请[Visual Studio Code](https://code.visualstudio.com/)和[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。

## <a name="deploy-from-the-azure-portal"></a>从 Azure 门户部署

Azure 门户指导您创建部署清单并将部署推送到 IoT Edge 设备。

### <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”。

### <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 Azure 门户提供了一个向导，该向导将引导你创建部署清单，而不是手动生成 JSON 文档。 它分为三个步骤，分为多个选项卡：**模块**、**路由**和**查看 + 创建**。

#### <a name="add-modules"></a>添加模块

1. 在该页的 " **IoT Edge 模块**" 部分中，单击 "**添加**" 下拉列表并选择 " **IoT Edge 模块**" 以显示 "**添加 IoT Edge 模块**" 页。

2. 在 "**模块设置**" 选项卡上，提供模块的名称，然后指定容器映像 URI：

   示例：
  
   - **IoT Edge 模块名称**： `azureblobstorageoniotedge`
   - **映像 URI**： `mcr.microsoft.com/azure-blob-storage:latest`

   ![模块克隆设置](./media/how-to-deploy-blob/addmodule-tab1.png)

   请不要选择 "**添加**"，直到在 "**模块设置**"、"**容器创建选项**" 和 "**模块克隆设置**" 选项卡上指定值，如本过程中所述。

   > [!IMPORTANT]
   > 调用模块时 Azure IoT Edge 区分大小写，并且存储 SDK 也默认为小写。 尽管[Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)中的模块名称是**AzureBlobStorageonIoTEdge**，但将该名称更改为小写有助于确保与 IoT Edge 模块上的 Azure Blob 存储的连接不会中断。

3. 在容器的 "**创建选项**" 选项卡上，你将提供 JSON 代码来提供存储帐户信息，并在设备上装入存储。

   ![模块克隆设置](./media/how-to-deploy-blob/addmodule-tab3.png)

   复制以下 JSON，并将其粘贴到框中，这是在下一步中引用占位符说明。
  
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

4. 更新为**容器创建选项**复制的 JSON，并提供以下信息：

   - 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名称的长度应为3到24个字符，并且带有小写字母和数字。 无空格。

   - 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

   - 根据容器操作系统替换 `<storage mount>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储装入会将你提供的设备上的位置映射到模块中的设置位置。

     - 对于 Linux 容器，格式为 *\<存储路径或卷 >：/blobroot*。 例如：
         - 使用[卷装入](https://docs.docker.com/storage/volumes/)：**我的卷：/blobroot**
         - 使用[bind 装载](https://docs.docker.com/storage/bind-mounts/)： **/srv/containerdata：/blobroot**。 请确保按照以下步骤为[容器用户授予目录访问权限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 对于 Windows 容器，格式为 *\<存储路径或卷 >： C：/BlobRoot*。 例如：
         - 使用[卷装入](https://docs.docker.com/storage/volumes/)：**我的卷： C：/blobroot**。
         - 使用[bind 装载](https://docs.docker.com/storage/bind-mounts/)： **c：/ContainerData： c：/BlobRoot**。
         - 请参阅[使用 smb 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)，而不是使用本地驱动器，有关详细信息，请参阅使用 smb 共享

     > [!IMPORTANT]
     > 不要更改存储装载值的后半部分，它指向模块中的特定位置。 存储装载应始终以**以下内容结束：/blobroot** for Linux 容器和 **： C：/blobroot** for Windows 容器。

5. 在 "**模块克隆设置**" 选项卡上，复制以下 JSON，并将其粘贴到框中。

   ![模块克隆设置](./media/how-to-deploy-blob/addmodule-tab4.png)

   按照占位符的指示，使用适当的值配置每个属性。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为[deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties)和[deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)所述的属性的相关环境变量。

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

   有关部署模块后配置 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的信息，请参阅[编辑模块](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)克隆。 有关所需属性的详细信息，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。

6. 选择 **添加** 。

7. 选择 "**下一步：** 继续路由" 部分。

#### <a name="specify-routes"></a>指定路由

保留默认路由，然后选择 "**下一步：查看 + 创建**" 以继续查看检查部分。

#### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 还有两个声明未添加的模块： **$edgeAgent**和 **$edgeHub**。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

查看部署信息，然后选择 "**创建**"。

### <a name="verify-your-deployment"></a>验证你的部署

创建部署后，将返回到 IoT 中心的 " **IoT Edge** " 页。

1. 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。
1. 在设备详细信息，检查 Blob 存储模块是否列出为“在部署中指定”和“由设备报告”。

可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。

## <a name="deploy-from-visual-studio-code"></a>从 Visual Studio Code 部署

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 使用以下步骤创建一个包含 blob 存储模块的新 IoT Edge 解决方案，并配置部署清单。

1. 选择“查看” > “命令面板”。

1. 在命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

   按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 选择开发计算机上用于创建解决方案文件 Visual Studio Code 的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“现有模块(输入完整映像 URL)”。 |
   | 提供模块名称 | 输入模块的全小写名称，例如**azureblobstorageoniotedge**。<br/><br/>必须对 IoT Edge 上的 Azure Blob 存储模块使用小写名称。 引用模块时，IoT Edge 区分大小写，存储 SDK 默认为小写。 |
   | 提供模块的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。

1. 在新的解决方案工作区中打开 deployment.template.json，然后找到“模块”部分。 进行以下配置更改：

   1. 删除**SimulatedTemperatureSensor**模块，因为这不是此部署所必需的。

   1. 将以下代码复制并粘贴到 `createOptions` 字段：

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

      ![更新模块 createOptions-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名称的长度应为3到24个字符，并且带有小写字母和数字。 无空格。

1. 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。

1. 根据容器操作系统替换 `<storage mount>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储装入会将你提供的设备上的位置映射到模块中的设置位置。  

     - 对于 Linux 容器，格式为 *\<存储路径或卷 >：/blobroot*。 例如：
         - 使用[卷装入](https://docs.docker.com/storage/volumes/)：**我的卷：/blobroot**
         - 使用[bind 装载](https://docs.docker.com/storage/bind-mounts/)： **/srv/containerdata：/blobroot**。 请确保按照以下步骤为[容器用户授予目录访问权限](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - 对于 Windows 容器，格式为 *\<存储路径或卷 >： C：/BlobRoot*。 例如：
         - 使用[卷装入](https://docs.docker.com/storage/volumes/)：**我的卷： C：/blobroot**。
         - 使用[bind 装载](https://docs.docker.com/storage/bind-mounts/)： **c：/ContainerData： c：/BlobRoot**。
         - 请参阅[使用 smb 共享作为本地存储](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)，而不是使用本地驱动器，有关详细信息，请参阅使用 smb 共享

     > [!IMPORTANT]
     > 不要更改存储装载值的后半部分，它指向模块中的特定位置。 存储装载应始终以**以下内容结束：/blobroot** for Linux 容器和 **： C：/blobroot** for Windows 容器。

1. 通过向 DeviceToCloudUploadProperties*文件中*添加以下 JSON，为模块配置[](how-to-store-data-blob.md#devicetoclouduploadproperties)和[deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 。 使用适当的值配置每个属性，并保存该文件。 如果使用 IoT Edge 模拟器，请将这些属性的值设置为相关的环境变量，可以在[deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties)和[deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)的解释部分中找到这些属性。

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

   ![设置 azureblobstorageoniotedge 的所需属性-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   有关部署模块后配置 deviceToCloudUploadProperties 和 deviceAutoDeleteProperties 的信息，请参阅[编辑模块](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)克隆。 有关容器创建选项、重新启动策略和所需状态的详细信息，请参阅[EdgeAgent 所需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。

1. 保存 *deployment.template.json* 文件。

1. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。

1. Visual Studio Code 获取你在*部署*中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="deploy-multiple-module-instances"></a>部署多个模块实例

如果要在 IoT Edge 模块上部署 Azure Blob 存储的多个实例，则需要提供其他存储路径并更改模块绑定到的 `HostPort` 值。 Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。

编辑**容器创建选项**（在 Azure 门户中）或 createOptions 字段（在 Visual Studio Code 中的 *文件中*）以更改 `HostPort` 值：

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。

## <a name="next-steps"></a>后续步骤
[在 IoT Edge 上了解有关 Azure Blob 存储的](how-to-store-data-blob.md)详细信息

若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。
