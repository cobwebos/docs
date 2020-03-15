---
title: 教程：使用 Azure 事件网格自动调整上载图像的大小
description: 教程：Azure 事件网格可以触发 Azure 存储中的 blob 上传。 你可以用其将上传到 Azure 存储的图像文件发送到其他服务（如 Azure Functions），以调整大小并进行其他改进。
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: 6b3375ea8c82ce916f3d6a5e0e29f2845400cc76
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117765"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>教程：使用事件网格自动调整上传图像的大小

[Azure 事件网格](overview.md)是一项用于云的事件处理服务， 可使你创建由 Azure 服务或第三方资源引发的事件的订阅。  

本教程是存储教程系列中的第二部分。 它扩展了[以前的存储教程][previous-tutorial]，以添加使用 Azure 事件网格和 Azure Functions 自动生成无服务器缩略图的功能。 事件网格可使 [Azure Functions](../azure-functions/functions-overview.md) 响应 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)事件，并生成上传图像的缩略图。 针对 Blob 存储创建事件会创建一个事件订阅。 当将 blob 添加到特定 Blob 存储容器时，将调用一个函数终结点。 从事件网格传递到函数绑定的数据用于访问 blob 并生成缩略图。

可以使用 Azure CLI 和 Azure 门户将调整大小功能添加到现有图像上传应用。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![在浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![在浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure 存储帐户
> * 使用 Azure Functions 部署无服务器代码
> * 在事件网格中创建 Blob 存储事件订阅

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

为完成此教程：

必须已完成以前的 Blob 存储教程：[使用 Azure 存储将映像数据上传到云中][previous-tutorial]。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

如果之前未在订阅中注册事件网格资源提供程序，请确保已注册。

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求使用 Azure CLI 2.0.14 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

如果不使用 Cloud Shell，必须先使用 `az login` 进行登录。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Azure Functions 需要一个常规存储帐户。 除了在上一教程中创建的 Blob 存储帐户，另请使用 [az storage account create](/cli/azure/storage/account) 命令在资源组中创建一个常规的独立存储帐户。 存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。

1. 设置一个变量，用于存储在上一教程中创建的资源组的名称。

    ```azurecli-interactive
    resourceGroupName="myResourceGroup"
    ```
2. 为 Azure Functions 所需的新存储帐户的名称设置一个变量。
    ```azurecli-interactive
    functionstorage="<name of the storage account to be used by the function>"
    ```
3. 为 Azure 函数创建存储帐户。

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>创建函数应用  

必须使用 Function App 托管函数的执行。 Function App 提供一个环境，以便在不使用服务器的情况下执行函数代码。 使用 [az functionapp create](/cli/azure/functionapp) 命令创建 Function App。

在以下命令中，请提供你自己的唯一的函数应用名称。 函数应用名称用作该函数应用的默认 DNS 域，因此，该名称需要在 Azure 的所有应用中保持唯一。

1. 为将要创建的函数应用指定一个名称。

    ```azurecli-interactive
    functionapp="<name of the function app>"
    ```
2. 创建 Azure 函数。

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location southeastasia \
      --functions_version 2
    ```

现在配置函数应用，以连接到你在[上一教程][previous-tutorial]中创建的 Blob 存储帐户。

## <a name="configure-the-function-app"></a>配置函数应用

该函数需要 Blob 存储帐户的凭据，这些凭据是使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 命令添加到函数应用的应用程序设置的。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount="<name of the Blob storage account you created in the previous tutorial>"

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` 设置使函数应用在 Azure Functions 运行时的 2.x 版上运行。

现在可以将函数代码项目部署到此函数应用。

## <a name="deploy-the-function-code"></a>部署函数代码 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

[GitHub](https://github.com/Azure-Samples/function-image-upload-resize) 上提供示例 C# 重设大小函数。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 命令将此代码项目部署到函数应用。

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

[GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node) 上提供示例 Node.js 重设大小函数。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 命令将此函数代码项目部署到函数应用。

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```
---

图像大小调整函数由事件网关服务发送的 HTTP 请求触发。 可以通过创建事件订阅来告知事件网格：你需要在函数的 URL 处获取这些通知。 在本教程中，你订阅到 Blob 创建的事件。

从事件网格通知传递到函数的数据包括 Blob 的 URL。 该 URL 反过来传递到输入绑定，以便从 Blob 存储获取上传的图像。 该函数生成缩略图，并将生成的流写入 Blob 存储中的单独容器。

此项目使用 `EventGridTrigger` 作为触发器类型。 建议使用事件网格触发器而不是泛型 HTTP 触发器。 事件网格会自动验证事件网格函数触发器。 使用泛型 HTTP 触发器时，必须实现[验证响应](security-authentication.md)。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

若要详细了解此函数，请参阅 [function.json 和 run.csx 文件](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions)。

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

若要详细了解此函数，请参阅 [function.json 和 index.js 文件](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)。

---

函数项目代码直接从公共示例存储库部署。 要了解有关 Azure Functions 部署选项的详细信息，请参阅 [Azure Functions 的持续部署](../azure-functions/functions-continuous-deployment.md)。

## <a name="create-an-event-subscription"></a>创建事件订阅

事件订阅指示要发送到特定终结点的提供程序生成的事件。 在这种情况下，终结点由函数公开。 使用以下步骤创建一个事件订阅，以便向 Azure 门户中的函数发送通知：

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单上选择“所有服务”，然后选择“Function App”。  

    ![在 Azure 门户中导航到“函数应用”](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 展开函数应用，选择 **Thumbnail** 函数，然后选择“添加事件网格订阅”  。

    ![在 Azure 门户中导航到“添加事件网格订阅”](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 使用表中指定的事件订阅设置。
    
    ![基于 Azure 门户中的函数创建事件订阅](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **名称** | imageresizersub | 标识新事件订阅的名称。 |
    | 主题类型  | 存储帐户 | 选择存储帐户事件提供程序。 |
    | **订阅** | Azure 订阅 | 默认情况下，选择当前的 Azure 订阅。 |
    | **资源组** | myResourceGroup | 选择“使用现有”  ，然后选择此教程中使用的资源组。 |
    | **资源** | 你的 Blob 存储帐户 | 使用你创建 Blob 存储帐户。 |
    | 事件类型  | 已创建 blob | 除“已创建 Blob”以外，取消选中所有其他类型  。 只有 `Microsoft.Storage.BlobCreated` 的事件类型传递给函数。 |
    | **终结点类型** | 自动生成 | 预定义为 **Azure Function**。 |
    | **终结点** | 自动生成 | 使用为你生成的终结点 URL。 |

4. 切换到“筛选器”  选项卡，然后执行以下操作：
    1. 选择“启用主题筛选”  选项。
    2. 对于“主题开头为”  ，输入以下值： **/blobServices/default/containers/images/blobs/** 。

        ![指定事件订阅筛选器](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

5. 选择“创建”  以添加事件订阅。 这将创建一个事件订阅，当将一个 blob 添加到 `images` 容器时，该事件订阅将触发 `Thumbnail` 函数。 此函数重设图像大小，然后将图像添加到 `thumbnails` 容器。

至此，已配置后端服务，可在示例 Web 应用中测试调整图像大小功能。

## <a name="test-the-sample-app"></a>测试示例应用

要在 Web 应用中测试调整图像大小功能，请浏览到已发布应用的 URL。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

单击“上传照片”  区域，选择并上传文件。 或者，也可以将照片拖动到此区域。

请注意，上传的图像消失后，上传图像的副本将显示在“生成的缩略图”  轮播中。 此图像在通过函数重设大小后会被添加到 *thumbnails* 容器中，再由 Web 客户端下载。

![在浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

单击“选择文件”  选择一个文件，然后单击“上传图像”  。 上传成功后，浏览器会导航到一个成功页面。 单击返回到主页的链接。 已上传图像的副本将显示在“生成的缩略图”  区域中。 （如果一开始没有显示图像，请尝试重新加载页面。）此图像在通过函数重设大小后会被添加到 *thumbnails* 容器中，再由 Web 客户端下载。

![在浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 创建常规 Azure 存储帐户
> * 使用 Azure Functions 部署无服务器代码
> * 在事件网格中创建 Blob 存储事件订阅

请继续学习存储教程系列的第三部分，了解如何安全访问存储帐户。

> [!div class="nextstepaction"]
> [安全访问云中的应用程序数据](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ 要详细了解事件网格，请参阅 [Azure 事件网格简介](overview.md)。
+ 若要尝试了解有关 Azure Functions 的其他教程，请参阅[创建与 Azure 逻辑应用集成的函数](../azure-functions/functions-twitter-email.md)。

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
