---
title: Azure IoT Edge 设备上的 Azure Blob 存储 | Microsoft 文档
description: 将 Azure Blob 存储模块部署到 IoT Edge 设备以在边缘存储数据。
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 10/03/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b86f73302d9f5d07cd1e6e8c7801de56a988cc7
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955263"
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

#### <a name="find-the-module"></a>查找模块

选择查找 blob 存储模块的两种方法之一：

1. 在 Azure 门户中搜索“IoT Edge 上的 Azure Blob 存储”。 选择搜索结果项
2. 从 Azure 门户转到市场，然后单击“物联网”。 在“IoT Edge 模块”部分选择“IoT Edge 上的 Azure Blob 存储”。 单击“创建”

#### <a name="steps-to-deploy"></a>部署步骤

**IoT Edge 模块的目标设备**

1. 选择在其中部署 IoT 中心的“订阅”。
2. 选择“IoT 中心”。
3. 提供想要在其中部署此模块的“IoT Edge 设备名称”。 可以选择使用“查找设备”找到你的设备。
4. 单击“创建”。

**设置模块**

1. 在“添加模块”部分的“部署模块”下，你会发现该模块已使用以“AzureBlobStorageonIoTEdge”开头的名称列出。 
2. 从“部署模块”列表中选择 blob 存储模块。 将打开“IoT Edge 自定义模块”侧面板。
3. 名称：可以在此处更改模块的名称
4. 映像 URI：将 URI 替换为 mcr.microsoft.com/azure-blob-storage:latest
5. 容器创建选项：使用你的值编辑以下 JSON 并将其替换为“门户”页中的 JSON：
   
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
   
    * 更新 `<your storage account name>`。 帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。
    * 使用 64 字节 base64 密钥更新 `<your storage account key>`。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。
    * 更新 `<storage directory bind>`。 具体取决于容器操作系统。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。  

       * Linux 容器：\<存储路径 >:/blobroot。 例如：/srv/containerdata:/blobroot。 或，my-volume:/blobroot。 
       * Windows 容器：\<存储路径 >:C:/BlobRoot。 例如，C:/ContainerData:C:/BlobRoot。 或，my-volume:C:/blobroot。
   
   > [!CAUTION]
   > 对于 \<存储目录绑定> 值，不要更改 Linux 的“/blobroot”和 Windows 的“C:/BlobRoot”。

    ![更新模块值](./media/how-to-store-data-blob/edit-module.png)

6. 保存“IoT Edge 自定义模块”中的值
7. 单击“设置模块”部分中的“下一步”
8. 单击“指定路由”部分中的“下一步”
9. 检查后，单击“查看部署”部分中的“提交”。
10. 在 IoT 中心验证设备正在运行 blob 存储模块 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 模板

Azure IoT Edge 在 Visual Studio Code 中提供模板，以帮助你开发边缘解决方案。 这些步骤需要你在开发计算机上安装有 [Visual Studio Code](https://code.visualstudio.com/)，并使用 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)进行配置。

使用以下步骤创建带有 blob 存储模块的新 IoT Edge 解决方案，并配置部署清单。 

1. 选择“视图” > “命令面板”。 

2. 在“命令面板”中，输入并运行“Azure IoT Edge: New IoT Edge Solution”命令。 

3. 按照提示操作以创建一个新解决方案： 

   1. 选择文件夹 - 浏览到要创建新解决方案的文件夹。  
   
   2. 提供解决方案名称 - 输入解决方案的名称，或者接受默认名称。
   
   3. 选择模块模板 - 选择现有模块（输入完整映像 URL）。
   
   4. 提供模块名称 - 为模块输入可识别的名称，例如 azureBlobStorage。
   
   5. 提供模块的 Docker 映像 - 提供映像 URI：mcr.microsoft.com/azure-blob-storage:latest

VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。 

解决方案模板创建包含 blob 存储模块映像的部署清单模板，但需要配置模块的创建选项。 

1. 在新的解决方案工作区中打开 deployment.template.json，然后找到“模块”部分。 

2. 删除 tempSensor 模块，因为此部署不需要该模块。 

3. 复制以下代码并将其粘贴到 blob 存储模块的“createOptions”字段中： 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![更新模块创建选项](./media/how-to-store-data-blob/create-options.png)

4. 在创建选项 JSON 中，根据容器操作系统更新 `<storage directory bind>`。 提供[卷](https://docs.docker.com/storage/volumes/)的名称或 IoT Edge 设备上希望 blob 模块在其中存储其数据的目录绝对路径。  

   * Linux 容器：\<存储路径 >:/blobroot。 例如：/srv/containerdata:/blobroot。 或，my-volume:/blobroot。
   * Windows 容器：\<存储路径 >:C:/BlobRoot。 例如，C:/ContainerData:C:/BlobRoot。 或，my-volume:C:/blobroot。
   
   > [!CAUTION]
   > 对于 \<存储目录绑定> 值，不要更改 Linux 的“/blobroot”和 Windows 的“C:/BlobRoot”。

5. 保存“deployment.template.json”。

6. 在解决方案工作区中打开 .env。 

7. 无需为 blob 存储映像输入任何容器注册表值，因为该映像是公开可用的。 而是，添加两个新的环境变量： 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. 提供 `STORAGE_ACCOUNT_NAME` 的值，帐户名长度应为 3 到 24 个字符，并带有小写字母和数字。 并提供 `STORAGE_ACCOUNT_KEY` 的 64 字节 base64 密钥。 你可以使用 [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 等工具生成密钥。 你将使用这些凭据从其他模块访问 blob 存储。 

9. 保存“.env”。 

10. 右键单击“deployment.template.json”并选择“生成 IoT Edge 部署清单”。 

Visual Studio Code 会获取你在 deployment.template.json 和 .env 中提供的信息，并使用它来创建新的部署清单文件。 在解决方案工作区中的新 config 文件夹中创建部署清单。 获得该文件后，可以按照[从 Visual Studio Code 中部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)或[使用 Azure CLI 2.0 部署 Azure IoT Edge 模块](how-to-deploy-modules-cli.md)中的步骤进行操作。

## <a name="connect-to-your-blob-storage-module"></a>连接到 blob 存储模块

可以使用为模块配置的帐户名和帐户密钥来访问 IoT Edge 设备上的 blob 存储。 

将你的 IoT Edge 设备指定为对其进行的任何存储请求的 blob 终结点。 你可以使用 IoT Edge 设备信息和配置的帐户名[为显式存储终结点创建连接字符串](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint)。 

1. 对于部署在运行“IoT Edge 上的 Azure Blob 存储”的同一边缘设备上的模块，blob 终结点为：`http://<Module Name>:11002/<account name>`。 
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

