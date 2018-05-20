---
title: 使用 Azure 存储在云中上传图像数据 | Microsoft Docs
description: 将 Azure blob 存储与 Web 应用结合使用来存储应用数据
services: storage
documentationcenter: ''
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 29accb3394e9a2f6939a657172c1a5c2e411706a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>使用 Azure 存储在云中上传图像数据

本教程是一个系列中的第一部分。 本教程演示如何部署一个 Web 应用，它使用 Azure 存储客户端库将图像上传到存储帐户。 完成后，你将具有一个通过 Azure 存储来存储和显示图像的 Web 应用。

![图像容器视图](media/storage-upload-process-images/figure2.png)

在该系列的第一部分中，你会学习如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 创建容器并设置权限
> * 检索访问密钥
> * 配置应用程序设置
> * 将 Web 应用部署到 Azure
> * 与 Web 应用进行交互

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="create-a-resource-group"></a>创建资源组 

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。
 
以下示例创建名为 `myResourceGroup` 的资源组。
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-a-storage-account"></a>创建存储帐户
 
此示例将图像上传到 Azure 存储帐户中的 blob 容器。 存储帐户提供唯一的命名空间来存储和访问 Azure 存储数据对象。 使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令在创建的资源组中创建存储帐户。 

> [!IMPORTANT] 
> 在本教程的第 2 部分中，你会对 blob 存储使用事件订阅。 当前仅对美国中西部和美国西部 2 的 Blob 存储帐户支持事件订阅。 由于存在此限制，因此必须创建由示例应用用于存储图像和缩略图的 Blob 存储帐户。   

在以下命令中，请将 `<blob_storage_account>` 占位符替换成自己的 Blob 存储帐户的全局唯一名称。  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>创建 blob 存储容器
 
应用使用 Blob 存储帐户中的两个容器。 这些容器类似于文件夹，用于存储 blob。 images 容器是应用在其中上传完整分辨率图像的位置。 在本系列的后面部分中，一个 Azure 函数应用将调整大小后的图像缩略图上传到 thumbnails 容器。 

使用 [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 命令获取存储帐户密钥。 然后使用此密钥通过 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令创建两个容器。  
 
在此例中，`<blob_storage_account>` 是你创建的 Blob 存储帐户的名称。 images 容器公共访问权限设置为 `off`，thumbnails 容器公共访问权限设置为 `container`。 `container` 公共访问权限设置使访问网页的人员可以查看缩略图。
 
```azurecli-interactive 
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbnails --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

记下 blob 存储帐户名称和密钥。 示例应用使用这些设置连接到存储帐户以上传图像。 

## <a name="create-an-app-service-plan"></a>创建应用服务计划 

[应用服务计划](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)指定托管应用的 Web 服务器场的位置、大小和功能。 

使用 [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 命令创建应用服务计划。 

以下示例在免费定价层中创建名为 `myAppServicePlan` 的应用服务计划： 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>创建 Web 应用 

Web 应用为从 GitHub 示例存储库部署的示例应用代码提供承载空间。 使用 [az webapp create](/cli/azure/webapp#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建 [Web 应用](../../app-service/app-service-web-overview.md)。  
 
在以下命令中，将 `<web_app>` 替换为唯一名称（有效字符是 `a-z`、`0-9` 和 `-`）。 如果 `<web_app>` 不是唯一名称，将收到错误消息：“具有给定名称 `<web_app>` 的网站已存在”。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>从 GitHub 存储库部署示例应用 

应用服务支持通过多种方式将内容部署到 Web 应用。 在本教程中，将从[公共 GitHub 示例存储库](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)部署 Web 应用。 使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) 命令配置 Web 应用的 GitHub 部署。 将 `<web_app>` 替换为在上一步中创建的 Web 应用的名称。

示例项目包含一个 [ASP.NET MVC](https://www.asp.net/mvc) 应用，它接受图像，将其保存到存储帐户，然后从缩略图容器显示图像。 Web 应用使用 Azure 存储客户端库中的 [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet)、[Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) 和 [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) 命名空间与 Azure 存储进行交互。 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>配置 Web 应用设置 

示例 Web 应用使用 [Azure 存储客户端库](/dotnet/api/overview/azure/storage?view=azure-dotnet)请求用于上传图像的访问令牌。 在 Web 应用的应用设置中设置存储 SDK 使用的存储帐户凭据。 使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令将应用设置添加到已部署的应用。 

在下面的命令中，`<blob_storage_account>` 是 Blob 存储帐户的名称，`<blob_storage_key>` 是关联密钥。 将 `<web_app>` 替换为在上一步中创建的 Web 应用的名称。     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbnails \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Web 应用已部署并配置之后，你可以在应用中测试图像上传功能。   

## <a name="upload-an-image"></a>上传映像 

若要测试 Web 应用，请浏览到已发布应用的 URL。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。 选择“上传照片”区域以选择并上传文件，或是将文件拖放到该区域上。 如果成功上传，图像会消失。

![ImageResizer 应用](media/storage-upload-process-images/figure1.png)

在示例代码中，`Storagehelper.cs` 文件中的 `UploadFiletoStorage` 任务用于通过 [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) 方法将图像上传到存储帐户中的 `images` 容器。 下面的代码示例包含 `UploadFiletoStorage` 任务。 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

以下是用于前一任务的类和方法：

|类  |方法  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>验证图像是否显示在存储帐户中

登录到 [Azure 门户](https://portal.azure.com)。 从左侧菜单中，选择“存储帐户”，然后选择你的存储帐户的名称。 在“概览”下，选择“images”容器。

验证图像是否显示在该容器中。

![图像容器视图](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>测试缩略图查看

为了测试缩略图查看，你会将图像上传到缩略图容器，以确保应用程序可以读取缩略图容器。

登录到 [Azure 门户](https://portal.azure.com)。 从左侧菜单中，选择“存储帐户”，然后选择你的存储帐户的名称。 选择“Blob 服务”下的“容器”，然后选择“thumbnails”容器。 选择“上传”以打开“上传 blob”窗格。

使用文件选取器选择文件，然后选择“上传”。

导航回到应用，以验证上传到 **thumbnails** 容器的图像是否可见。

![图像容器视图](media/storage-upload-process-images/figure2.png)

在 Azure 门户中的 **thumbnails** 容器中，选择上传的图像，然后选择“删除”以删除图像。 在本系列的第二部分中，你会自动创建缩略图图像，因此无需此测试图像。

启用 CDN 可以缓存 Azure 存储帐户的内容。 虽然本教程中未进行介绍，不过若要了解如何对 Azure 存储帐户启用 CDN，可以访问：[将 Azure 存储帐户与 Azure CDN 集成](../../cdn/cdn-create-a-storage-account-with-cdn.md)。

## <a name="next-steps"></a>后续步骤

在本系列的第一部分中，你了解了如何配置与存储交互的 Web 应用，例如如何：

> [!div class="checklist"]
> * 创建存储帐户
> * 创建容器并设置权限
> * 检索访问密钥
> * 配置应用程序设置
> * 将 Web 应用部署到 Azure
> * 与 Web 应用进行交互

前进到本系列的第二部分，以了解如何使用事件网格触发 Azure 函数以调整图像大小。

> [!div class="nextstepaction"]
> [使用事件网格触发 Azure 函数以调整上传的图像大小](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
