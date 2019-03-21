---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0fc34c913453abd174009213233a54e30b9346d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881378"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>通过 IoT Edge 上的 Azure Blob 存储（预览版）在边缘存储数据

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 blob 存储模块的行为类似于 Azure 块 blob 服务，但块 blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 

此模块附带**自动分层**和**自动过期**功能。

> [!NOTE]
> 目前，自动分层和自动过期功能仅在 Linux AMD64 和 Linux ARM32 中可用。

**自动分层**是可配置的功能，可用于将本地 Blob 存储中的数据自动上传到 Azure，并提供间歇性的 Internet 连接支持。 该功能允许：
- 启用/禁用分层功能
- 选择将数据复制到 Azure 的顺序，例如，选择 NewestFirst 或 OldestFirst
- 指定要将数据上传到的 Azure 存储帐户。
- 指定要上传到 Azure 的容器。 此模块允许指定源和目标容器名称。
- 执行完整 Blob 分层（使用 `Put Blob` 操作）和块级分层（使用 `Put Block` 和 `Put Block List` 操作）。

如果 Blob 由块构成，则此模块使用块级分层。 下面是一些常见场景：
- 应用程序需要更新以前上传的 Blob 的某些块；此模块只上传更新的块，而不会上传整个 Blob。
- 当模块正在上传 Blob 时，Internet 连接断开；连接恢复后，该模块只上传剩余的块，而不会上传整个 Blob。

如果在 Blob 上传期间发生意外的进程终止（例如电源故障），当模块重新联机时，将再次上传需要上传的所有块。

**自动过期**是可配置的功能，当本地存储中的 Blob 达到生存时间 (TTL) 时，此模块会使用此功能自动删除这些 Blob。 生存时间以分钟为单位。 该功能允许：
- 启用/禁用自动过期功能
- 以分钟为单位指定 TTL

对于需要在本地存储视频、图像、金融数据、医院数据或其他任何数据，以后在本地处理数据或将其传输到云的场合，都很适合使用此模块。

本文提供有关在 IoT Edge 设备上运行 blob 服务的 IoT Edge 容器中部署 Azure Blob 存储的说明。 

>[!NOTE]
>IoT Edge 上的 Azure Blob 存储现为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

观看视频，快速了解
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>必备组件

Azure IoT Edge 设备：

* 可以按照适用于 [Linux](quickstart-linux.md) 或 [Windows 设备](quickstart.md)的快速入门中的步骤，将开发计算机或虚拟机用作 Edge 设备。
* IoT Edge 模块上的 Azure Blob 存储支持以下设备配置：

   | 操作系统 | 体系结构 |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT 核心版（10 月更新） | AMD64 |
   | Windows 10 IoT 企业版（10 月更新） | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

云资源：

* Azure 中的标准层 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 


## <a name="deploy-blob-storage-to-your-device"></a>将 blob 存储部署到设备

有多种方法可以将模块部署到 IoT Edge 设备，并且所有这些方法都适用于 IoT Edge 模块上的 Azure Blob 存储。 两种最简单的方法是使用 Azure 门户或 Visual Studio Code 模板。 

### <a name="azure-portal"></a>Azure 门户

Azure 市场提供了可直接部署到 IoT Edge 设备的 IoT Edge 模块，包括 IoT Edge 上的 Azure Blob 存储。 请在 Azure 门户中遵循以下步骤部署模块：

