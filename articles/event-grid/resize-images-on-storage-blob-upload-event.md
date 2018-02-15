---
title: "使用 Azure 事件网格自动调整上传图像的大小 | Microsoft 文档"
description: "Azure 事件网格可以触发 Azure 存储中的 blob 上传。 你可以用其将上传到 Azure 存储的图像文件发送到其他服务（如 Azure Functions），以调整大小并进行其他改进。"
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: b0fccd058620537f6dcfaf37ee14c1ff0cb8857a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>使用事件网格自动调整上传图像的大小

[Azure 事件网格](overview.md)是一项用于云的事件处理服务， 可使你创建由 Azure 服务或第三方资源引发的事件的订阅。  

本教程是存储教程系列中的第二部分。 它对[前面存储教程][previous-tutorial]的内容进行了扩充，以学习如何使用 Azure 事件网格和 Azure Functions 添加无服务器自动缩略图生成。 事件网格可使 [Azure Functions](..\azure-functions\functions-overview.md) 响应 [Azure Blob 存储](..\storage\blobs\storage-blobs-introduction.md)事件，并生成上传图像的缩略图。 针对 Blob 存储创建事件会创建一个事件订阅。 当将 blob 添加到特定 Blob 存储容器时，将调用一个函数终结点。 从事件网格传递到函数绑定的数据用于访问 blob 并生成缩略图。 

可以使用 Azure CLI 和 Azure 门户将调整大小功能添加到现有图像上传应用。

![在 Edge 浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建常规 Azure 存储帐户
> * 使用 Azure Functions 部署无服务器代码
> * 在事件网格中创建 Blob 存储事件订阅

## <a name="prerequisites"></a>先决条件

完成本教程：

+ 必须完成以前的 Blob 存储教程：[使用 Azure 存储上传云中的图像数据][previous-tutorial]。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求使用 Azure CLI 2.0.14 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果不使用云 Shell，则必须先使用 `az login` 登录。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Azure Functions 需要一个常规存储帐户。 使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令在资源组中创建一个常规的独立存储帐户。

存储帐户名称必须为 3 到 24 个字符，并且只能包含数字和小写字母。 

在以下命令中，请将 `<general_storage_account>` 占位符替换为自己的常规存储帐户的全局唯一名称。 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>创建函数应用  

必须使用 Function App 托管函数的执行。 Function App 提供一个环境，以便在不使用服务器的情况下执行函数代码。 使用 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令创建 Function App。 

在以下命令中，请在看到 `<function_app>` 占位符的位置替换为自己的唯一函数应用名称。 函数应用名称用作该函数应用的默认 DNS 域，因此，该名称需要在 Azure 的所有应用中保持唯一。 请使用所创建的常规存储帐户的名称来代替 `<general_storage_account>`。

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

现在，必须对函数应用进行配置，以便连接到在[以前的教程][previous-tutorial]中创建的 Blob 存储帐户。

## <a name="configure-the-function-app"></a>配置函数应用

该函数需要连接字符串来连接到 Blob 存储帐户。 在以下步骤中部署到 Azure 的函数代码在应用设置 myblobstorage_STORAGE 中查找连接字符串，在应用设置 myContainerName 中查找缩略图容器名称。 使用 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) 命令获得连接字符串。 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令设置应用程序设置。

在以下 CLI 命令中，`<blob_storage_account>` 是在上一教程中创建的 Blob 存储帐户的名称。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

现在可以将函数代码项目部署到此函数应用。

## <a name="deploy-the-function-code"></a>部署函数代码 

进行图像大小调整的 C# 函数在[此 GitHub 存储库](https://github.com/Azure-Samples/function-image-upload-resize)中提供。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) 命令将此函数代码项目部署到函数应用。 

在以下命令中，`<function_app>` 是此前创建的函数应用的名称。

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

图像大小调整函数由事件网关服务发送的 HTTP 请求触发。 可以通过创建事件订阅来告知事件网格：你需要在函数的 URL 处获取这些通知。 在本教程中，你订阅到 Blob 创建的事件。

