---
title: 将块 Blob 存储在设备上 - Azure IoT Edge |Microsoft Docs
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9faed53540d449f8658655ff7285b38aa20bee6c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901813"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>通过 IoT Edge 上的 Azure Blob 存储（预览版）在边缘存储数据

IoT Edge 上的 Azure Blob 存储在边缘提供了[块 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 存储解决方案。 IoT Edge 设备上的 blob 存储模块的行为类似于 Azure 块 blob 服务，但块 blob 存储在本地 IoT Edge 设备上。 你可以使用相同的 Azure 存储 SDK 方法或已经习惯的块 blob API 调用来访问 blob。 

拥有视频、图像、财务数据、医院数据或任何需要在本地存储稍后可在本地处理或传输到云的数据的方案，都是使用此模块的很好的示例。

本文提供有关在 IoT Edge 设备上运行 blob 服务的 IoT Edge 容器中部署 Azure Blob 存储的说明。 

>[!NOTE]
>IoT Edge 上的 Azure Blob 存储现为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

## <a name="prerequisites"></a>先决条件

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

   4. 选择“保存”。

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

5. 保存 **deployment.template.json** 文件。

6. 在解决方案工作区中打开 **.env** 文件。 

7. .env 文件设置为接收容器注册表凭据，但对于 Blob 存储映像，你不需要提供这些凭据，因为它们是公开的。 请将该文件替换为两个新的环境变量： 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. 提供 `STORAGE_ACCOUNT_NAME` 的值。帐户名称长度应为 3 到 24 个字符，且包含小写字母和数字。 为 `STORAGE_ACCOUNT_KEY` 提供 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。 

   不要在提供的值两端包含空格或引号。 

9. 保存 **.env** 文件。 

10. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。 

11. Visual Studio Code 会获取你在 deployment.template.json 和 .env 中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。 

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。 

1. 对于部署在运行“IoT Edge 上的 Azure Blob 存储”的同一边缘设备上的模块，blob 终结点为：`http://<module name>:11002/<account name>`。 
2. 对于部署在不同边缘设备，而非运行“IoT Edge 上的 Azure Blob 存储”的边缘设备上的模块，blob 终结点为：`http://<device IP >:11002/<account name>` 或 `http://<IoT Edge device hostname>:11002/<account name>` 或 `http://<FQDN>:11002/<account name>`，具体取决于你的设置

## <a name="logs"></a>日志

可以在以下位置找到容器内的日志： 
* 对于 Linux：/blobroot/logs/platformblob.log

## <a name="deploy-multiple-instances"></a>部署多个实例

如果要在 IoT Edge 上部署 Azure Blob 存储的多个实例，只需更改模块绑定到的 HostPort 即可。 Blob 存储模块始终在容器中公开端口 11002，但你可以声明它在主机上绑定到的端口。 

编辑模块创建选项以更改 HostPort 值：

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

连接到其他 blob 存储模块时，请将终结点更改为指向更新的主机端口。 

### <a name="try-it-out"></a>试用

Azure Blob 存储文档包括以多种语言提供示例代码的快速入门。 你可以通过将 blob 终结点更改为指向 blob 存储模块来运行这些示例，以测试 IoT Edge 上的 Azure Blob 存储。

以下快速入门使用 IoT Edge 也同样支持的语言，因此，你可以将它们作为 IoT Edge 模块与 blob 存储模块一起部署：

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>受支持的存储操作

IoT Edge 上的 blob 存储模块使用相同的 Azure 存储 SDK，并与适用于块 blob 终结点的 2018 年 3 月 28 日版 Azure 存储 API 保持一致。 后期版本取决于客户需求。 

并非所有 Azure Blob 存储操作都受 IoT Edge 上的 Azure Blob 存储支持。 以下部分详细说明了哪些操作不受支持。 

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

不受支持： 
* 获取和设置容器 ACL
* 租用容器
* 设置容器元数据

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
* 放置块：- 块大小必须小于或等于 4 MB
* 放置和获取块列表

不受支持：
* 放置来自 URL 的块

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)的详细信息

