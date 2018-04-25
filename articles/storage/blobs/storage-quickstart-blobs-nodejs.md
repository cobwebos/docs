---
title: Azure 快速入门 - 使用 Node.js 在对象存储中创建 blob | Microsoft Docs
description: 本快速入门将在对象 (Blob) 存储中创建存储帐户和容器。 然后，使用适用于 Node.js 的存储客户端库将一个 Blob 上传到 Azure 存储，下载一个 Blob，然后列出容器中的 Blob。
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: 30a64ec6fd4df63eba9c35f1774c81c35fa3506f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>快速入门：使用 Node.js 上传、下载和列出 Blob

本快速入门介绍如何使用 Node.js 上传、下载和列出 Azure Blob 存储中容器内的块 Blob。

若要完成本快速入门，需要一个 [Azure 订阅](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)是简单的 Node.js 控制台应用程序。 若要开始，请使用以下命令将存储库克隆到计算机：

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

若要打开应用程序，请找到 *storage-blobs-node-quickstart* 文件夹，然后在最常用的代码编辑环境中将其打开。

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

在运行应用程序之前，必须为存储帐户提供连接字符串。 示例存储库包括名为 *.env.example* 的文件。 可以通过删除 *.example* 扩展来重命名此文件，这样会生成名为 *.env* 的文件。 在 *.env* 文件中，请将连接字符串值添加到 *AZURE_STORAGE_CONNECTION_STRING* 键之后。

## <a name="install-required-packages"></a>安装所需程序包

在应用程序目录中运行 *npm install*，以安装应用程序所需的包。

```bash
npm install
```

## <a name="run-the-sample"></a>运行示例
安装依赖项以后，即可将命令传递给脚本，以便运行示例。 例如，若要创建 Blob 容器，请运行以下命令：

```bash
node index.js --command createContainer
```

可用命令包括：

| 命令 | 说明 |
|---------|---------|
|*createContainer* | 创建名为 *test-container* 的容器（即使容器已存在，此操作也会成功） |
|*上传*          | 将 *example.txt* 文件上传到 *test-container* 容器 |
|*下载*        | 将 *example* Blob 的内容下载到 *example.downloaded.txt* |
|*delete*          | 删除 *example* Blob |
|*list*            | 将 *test-container* 容器的内容列出到控制台中 |


## <a name="understanding-the-sample-code"></a>了解示例代码
以下代码示例使用一些模块与文件系统和命令行进行接口通信。 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

模块的用途如下： 

- *dotenv* 将 *.env* 文件中定义的环境变量加载到当前的执行上下文中
- 若要确定要上传到 Blob 存储的文件的绝对路径，*path* 是必需的
- *yargs* 可以公开一个简单的接口，用于访问命令行参数
- *azure-storage* 是适用于 Node.js 的 [Azure 存储 SDK](https://docs.microsoft.com/javascript/api/azure-storage) 模块

接下来是初始化一系列变量：

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

这些变量设置为以下值：

- *blobService* 设置为 Azure Blob 服务的新实例
- *containerName* 设置为容器的名称
- *sourceFilePath* 设置为要上传的文件的绝对路径
- 创建 *blobName* 时可以使用文件名，但需删除文件扩展名

在以下实现中，每个 *blobService* 函数都包装在 *Promise* 中，因此可以访问 JavaScript 的 *async* 函数和 *await* 运算符，以便简化 [Azure 存储 API](/nodejs/api/azure-storage/blobservice) 的回调性质。 针对每个函数返回成功的响应后，promise 会使用相关的数据和特定于操作的消息进行解析。

### <a name="create-a-blob-container"></a>创建 Blob 容器

*createContainer* 函数调用 [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) 并为 Blob 设置相应的访问级别。

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists** 的第二个参数（选项）接受一个值作为 [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)。 如果 *publicAccessLevel* 的值为 *blob*，则表示会公开特定的 Blob 数据。 此设置不同于容器级别的访问权限，后者授予列出容器内容的权限。

使用 **createContainerIfNotExists** 时，应用程序可以多次运行 *createContainer* 命令，在容器存在的情况下也不会返回错误。 在生产环境中，通常只调用 **createContainerIfNotExists** 一次，因为在应用程序中，从头至尾使用的是同一容器。 在这种情况下，可以通过门户或 Azure CLI 提前创建容器。

### <a name="upload-a-blob-to-the-container"></a>将 Blob 上传到容器中

*upload* 函数通过 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) 函数将文件从文件系统上传和写入（或重写）到 Blob 存储中。 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
就示例应用程序来说，名为 *example.txt* 的文件上传到名为 *example* 的 Blob，后者位于名为 *test-container* 的容器中。 其他适用于将内容上传到 Blob 中的方法涉及到使用[文本](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText)和[流](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream)。

