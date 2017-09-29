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
ms.topic: article
ms.date: 09/20/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 358015d6cfd9961508b209f628b2d648a75e3c2c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>使用事件网格自动调整上传图像的大小

[Azure 事件网格](overview.md)是一项用于云的事件处理服务， 可使你创建由 Azure 服务或第三方资源引发的事件的订阅。  

本教程是存储教程系列中的第二部分。 它对[前面存储教程][previous-tutorial]的内容进行了扩充，以学习如何使用 Azure 事件网格和 Azure Functions 添加无服务器自动缩略图生成。 事件网格可使 [Azure Functions](..\azure-functions\functions-overview.md) 响应 [Azure Blob 存储](..\storage\blobs\storage-blobs-introduction.md)事件，并生成上传图像的缩略图。 针对 Blob 存储创建事件会创建一个事件订阅。 当将 blob 添加到特定 Blob 存储容器时，将调用一个函数终结点。 从事件网格传递到函数绑定的数据用于访问 blob 并生成缩略图。 

可以使用 Azure CLI 和 Azure 门户将调整大小功能添加到现有图像上传应用。

[!INCLUDE [storage-events-note.md](../../includes/storage-events-note.md)]

![在 Edge 浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建常规 Azure 存储帐户
> * 使用 Azure Functions 部署无服务器代码
> * 在事件网格中创建 Blob 存储事件订阅

## <a name="prerequisites"></a>先决条件

若要完成本教程：

+ 必须完成以前的 Blob 存储教程：[使用 Azure 存储上传云中的图像数据][previous-tutorial]。 
+ 必须申请并被授予 Blob 存储事件功能的访问权限。 [请求访问 Blob 存储事件](#request-storage-access)，然后再继续执行本主题中的其他步骤。  

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题需要运行 Azure CLI 2.0.14 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

如果不使用云 Shell，则必须先使用 `az login` 登录。

## <a name="enable-blob-storage-events"></a>启用 Blob 存储事件

此时，你必须请求访问 Blob 存储事件功能。  

### <a name="request-storage-access"></a>请求访问 Blob 存储事件

使用 `az feature register` 命令请求访问。

> [!IMPORTANT]  
> 我们将按照 Blob 存储事件预览参与者要求加入的顺序接受他们。 可能会有 1-2 个工作日的延迟才能授予访问此功能的权限。 

```azurecli-interactive
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```

### <a name="check-access-status"></a>检查批准状态

你将收到来自 Microsoft 的一封电子邮件，通知你已获准访问 Blob 存储事件。 你可以随时通过 `az feature show` 命令验证访问请求的状态。

```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid --query properties.state
```
获得 Blob 存储事件功能的访问权限后，此命令将返回 `"Registered"` 值。 
 
注册后，你可以继续学习本教程。

## <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Azure Functions 需要一个常规存储帐户。 使用 [az storage account create](/cli/azure/storage/account#create) 命令在资源组中创建一个常规的独立存储帐户。

存储帐户名称长度必须介于 3-24 个字符，并且只能包含数字和小写字母。 

在以下命令中，请将 `<general_storage_account>` 占位符替换为自己的常规存储帐户的全局唯一名称。 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>创建 Function App  

必须使用 Function App 托管函数的执行。 Function App 提供一个环境，以便在不使用服务器的情况下执行函数代码。 使用 [az functionapp create](/cli/azure/functionapp#create) 命令创建 Function App。 

在以下命令中，请在看到 `<function_app>` 占位符的位置替换为自己的唯一函数应用名称。 `<function_app>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 在这种情况下，`<general_storage_account>` 是你创建的常规存储帐户的名称。  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

现在，你必须配置功能应用程序以连接到 blob 存储。 

## <a name="configure-the-function-app"></a>配置函数应用

该函数需要连接字符串以连接到 blob 存储帐户。 在这种情况下，`<blob_storage_account>` 是你在上一教程中创建的 Blob 存储帐户的名称。 使用 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) 命令获得连接字符串。 缩略图容器名称也必须设置为 `thumbs`。 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) 命令将这些应用程序设置添加到函数应用。

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

执行图像大小调整的 C# 函数可在此[示例 GitHub 存储库](https://github.com/Azure-Samples/function-image-upload-resize)中找到。 使用 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) 命令将此函数代码项目部署到函数应用。 

在以下命令中，`<function_app>` 是你在上一个脚本中创建的同一个函数应用。

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

图像大小调整功能由 Blob 创建事件的事件订阅触发。 传递给触发器的数据包括 blob 的 URL，此 URL 接着会传递给输入绑定，以从 Blob 存 储中获取上传的图像。 该函数生成缩略图，并将生成的流写入 Blob 存储中的单独容器。 要了解有关此函数的详细信息，请参阅[示例存储库中的自述文件](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md)。
 
函数项目代码直接从公共示例存储库部署。 要了解有关 Azure Functions 部署选项的详细信息，请参阅 [Azure Functions 的持续部署](../azure-functions/functions-continuous-deployment.md)。

## <a name="create-your-event-subscription"></a>创建事件订阅

事件订阅指示要发送到特定终结点的提供程序生成的事件。 在这种情况下，终结点由函数公开。 使用以下步骤基于 Azure 门户中的函数创建事件订阅： 

1. 在 [Azure 门户](https://portal.azure.com)中，单击左下角的箭头以展开所有服务，在“筛选器”字段内键入 `functions`，然后选择“Function App”。 

    ![在 Azure 门户中浏览到 Function App](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 展开函数应用，选择“imageresizerfunc”函数，然后选择“添加事件网格订阅”。

    ![在 Azure 门户中浏览到 Function App](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 使用表中指定的事件订阅设置。

    ![基于 Azure 门户中的函数创建事件订阅](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | 名称 | imageresizersub | 标识新事件订阅的名称。 | 
    | 主题类型 |  存储帐户 | 选择存储帐户事件提供程序。 | 
    | 订阅 | 订阅 | 默认情况下，应选择当前订阅。   |
    | 资源组 | myResourceGroup | 选择“使用现有”，然后选择此主题中使用的资源组。  |
    | 实例 |  `<blob_storage_account>` |  使用你创建 Blob 存储帐户。 |
    | 事件类型 | 已创建 blob | 除“已创建 Blob”以外，取消选中所有其他类型。 只有 `Microsoft.Storage.BlobCreated` 的事件类型传递给函数。| 
    | 订阅者终结点 | 自动生成 | 使用为你生成的终结点 URL。 | 
    | 前缀筛选器 | /blobServices/default/containers/images/blobs/ | 仅筛选图像容器上的那些存储事件。| 

4. 单击“创建”以添加事件订阅。 当将 blob 添加到图像容器时，这将创建可触发 imageresizerfunc 的事件订阅。 调整后的图像将添加到缩略图容器。

至此，已配置后端服务，可在示例 Web 应用中测试调整图像大小功能。 

## <a name="test-the-sample-app"></a>测试示例应用

要在 Web 应用中测试调整图像大小功能，请浏览到已发布应用的 URL。 Web 应用的默认 URL 为 `https://<web_app>.azurewebsites.net`。

单击“上传照片”区域，选择并上传文件。 或者，也可以将照片拖动到此区域。 

注意，上传的图像消失后，上传图像的副本将显示在生成的缩略图轮廓中。 该图像通过此功能调整大小，并添加到缩略图容器中，然后由 Web 客户端下载。 

![在 Edge 浏览器中发布的 Web 应用](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何执行以下操作：

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
