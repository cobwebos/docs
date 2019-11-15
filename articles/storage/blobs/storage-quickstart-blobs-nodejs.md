---
title: 快速入门：Azure Blob 存储库 v12 - JavaScript
description: 本快速入门介绍如何使用适用于 JavaScript 的 Azure Blob 存储客户端库版本 12 在 Blob（对象）存储中创建容器和 blob。 接下来，将介绍如何将 blob 下载到本地计算机，以及如何在容器中列出所有 blob。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 28a75158b161e680f857b986bcb754f1f99e8fab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825305"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>快速入门：适用于 JavaScript 的 Azure Blob 存储客户端库 v12

适用于 JavaScript 的 Azure Blob 存储客户端库 v12 入门。 Azure Blob 存储是 Microsoft 提供的适用于云的对象存储解决方案。 请按照步骤操作，安装程序包并试用基本任务的示例代码。 Blob 存储最适合存储巨量的非结构化数据。

> [!NOTE]
> 若要开始使用以前的 SDK 版本，请参阅[快速入门：适用于 JavaScript 的 Azure Blob 存储客户端库](storage-quickstart-blobs-nodejs-v10.md)。

使用适用于 JavaScript 的 Azure Blob 存储客户端库 v12 完成以下操作：

* 创建容器
* 将 blob 上传到 Azure 存储
* 列出容器中所有的 blob
* 将 blob 下载到本地计算机
* 删除容器

