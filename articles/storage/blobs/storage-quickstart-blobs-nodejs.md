---
title: "Azure 快速入门 - 使用 Node.js 从 Azure Blob 存储转入/转出对象 | Microsoft 文档"
description: "快速了解如何使用 Node.js 从 Azure Blob 存储转入/转出对象"
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: 9ea7f77d3bbe45de49c798fe3d51151e1a5a6658
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>使用 Node.js 从 Azure Blob 存储转入/转出对象

本快速入门介绍如何使用 Node.js 上传、下载和列出 Azure Blob 存储中容器内的块 blob。

## <a name="prerequisites"></a>先决条件

完成本快速入门教程：

* 安装 [Node.js](https://nodejs.org/en/)

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下载示例应用程序

本快速入门中使用的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)是基本的控制台应用程序。 

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

此命令会将存储库克隆到本地 git 文件夹。 若要打开应用程序，请找到“storage-blobs-node-quickstart”文件夹，将其打开并双击“index.js”。

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串

在应用程序中，必须为存储帐户提供连接字符串。 打开 `index.js` 文件，查找 `connectionString` 变量。 请将整个连接字符串的值替换为从 Azure 门户保存的值。 存储连接字符串应类似于以下连接字符串：

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>安装所需程序包

在应用程序目录运行 `npm install`，以安装 `package.json` 文件中列出的任何所需程序包。

```javascript
npm install
```

## <a name="run-the-sample"></a>运行示例

此示例在“我的文档”中创建一个测试文件，将其上传到 Blob 存储，列出容器中的 blob，然后下载具有新名称的文件，以便比较旧文件和新文件。

通过键入 `node index.js` 运行示例。 以下输出来自 Windows 系统。  如果使用 Linux，则可生成具有正确文件路径的类似输出。

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

继续前，请在“我的文档”中查看这两个文件。 可以打开它们，并看到它们完全相同。

还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。

验证文件后，按任意键可完成演示并删除测试文件。 至此，你已了解此示例的用途，接下来打开 index.js 文件来查看代码。 

## <a name="get-references-to-the-storage-objects"></a>获取对存储对象的引用

首先创建对用于访问和管理 Blob 存储的 `BlobService` 的引用。 这些对象相互关联 - 每个对象被列表中的下一个对象使用。

* 创建 [BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor) 对象的实例，指向存储帐户中的 Blob 服务。

* 创建新容器，并对容器设置权限，以便 blob 公开，只需 URL 即可对其进行访问。 容器以“quickstartcontainer-”开头。

此示例使用 [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)，因为我们想要每次运行示例时都创建新容器。 在整个应用程序中使用相同容器的生产环境中，建议仅调用一次 CreateIfNotExists。 或者可以提前创建容器，这样就无需在代码中创建它。

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

## <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 是最常用的。 它们非常适合用于存储文本和二进制数据，这也是本快速入门使用这些块 blob 的原因。

若要上传文件至 blob，请使用 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile) 方法。 此操作会创建 blob（若尚不存在），或者覆盖它（若已存在）。

示例代码创建一个用于上传和下载的本地文件，存储作为“localPath”上传的文件和“localFileToUpload”中的 blob 名称。 以下示例将文件上传到以“quickstartcontainer-”开头的容器。

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Blob 存储支持多种上传方法。 例如，若有一个内存流，则可以使用 [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream) 方法来替换 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile)。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

下一步，此应用程序使用 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) 获取容器中的文件列表。 下面的代码检索 blob 列表，然后循环访问它们，显示找到的 blob 的 URI。 可以从命令窗口中复制 URI，然后将其粘贴到浏览器以查看文件。

如果容器中的  blob 不超过 5000 个，则调用一次 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented) 可检索出所有 blob 名称。 如果容器中的 blob 超过 5000 个，服务会分批检索列表，每组 5000 个 blob，直到检索出所有 blob 名称。 因此，第一次调用此 API 时，它将返回第一组 5,000 个blob 名称和一个继续令牌。 第二次调用时，提供令牌，服务会检索下一组 blob 名称，以此类推，直到继续令牌为 null（表示已检索出所有 blob 名称）。

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

## <a name="download-blobs"></a>下载 Blob

使用 [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile) 方法将 blob 下载到本地磁盘。

以下代码下载上一部分上传的 blob，对 blob 名称添加“_DOWNLOADED”后缀，以便可以在本地磁盘上看到两个文件。 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要本快速入门中上传的 blob，可使用 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 和 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists) 删除整个容器。 这还会同时删除创建的文件（如果不需要）。 当你按下 Enter 退出应用程序时，需要在应用程序中注意这一点。

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>后续步骤

在此快速入门教程中，我们学习了如何使用 Node.js 在本地磁盘和 Azure Blob 存储之间传输文件。 要深入了解如何使用 Blob 存储，请继续学习 Blob 存储操作说明。

> [!div class="nextstepaction"]
> [Blob 存储操作说明](storage-nodejs-how-to-use-blob-storage.md)

若要详细了解存储资源管理器和 Blob，请参阅[使用存储资源管理器管理 Azure Blob 存储资源](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