为了验证文件是否已上传到 Blob 存储，可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer/)查看帐户中的数据。

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

*list* 函数在调用 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) 方法后会返回一个列表，其中包含容器中的 Blob 元数据。 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

调用 *listBlobsSegmented* 会返回 [BlobResult](/nodejs/api/azure-storage/blobresult) 实例数组形式的 Blob 元数据。 结果以 5,000 增量批次（段）的方式返回。 如果容器中有 5,000 个以上的 Blob，则结果中会包含一个 **continuationToken** 值。 若要列出 Blob 容器中的后续段，可以将继续标记作为第二个参数传递回 **listBlobSegmented** 中。

### <a name="download-a-blob-from-the-container"></a>从容器下载 Blob

*download* 函数使用 [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) 将 Blob 的内容下载到给定的绝对文件路径。

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
此处显示的实现更改了源文件路径，目的是将 *.downloaded.txt* 追加到文件名。 在实际上下文中，可以在选择下载目标时更改位置和文件名。

### <a name="delete-blobs-in-the-container"></a>删除容器中的 Blob

*deleteBlock* 函数（别名为 *delete* 控制台命令）调用 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 函数。 顾名思义，此函数不返回“Blob 已删除”错误。

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>上传并列出

使用 promise 的一大好处是能够将命令链接到一起。 **uploadAndList** 函数演示了在上传某个文件以后，可以很容易地直接列出 Blob 的内容。

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>调用代码

若要将实现的函数公开给命令行，可以将每个函数映射到对象文字。

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

设置好 *_module* 以后，即可从命令行获取每个命令。

```javascript
const commandExists = () => exists = !!_module[args.command];
```

如果给定的命令不存在，则会将 *_module* 的属性呈现给控制台，作为帮助文本提供给用户。 

函数 *executeCommand* 为 *async* 函数，它使用 *await* 运算符调用给定的命令，并将任何消息或数据记录到控制台。

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

最后，执行代码先调用 *commandExists* 来验证是否已将已知命令传递到脚本中。 如果选择了现有命令，则会运行该命令并将任何错误记录到控制台。

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>清理资源
如果不打算使用本文中创建的数据或帐户，则为了避免意外收费，需要删除它们。 若要删除 Blob 和容器，可以使用 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) 和 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_) 方法。 也可[通过门户](../common/storage-create-storage-account.md)来删除存储帐户。

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>用于通过 Blob 开发 Node.js 应用程序的资源

请查看以下其他资源，了解如何通过 Blob 存储进行 Node.js 开发：

### <a name="binaries-and-source-code"></a>二进制文件和源代码

- 在 GitHub 上查看并安装 Azure 存储的 [Node.js 客户端库源代码](https://github.com/Azure/azure-storage-node)。

### <a name="client-library-reference-and-samples"></a>客户端库参考和示例

- 参阅 [Node.js API 参考](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage)，详细了解 Node.js 客户端库。
- 浏览使用 Node.js 客户端库编写的 [Blob 存储示例](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Node.js 将文件从本地磁盘上传到 Azure Blob 存储。 要深入了解如何使用 Blob 存储，请继续学习 Blob 存储操作说明。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-nodejs-how-to-use-blob-storage.md)

有关 Azure 存储的 Node.js 参考，请参阅 [azure-storage package](https://docs.microsoft.com/javascript/api/azure-storage)。