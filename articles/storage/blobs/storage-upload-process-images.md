---
title: 使用 Azure 存储在云中上传图像数据 | Microsoft Docs
description: 将 Azure blob 存储与 Web 应用配合使用来存储应用数据
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.openlocfilehash: e2ee959fb6fc7e8454919c71cfa20e2bb9055dfb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81393850"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>教程：使用 Azure 存储在云中上传图像数据

本教程是一个系列中的第一部分。 本教程介绍如何部署一个 Web 应用，该应用使用 Azure Blob 存储客户端库将图像上传到存储帐户。 完成后，你将具有一个通过 Azure 存储来存储和显示图像的 Web 应用。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![.NET 中的图像调整器应用](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![Node.js V10 中的图像调整器应用](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 创建容器并设置权限
> * 检索访问密钥
> * 将 Web 应用部署到 Azure
> * 配置应用设置
> * 与 Web 应用进行交互

## <a name="prerequisites"></a>先决条件

需要一个 Azure 订阅才能完成此教程。 在开始之前，创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

若要在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group)”命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。  

以下示例创建名为 `myResourceGroup` 的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>创建存储帐户

此示例将图像上传到 Azure 存储帐户中的 blob 容器。 存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。 使用 [az storage account create](/cli/azure/storage/account) 命令在创建的资源组中创建存储帐户。

> [!IMPORTANT]
> 在本教程的第 2 部分中，你将 Azure 事件网格与 Blob 存储配合使用。 请确保在支持事件网格的 Azure 区域中创建你的存储帐户。 有关受支持区域的列表，请参阅 [Azure 产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

在以下命令中，请将 `<blob_storage_account>` 占位符替换为你自己的 Blob 存储帐户的全局唯一名称。

```azurecli-interactive
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>创建 Blob 存储容器

应用使用 Blob 存储帐户中的两个容器。 容器类似于文件夹，用于存储 blob。 images  容器是应用在其中上传完整分辨率图像的位置。 在本系列的后面部分中，一个 Azure 函数应用将调整大小后的图像缩略图上传到 thumbnails  容器。

使用 [az storage account keys list](/cli/azure/storage/account/keys) 命令获取存储帐户密钥。 然后，使用此密钥通过 [az storage container create](/cli/azure/storage/container) 命令创建两个容器。

*images* 容器的公共访问权限设置为 `off`。 *thumbnails* 容器的公共访问权限设置为 `container`。 `container` 公共访问权限设置允许访问网页的用户查看缩略图。

```azurecli-interactive
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create -n images --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access off

az storage container create -n thumbnails --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container

echo "Make a note of your Blob storage account key..."
echo $blobStorageAccountKey
```

记下 Blob 存储帐户名称和密钥。 示例应用使用这些设置连接到存储帐户以上传图像。 

## <a name="create-an-app-service-plan"></a>创建应用服务计划

[应用服务计划](../../app-service/overview-hosting-plans.md)指定托管应用的 Web 服务器场的位置、大小和功能。

使用 [az appservice plan create](/cli/azure/appservice/plan) 命令创建应用服务计划。

以下示例在免费定价层中创建名为 `myAppServicePlan` 的应用服务计划： 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>创建 Web 应用

Web 应用为从 GitHub 示例存储库部署的示例应用代码提供承载空间。 使用 [az webapp create](/cli/azure/webapp) 命令在 `myAppServicePlan` 应用服务计划中创建 [Web 应用](../../app-service/overview.md)。  

在以下命令中，将 `<web_app>` 替换为唯一名称。 有效的字符是 `a-z`、`0-9` 和 `-`。 如果 `<web_app>` 不唯一，将收到错误消息：*具有给定名称 `<web_app>` 的网站已存在。* Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。  

```azurecli-interactive
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>从 GitHub 存储库部署示例应用

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

应用服务支持通过多种方式将内容部署到 Web 应用。 在本教程中，将从[公共 GitHub 示例存储库](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)部署 Web 应用。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令配置 Web 应用的 GitHub 部署。

示例项目包含一个 [ASP.NET MVC](https://www.asp.net/mvc) 应用。 该应用接受一个图像，将其保存到存储帐户，然后从缩略图容器显示图像。 此 Web 应用使用 [Azure.Storage](/dotnet/api/azure.storage)、[Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs) 和 [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) 命名空间与 Azure 存储服务交互。

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
应用服务支持通过多种方式将内容部署到 Web 应用。 在本教程中，将从[公共 GitHub 示例存储库](https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10)部署 Web 应用。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令配置 Web 应用的 GitHub 部署。

```azurecli-interactive
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp-node-v10
```

---

## <a name="configure-web-app-settings"></a>配置 Web 应用设置

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

示例 Web 应用使用[适用于 .NET 的 Azure 存储 API](/dotnet/api/overview/azure/storage) 上传图像。 存储帐户凭据在 Web 应用的应用设置中进行设置。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) 命令将应用设置添加到已部署的应用。

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

示例 Web 应用使用 [Azure 存储客户端库](https://github.com/Azure/azure-storage-js)请求用于上传图像的访问令牌。 存储 SDK 使用的存储帐户凭据是在 Web 应用的应用设置中设置的。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) 命令将应用设置添加到已部署的应用。

```azurecli-interactive
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

在部署并配置 Web 应用后，你可以在应用中测试图像上传功能。

## <a name="upload-an-image"></a>上传图像

若要测试 Web 应用，请浏览到已发布应用的 URL。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

选择“上传照片”  区域以指定并上传文件，或者将文件拖动到该区域上。 如果成功上传，图像会消失。 “生成的缩略图”  部分将保持为空，直到我们稍后在本主题中对其进行测试为止。

![在 .NET 中上传照片](media/storage-upload-process-images/figure1.png)

在示例代码中，*Storagehelper.cs* 文件中的 `UploadFileToStorage` 任务用于使用 [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) 方法将图像上传到存储帐户中的 images  容器。 下面的代码示例包含 `UploadFileToStorage` 任务。

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

以下是用于前一任务的类和方法：

| 类    | 方法   |
|----------|----------|
| [Uri](/dotnet/api/system.uri) | [Uri 构造函数](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential(String, String) 构造函数](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)

选择“选择文件”  以选择文件，然后单击“上传图像”  。 “生成的缩略图”  部分将保持为空，直到我们稍后在本主题中对其进行测试为止。 

![在 Node.js V10 中上传照片](media/storage-upload-process-images/upload-app-nodejs.png)

在示例代码中，`post` 路由负责将图像上传到 Blob 容器中。 此路由使用模块来帮助处理上传：

- [multer](https://github.com/expressjs/multer) 为路由处理程序实施上传策略。
- [into-stream](https://github.com/sindresorhus/into-stream) 将缓冲区转换成 [createBlockBlobFromStream](https://azure.github.io/azure-sdk-for-node/azure-storage-legacy/latest/BlobService.html) 所需要的流。

将文件发送到路由时，文件的内容仍保留在内存中，直至文件上传到 Blob 容器。

> [!IMPORTANT]
> 将大文件加载到内存中可能会对 Web 应用的性能造成负面影响。 如果预期用户会发布大文件，则可能需要考虑将文件暂存在 Web 服务器文件系统中，然后按计划将其上传到 Blob 存储。 在文件位于 Blob 存储中以后，即可将其从服务器文件系统中删除。

```javascript
const {
  Aborter,
  BlobURL,
  BlockBlobURL,
  ContainerURL,
  ServiceURL,
  StorageURL,
  SharedKeyCredential,
  uploadStreamToBlockBlob
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };
const ONE_MINUTE = 60 * 1000;
const aborter = Aborter.timeout(30 * ONE_MINUTE);

const sharedKeyCredential = new SharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(sharedKeyCredential);
const serviceURL = new ServiceURL(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, ''); 
  return `${identifier}-${originalName}`;
};

router.post('/', uploadStrategy, async (req, res) => {

    const blobName = getBlobName(req.file.originalname);
    const stream = getStream(req.file.buffer);
    const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
    const blobURL = BlobURL.fromContainerURL(containerURL, blobName);
    const blockBlobURL = BlockBlobURL.fromBlobURL(blobURL);

    try {
      
      await uploadStreamToBlockBlob(aborter, stream,
        blockBlobURL, uploadOptions.bufferSize, uploadOptions.maxBuffers);

      res.render('success', { message: 'File uploaded to Azure Blob storage.' });   

    } catch (err) {

      res.render('error', { message: 'Something went wrong.' });

    }
});
```
---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>验证图像是否显示在存储帐户中

登录 [Azure 门户](https://portal.azure.com)。 从左侧菜单中，选择“存储帐户”  ，然后选择你的存储帐户的名称。 选择“容器”，然后选择“图像”容器   。

验证图像是否显示在该容器中。

![Azure 门户图像容器列表](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>测试缩略图查看

为了测试缩略图查看，你将把图像上传到 **thumbnails** 容器，以检查应用可以读取 **thumbnails** 容器。

登录 [Azure 门户](https://portal.azure.com)。 从左侧菜单中，选择“存储帐户”  ，然后选择你的存储帐户的名称。 选择“容器”，然后选择“缩略图”容器   。 选择“上传”  以打开“上传 blob”  窗格。

使用文件选取器选择文件，然后选择“上传”  。

导航回到应用，以验证上传到 **thumbnails** 容器的图像是否可见。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)
![显示新图像的 .NET 图像调整器应用](media/storage-upload-process-images/figure2.png)

# <a name="nodejs-v10-sdk"></a>[Node.js v10 SDK](#tab/nodejsv10)
![显示新图像的 Node.js V10 图像调整器应用](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

在本系列的第二部分中，您将使缩略图创建自动化，以便不需要此图像。 在 Azure 门户中的 **thumbnails** 容器中，选择上传的图像，然后选择“删除”  以删除图像。 

可启用内容分发网络 (CDN) 来缓存 Azure 存储帐户中的内容。 有关如何对 Azure 存储帐户启用 CDN 的详细信息，请参阅：[将 Azure 存储帐户与 Azure CDN 集成](../../cdn/cdn-create-a-storage-account-with-cdn.md)。

## <a name="next-steps"></a>后续步骤

在本系列的第一部分中，你已学习了如何配置 Web 应用来与存储进行交互。

请前进到本系列的第二部分，以了解如何使用事件网格触发 Azure 函数以调整图像大小。

> [!div class="nextstepaction"]
> [使用事件网格触发 Azure 函数以调整上传的图像大小](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