从事件网格通知传递到函数的数据包括 Blob 的 URL。 该 URL 反过来传递到输入绑定，以便从 Blob 存储获取上传的图像。 该函数生成缩略图，并将生成的流写入 Blob 存储中的单独容器。 

此项目使用 `EventGridTrigger` 作为触发器类型。 建议使用事件网格触发器而不是泛型 HTTP 触发器。 事件网格会自动验证事件网格函数触发器。 使用泛型 HTTP 触发器时，必须实现[验证响应](security-authentication.md#webhook-event-delivery)。

若要详细了解此函数，请参阅 [function.json 和 run.csx 文件](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/imageresizerfunc)。
 
函数项目代码直接从公共示例存储库部署。 要了解有关 Azure Functions 部署选项的详细信息，请参阅 [Azure Functions 的持续部署](../azure-functions/functions-continuous-deployment.md)。

## <a name="create-an-event-subscription"></a>创建事件订阅

事件订阅指示要发送到特定终结点的提供程序生成的事件。 在这种情况下，终结点由函数公开。 使用以下步骤创建一个事件订阅，以便向 Azure 门户中的函数发送通知： 

1. 在 [Azure 门户](https://portal.azure.com)中，单击左下角的箭头以展开所有服务，在“筛选器”字段中键入 *functions*，然后选择“Function App”。 

    ![在 Azure 门户中浏览到 Function App](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 展开函数应用，选择“imageresizerfunc”函数，然后选择“添加事件网格订阅”。

    ![在 Azure 门户中浏览到 Function App](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 使用表中指定的事件订阅设置。
    
    ![基于 Azure 门户中的函数创建事件订阅](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | imageresizersub | 标识新事件订阅的名称。 | 
    | 主题类型 |  存储帐户 | 选择存储帐户事件提供程序。 | 
    | **订阅** | Azure 订阅 | 默认情况下，选择当前的 Azure 订阅。   |
    | **资源组** | myResourceGroup | 选择“使用现有”，然后选择此教程中使用的资源组。  |
    | **实例** |  你的 Blob 存储帐户 |  使用你创建 Blob 存储帐户。 |
    | 事件类型 | 已创建 blob | 除“已创建 Blob”以外，取消选中所有其他类型。 只有 `Microsoft.Storage.BlobCreated` 的事件类型传递给函数。| 
    | **订阅者类型** |  Web 挂钩 |  选项为“Web 挂钩”或“事件中心”。 |
    | 订阅者终结点 | 自动生成 | 使用为你生成的终结点 URL。 | 
    | 前缀筛选器 | /blobServices/default/containers/images/blobs/ | 仅筛选图像容器上的那些存储事件。| 

4. 单击“创建”以添加事件订阅。 这将创建一个事件订阅。在将 Blob 添加到图像容器时，该订阅会触发 `imageresizerfunc`。 此函数重设图像大小，然后将图像添加到缩略图容器。

至此，已配置后端服务，可在示例 Web 应用中测试调整图像大小功能。 

## <a name="test-the-sample-app"></a>测试示例应用

要在 Web 应用中测试调整图像大小功能，请浏览到已发布应用的 URL。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。

单击“上传照片”区域，选择并上传文件。 或者，也可以将照片拖动到此区域。 

注意，上传的图像消失后，上传图像的副本将显示在生成的缩略图轮廓中。 此图像在通过函数重设大小后会被添加到缩略图容器中，再由 Web 客户端下载。

![在 Edge 浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建常规 Azure 存储帐户
> * 使用 Azure Functions 部署无服务器代码
> * 在事件网格中创建 Blob 存储事件订阅

请继续学习存储教程系列的第三部分，了解如何安全访问存储帐户。

> [!div class="nextstepaction"]
> [安全访问云中的应用程序数据](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ 要详细了解事件网格，请参阅 [Azure 事件网格简介](overview.md)。 
+ 若要尝试了解有关 Azure Functions 的其他教程，请参阅[创建与 Azure 逻辑应用集成的函数](..\azure-functions\functions-twitter-email.md)。 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