[API 参考文档](/javascript/api/@azure/storage-blob) | [库源代码](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [包（节点包管理器）](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | [示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
* Azure 存储帐户 - [创建存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 适用于操作系统的当前 [Node.js](https://nodejs.org/en/download/)。

## <a name="setting-up"></a>设置

本部分逐步指导如何准备一个项目，使其与适用于 JavaScript 的 Azure Blob 存储客户端库 v12 配合使用。

### <a name="create-the-project"></a>创建项目

创建名为 blob-quickstart-v12 的 JavaScript 应用程序  。

1. 在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为项目创建新目录。

    ```console
    mkdir blob-quickstart-v12
    ```

1. 切换到新创建的 blob-quickstart-v12 目录  。

    ```console
    cd blob-quickstart-v12
    ```

1. 创建名为 package.json 的新文本文件  。 此文件定义 Node.js 项目。 将此文件保存到 blob-quickstart-v12 目录中  。 下面是文件的内容：

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    如果需要，可以在 `author` 字段中输入自己的名字。
   
### <a name="install-the-package"></a>安装包

当仍在 blob-quickstart-v12 目录中时，使用 `npm install` 命令安装适用于 JavaScript 包的 Azure Blob 存储客户端库  。 此命令读取 package.json 文件，并安装适用于 JavaScript 包的 Azure Blob 存储客户端库 v12 及其依赖的所有库  。

```console
npm install
```

### <a name="set-up-the-app-framework"></a>设置应用框架

从项目目录中执行以下操作：

1. 在代码编辑器中打开另一个新文本文件
1. 添加 `require` 调用以加载 Azure 和 Node.js 模块
1. 为程序创建结构，包括非常基本的异常处理

    代码如下：

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - Javascript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. 将新文件在 blob-quickstart-v12 目录中另存为 blob-quickstart-v12.js   。

### <a name="copy-your-credentials-from-the-azure-portal"></a>从 Azure 门户复制凭据

当示例应用程序向 Azure 存储发出请求时，必须对其进行授权。 若要对请求进行授权，请将存储帐户凭据以连接字符串形式添加到应用程序中。 按照以下步骤查看存储帐户凭据：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在存储帐户概述的“设置”部分，选择“访问密钥”。   在这里，可以查看你的帐户访问密钥以及每个密钥的完整连接字符串。
4. 找到“密钥 1”下面的“连接字符串”值，选择“复制”按钮复制该连接字符串。    下一步需将此连接字符串值添加到某个环境变量。

    ![显示如何从 Azure 门户复制连接字符串的屏幕截图](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

复制连接字符串以后，请将其写入运行应用程序的本地计算机的新环境变量中。 若要设置环境变量，请打开控制台窗口，并遵照适用于操作系统的说明。 将 `<yourconnectionstring>` 替换为实际的连接字符串。

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

在 Windows 中添加环境变量后，必须启动命令窗口的新实例。

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>重新启动程序

添加环境变量后，重启需要读取环境变量的任何正在运行的程序。 例如，重启开发环境或编辑器，然后再继续。

## <a name="object-model"></a>对象模型

Azure Blob 存储最适合存储巨量的非结构化数据。 非结构化数据是不遵循特定数据模型或定义（如文本或二进制数据）的数据。 Blob 存储提供了三种类型的资源：

* 存储帐户
* 存储帐户中的容器
* 容器中的 blob

以下图示显示了这些资源之间的关系。

![Blob 存储体系结构的图示](./media/storage-blob-introduction/blob1.png)

使用以下 JavaScript 类与这些资源进行交互：

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient)：`BlobServiceClient` 类可用于操纵 Azure 存储资源和 blob 容器。
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient)：`ContainerClient` 类可用于操纵 Azure 存储容器及其 blob。
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient)：`BlobClient` 类可用于操纵 Azure 存储 blob。

## <a name="code-examples"></a>代码示例

这些示例代码片段演示如何使用适用于 JavaScript 的 Azure Blob 存储客户端库执行以下步骤：

* [获取连接字符串](#get-the-connection-string)
* [创建容器](#create-a-container)
* [将 blob 上传到容器中](#upload-blobs-to-a-container)
* [列出容器中的 blob](#list-the-blobs-in-a-container)
* [下载 blob](#download-blobs)
* [删除容器](#delete-a-container)

### <a name="get-the-connection-string"></a>获取连接字符串

下面的代码从[配置存储连接字符串](#configure-your-storage-connection-string)部分中创建的环境变量中检索存储帐户的连接字符串。

在 `main` 函数内添加此代码：

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>创建容器

确定新容器的名称。 以下代码将 UUID 值追加到容器名称，确保其是唯一的。

> [!IMPORTANT]
> 容器名称必须为小写。 有关命名容器和 Blob 的详细信息，请参阅[命名和引用容器、Blob 和元数据](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

调用 [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--newpipelineoptions-) 方法，创建 [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) 类的实例。 然后，调用 [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) 方法以获取对容器的引用。 最后，调用 [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) 方法在存储帐户中实际创建容器。

将此代码添加到 `main` 函数的末尾：

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await new BlobServiceClient.fromConnectionString(CONNECT_STR);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
await containerClient.create();
```

### <a name="upload-blobs-to-a-container"></a>将 blob 上传到容器中

以下代码片段：

1. 创建要上传到 blob 的文本字符串。
1. 通过从 [Create a container](#create-a-container) 部分调用 [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) 上的 [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) 方法，获取对 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 对象的引用。
1. 通过调用 [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) 方法，将文本字符串数据上传到 blob。

将此代码添加到 `main` 函数的末尾：

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure Storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
await blockBlobClient.upload(data, data.length);
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

通过调用 [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 方法，列出容器中的 blob。 在这种情况下，只向容器添加了一个 blob，因此列表操作只返回那个 blob。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>下载 Blob

通过调用 [download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) 方法，下载以前创建的 blob。 示例代码包括名为 `streamToString` 的帮助器函数，用于将 Node.js 可读流读入字符串。

将此代码添加到 `main` 函数的末尾：

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

在 `main` 函数后添加此帮助器函数  ：

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>删除容器

以下代码使用 [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) 方法删除整个容器，从而清除该应用所创建的资源。 也可根据需要删除本地文件。

将此代码添加到 `main` 函数的末尾：

```javascript
console.log('\nDeleting container...');

// Delete container
await containerClient.delete();
```

## <a name="run-the-code"></a>运行代码

此应用创建文本字符串，并将其上传到 Blob 存储。 示例随后列出容器中的 Blob，下载 Blob 并显示下载的数据。

在控制台提示符下，导航到包含blob-quickstart-v12.py 文件的目录，然后执行以下 `node` 命令来运行应用  。

```console
node blob-quickstart-v12.js
```

应用的输出类似于以下示例：

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

在逐步执行调试器中的代码，并在整个过程中检查 Azure 门户。 检查是否正在创建容器。 可以在容器中打开 blob 并查看内容。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 JavaScript 上传、下载和列出 blob。

若要查看 Blob 存储示例应用，请继续执行以下操作：

> [!div class="nextstepaction"]
> [Azure Blob 存储 SDK v12 JavaScript 示例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/azure-storage-blob/samples)

* 若要了解详细信息，请参阅 [Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md)。
* 有关教程、示例、快速入门和其他文档，请访问 [Azure SDK for JavaScript 文档](/azure/javascript/)。