1. 在 [Azure 门户](https://portal.azure.com)中，搜索“IoT Edge 上的 Azure Blob 存储”。 **选择**从市场返回的搜索结果。

   ![通过市场搜索创建模块](./media/how-to-store-data-blob/marketplace-module.png)

2. 选择要接收该模块的 IoT Edge 设备。 在“IoT Edge 模块的目标设备”页面上，提供以下信息：

   1. 选择包含要使用的 IoT 中心的**订阅**。

   2. 选择你的 **IoT 中心**。

   3. 如果你知道自己的 **IoT Edge 设备名称**，请在文本框中输入该名称。 或者，选择“查找设备”以从 IoT 中心的 IoT Edge 设备列表中进行选择。 
   
   4. 选择“创建”。

   现在，你已从 Azure 市场选择了 IoT Edge 模块，并且选择了要接收该模块的 IoT Edge 设备，接下来，你将转到一个具有三个步骤的向导，该向导可帮助准确定义该模块的部署方式。

3. 在“设置模块”向导的“添加模块”步骤中可以看到，**AzureBlobStorageonIoTEdge** 模块已列在“部署模块”下面。 

2. 从部署模块列表中选择 Blob 存储模块打开其详细信息。 

   ![选择模块名称打开模块详细信息](./media/how-to-store-data-blob/open-module-details.png)

3. 在“IoT Edge 自定义模块”页上，使用以下步骤更新 IoT Edge 上的 Azure Blob 存储模块：

   1. 将模块**名称**更改为小写。 可根据需要重命名模块，或使用 `azureblobstorageoniotedge`。 

      >[!IMPORTANT]
      >调用模块时，Azure IoT Edge 区分大小写，存储 SDK 默认为小写。 为确保与 IoT Edge 上的 Azure Blob 存储模块建立的连接不会中断，请为其提供小写名称。 

   2. 默认的“容器创建选项”可以创建容器所需的端口绑定，但你还需要为设备上的存储目录添加存储帐户信息和绑定。 使用以下 JSON 覆盖门户中的 JSON：
    
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
   3. 使用以下信息更新复制的 JSON： 

      * 请将 `<your storage account name>` 替换为容易记住的名称。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。
      * 将 `<your storage account key>` 替换为 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。
      * 根据容器操作系统替换 `<storage directory bind>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储目录绑定将你在设备上提供的位置映射到模块中设置的位置。 

         * Linux 容器：\<存储路径 >:/blobroot。 例如：/srv/containerdata:/blobroot。 或，my-volume:/blobroot。 
         * Windows 容器：\<存储路径 >:C:/BlobRoot。 例如，C:/ContainerData:C:/BlobRoot。 或，my-volume:C:/blobroot。
   
      > [!IMPORTANT]
      > 请不要更改存储目录绑定值的后半部分，该部分指向模块中的特定位置。 对于 Linux 容器，存储目录绑定应始终以 **:/blobroot** 结尾；对于 Windows 容器，应以 **:C:/BlobRoot** 结尾。

      ![更新模块容器创建选项 - 门户](./media/how-to-store-data-blob/edit-module.png)

   4. 在所需属性中设置[自动分层和自动过期](#configure-auto-tiering-and-auto-expiration-via-azure-portal)。 [自动分层](#auto-tiering-properties)和[自动过期](#auto-expiration-properties)属性及其可能值的列表。 

   5. 选择“保存”。 

4. 选择“下一步”继续执行向导的下一步。
5. 在向导的“指定路由”步骤中，选择“下一步”。
6. 在向导的“查看部署”步骤中，选择“提交”。
7. 提交部署后，返回到 IoT 中心的“IoT Edge”页。 选择用作部署目标的 IoT Edge 设备，以打开其详细信息。 
8. 在设备详细信息，检查 Blob 存储模块是否列出为“在部署中指定”和“由设备报告”。 可能需要等待一段时间，该模块才会在设备上启动并向 IoT 中心发回报告。 刷新页面以查看更新的状态。 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 模板

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 这些步骤要求在开发计算机上安装 [Visual Studio Code](https://code.visualstudio.com/)，并使用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 进行配置。

使用以下步骤创建带有 blob 存储模块的新 IoT Edge 解决方案，并配置部署清单。 

1. 选择“视图” > “命令面板”。 

2. 在“命令面板”中，输入并运行 Azure IoT Edge：**New IoT Edge solution** 命令。 按命令面板中的提示创建解决方案。

   | 字段 | 值 |
   | ----- | ----- |
   | 选择文件夹 | 在适用于 VS Code 的开发计算机上选择用于创建解决方案文件的位置。 |
   | 提供解决方案名称 | 输入解决方案的描述性名称，或者接受默认的 **EdgeSolution**。 |
   | 选择模块模板 | 选择“现有模块(输入完整映像 URL)”。 |
   | 提供模块名称 | 输入模块的全小写名称，例如 **azureblobstorage**。<br><br>必须对 IoT Edge 上的 Azure Blob 存储模块使用小写名称。 引用模块时，IoT Edge 区分大小写，存储 SDK 默认为小写。 |
   | 提供模块的 Docker 映像 | 提供映像 URI：**mcr.microsoft.com/azure-blob-storage:latest** |

   VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。 

3. 在新的解决方案工作区中打开 deployment.template.json，然后找到“模块”部分。 进行以下配置更改：

   1. 删除 tempSensor 模块，因为此部署不需要该模块。 

   2. 复制以下代码并将其粘贴到 blob 存储模块的“createOptions”字段中： 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![更新模块创建选项 - VS Code](./media/how-to-store-data-blob/create-options.png)

4. 在创建选项 JSON 中，根据容器操作系统更新 `<storage directory bind>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。 存储目录绑定将你在设备上提供的位置映射到模块中设置的位置。  

   * Linux 容器：\<存储路径 >:/blobroot。 例如：/srv/containerdata:/blobroot。 或，my-volume:/blobroot。
   * Windows 容器：\<存储路径 >:C:/BlobRoot。 例如，C:/ContainerData:C:/BlobRoot。 或，my-volume:C:/blobroot。
   
   > [!IMPORTANT]
   > 请不要更改存储目录绑定值的后半部分，该部分指向模块中的特定位置。 对于 Linux 容器，存储目录绑定应始终以 **:/blobroot** 结尾；对于 Windows 容器，应以 **:C:/BlobRoot** 结尾。

5. 配置[自动分层和自动过期](#configure-auto-tiering-and-auto-expiration-via-vscode)。 [自动分层](#auto-tiering-properties)和[自动过期](#auto-expiration-properties)属性的列表

6. 保存 **deployment.template.json** 文件。

7. 在解决方案工作区中打开 **.env** 文件。 

8. .env 文件设置为接收容器注册表凭据，但对于 Blob 存储映像，你不需要提供这些凭据，因为它们是公开的。 请将该文件替换为两个新的环境变量： 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. 提供 `STORAGE_ACCOUNT_NAME` 的值。帐户名称长度应为 3 到 24 个字符，且包含小写字母和数字。 为 `STORAGE_ACCOUNT_KEY` 提供 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。 

   不要在提供的值两端包含空格或引号。 

10. 保存 **.env** 文件。 

11. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。 

12. Visual Studio Code 会获取你在 deployment.template.json 和 .env 中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>自动分层和自动过期属性与配置

使用所需属性设置自动分层和自动过期属性。 可以在部署期间设置这些属性，或者，以后可以通过编辑模块孪生来更改这些属性，而无需重新部署。 我们建议检查“模块孪生”中的 `reported configuration` 和 `configurationValidation`，以确保正确传播值。

### <a name="auto-tiering-properties"></a>自动分层属性 
此设置的名称为 `tieringSettings`

| 字段 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| tieringOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| backlogPolicy | NewestFirst、OldestFirst | 用于选择将数据复制到 Azure 的顺序。 默认设置为 `OldestFirst`。 顺序由 Blob 的上次修改时间确定 |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 是一个连接字符串，用于指定要将数据上传到的 Azure 存储帐户。 指定 `Azure Storage Account Name`、`Azure Storage Account Key` 或 `End point suffix`。 添加相应 EndpointSuffix 的 Azure 位置将上传数据，它对于各不相同全局 Azure，政府 Azure 和 Microsoft Azure Stack。 |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | 用于指定要上传到 Azure 的容器名称。 此模块允许指定源和目标容器名称。 如果未指定目标容器名称，系统会自动分配 `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>` 作为容器名称。 可以创建目标容器名称的模板字符串，具体请查看“可能的值”列。 <br>* %h -> IoT 中心名称（3 到 50 个字符）。 <br>* %d -> IoT 设备 ID（1 到 129 个字符）。 <br>* %m -> 模块名称（1 到 64 个字符）。 <br>* %c -> 源容器名称（3 到 63 个字符）。 <br><br>容器名称的最大大小为 63 个字符。尽管系统会自动分配目标容器名称，但如果容器大小超过 63 个字符，系统会将每个部分（IoTHubName、IotEdgeDeviceName、ModuleName、ContainerName）修剪为 15 个字符。 |

### <a name="auto-expiration-properties"></a>自动过期属性
此设置的名称为 `ttlSettings`

| 字段 | 可能的值 | 说明 |
| ----- | ----- | ---- |
| ttlOn | true、false | 默认设置为 `false`，若要启用它，可将它设置为 `true`|
| timeToLiveInMinutes | `<minutes>` | 以分钟为单位指定 TTL。 当本地存储中的 Blob 达到 TTL 时，模块会自动删除这些 Blob |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>通过 Azure 门户配置自动分层和自动过期

若要通过设置所需属性来启用自动分层和自动过期，可以设置以下值：

- **在初始部署期间**：复制“设置模块孪生的所需属性”框中的 JSON。 为每个属性配置适当的值，保存属性，然后继续部署。

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![设置自动分层和自动过期属性](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **通过“模块标识孪生”功能部署模块后**：转到此模块的“模块标识孪生”，复制所需属性下的 JSON，为每个属性配置适当的值，然后保存属性。 在“模块标识孪生”JSON 中，确保每次添加或更新任何所需属性时，`reported configuration` 部分都会反映所做的更改，并且 `configurationValidation` 部分针对每个属性显示成功消息。

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>通过 VSCode 配置自动分层和自动过期

- **在初始部署期间**：在 deployment.template.json 中添加以下 JSON，以定义此模块的所需属性。 为每个属性配置适当的值并保存。

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

下面是此模块的所需属性示例：![设置 azureblobstorageoniotedge 的所需属性 - VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **通过“模块孪生”部署模块后**：[编辑此模块的模块孪生](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)，复制所需属性下的 JSON，为每个属性配置适当的值，然后保存属性。 在“模块孪生”JSON 中，确保每次添加或更新任何所需属性时，`reported configuration` 部分都会反映所做的更改，并且 `configurationValidation` 部分针对每个属性显示成功消息。

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
  ## <a name="logs"></a>日志

请按照说明为[配置 IoT Edge 模块在 docker 日志](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。 

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。 

1. 对于部署在运行“IoT Edge 上的 Azure Blob 存储”的同一边缘设备上的模块，Blob 终结点为：`http://<module name>:11002/<account name>`。 
2. 对于部署在不同边缘设备，而非运行“IoT Edge 上的 Azure Blob 存储”的边缘设备上的模块，Blob 终结点为：`http://<device IP >:11002/<account name>`、`http://<IoT Edge device hostname>:11002/<account name>` 或 `http://<FQDN>:11002/<account name>`，具体取决于设置

## <a name="deploy-multiple-instances"></a>部署多个实例

若要在 IoT Edge 上部署 Azure Blob 存储的多个实例，需要提供不同的存储路径，并更改模块绑定到的 HostPort。 Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。 

编辑模块创建选项以更改 HostPort 值：

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。 

## <a name="try-it-out"></a>试用

### <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 存储快速入门示例
Azure Blob 存储文档包括以多种语言提供示例代码的快速入门。 你可以通过将 blob 终结点更改为指向 blob 存储模块来运行这些示例，以测试 IoT Edge 上的 Azure Blob 存储。 遵循相应的步骤[连接到 Blob 存储模块](#connect-to-your-blob-storage-module)

以下快速入门使用 IoT Edge 也同样支持的语言，因此，你可以将它们作为 IoT Edge 模块与 blob 存储模块一起部署：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure 存储资源管理器
此外可以尝试[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)连接到本地存储帐户。 我们已尝试使用[以前版本 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0)的 Azure 资源管理器。
> [!NOTE]
> 您可能会执行以下步骤时遇到错误，忽略和刷新。 

1. 下载并安装 Azure 存储资源管理器
2. 连接到 Azure 存储中使用的连接字符串
3. 提供连接字符串： `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. 经过连接的步骤。
5. 创建本地存储帐户内的容器
6. 开始上传文件作为块 blob。
   > [!NOTE]
   > 取消选中该复选框以将其作为页 blob 上传。 此模块不支持页 blob。 你将获取此信息提示上传文件，例如.iso、.vhd、.vhdx 或任何大型文件时。

7. 您可以选择要上传数据连接你的 Azure 存储帐户。 它提供单个视图对本地存储帐户和 Azure 存储帐户

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 Blob 存储模块使用相同的 Azure 存储 SDK，并与适用于块 Blob 终结点的 2017-04-17 版 Azure 存储 API 保持一致。 后期版本取决于客户需求。

并非所有 Azure Blob 存储操作都受 IoT Edge 上的 Azure Blob 存储支持。 以下部分列出了支持和不支持的操作。

### <a name="account"></a>帐户

受支持： 
* 列出容器

不受支持： 
* 获取和设置 blob 服务属性
* 预检 blob 请求
* 获取 blob 服务统计信息
* 获取帐户信息

### <a name="containers"></a>容器

受支持： 
* 创建和删除容器
* 获取容器属性和元数据
* 列出 Blob
* 获取和设置容器 ACL
* 设置容器元数据

不受支持：
* 租用容器

### <a name="blobs"></a>Blob

受支持： 
* 放置、获取和删除 blob
* 获取和设置 blob 属性
* 获取和设置 blob 元数据

不受支持： 
* 租用 blob
* 获取 blob 快照
* 复制和中止复制 blob
* 撤销删除 blob
* 设置 blob 层

### <a name="block-blobs"></a>块 Blob

受支持： 
* 放置块
* 放置和获取块列表

不受支持：
* 放置来自 URL 的块

## <a name="feedback"></a>反馈:
你的反馈是，若要使此模块和它的功能非常有用且易于使用我们非常重要。 请分享你的反馈并告诉我们如何改进。

在可以联系我们联系我们： absiotfeedback@microsoft.com 

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)的详细信息

