---
title: 快速入门 - 使用 Azure CLI 创建 blob
titleSuffix: Azure Storage
description: 在本快速入门中，你将了解如何使用 Azure CLI 将 blob 上传到 Azure 存储、下载 blob 以及在容器中列出 blob。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892917"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>快速入门：使用 Azure CLI 创建、下载和列出 blob

Azure CLI 是 Azure 的命令行体验，用于管理 Azure 资源。 可以在浏览器中将它与 Azure Cloud Shell 配合使用。 也可将它安装在 macOS、Linux 或 Windows 上，然后从命令行运行它。 本快速入门介绍了如何使用 Azure CLI 通过 Azure Blob 存储来上传和下载数据。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>先决条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可确定你的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>创建容器

始终将 Blob 上传到容器中。 可以整理 Blob 组，就像在计算机的文件夹中整理文件一样。

可以使用 [az storage container create](/cli/azure/storage/container) 命令创建用于存储 blob 的容器。

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>上传 blob

Blob 存储支持块 blob、追加 blob 和页 blob。 本快速入门中的示例介绍如何使用块 blob。

首先，创建要上传到块 blob 的文件。 如果使用的是 Azure Cloud Shell，请使用以下命令创建文件：

```bash
vi helloworld
```

打开该文件后，按“插入”  。 键入“Hello world”，并按“Esc”   。接下来，键入“:x”，然后按“Enter”   。

此示例使用 [az storage blob upload](/cli/azure/storage/blob) 命令将 Blob 上传到在上一个步骤中创建的容器中。 由于文件是在根目录下创建的，因此无需指定文件路径：

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

此操作将创建 Blob（如果该 Blob 尚不存在），或者覆盖 Blob（如果该 Blob 已存在）。 上传尽可能多的文件，然后继续操作。

若要同时上传多个文件，则可使用 [az storage blob upload-batch](/cli/azure/storage/blob) 命令。

## <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

使用 [az storage blob list](/cli/azure/storage/blob) 命令列出容器中的 blob。

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>下载 Blob

使用 [az storage blob download](/cli/azure/storage/blob) 命令下载之前上传的 Blob。

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>使用 AzCopy 传输数据

若要按可编写脚本的方式高性能地传输 Azure 存储中的数据，还可使用 [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 实用工具。 可使用 AzCopy 将数据传输到 Blob、文件和表存储或将数据从中传出。

下面的示例使用 AzCopy 将名为 myfile.txt 的文件上传到 sample-container 容器   。 请务必将尖括号中的占位符值替换为你自己的值：

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何资源（包括在本快速入门中创建的存储帐户），请使用 [az group delete](/cli/azure/group) 命令删除该资源组。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，你了解了如何在本地文件系统和 Azure Blob 存储中的容器之间传输文件。 若要详细了解如何使用 Azure 存储中的 blob，请继续通过本教程了解如何使用 Azure Blob 存储。

> [!div class="nextstepaction"]
> [如何：通过 Azure CLI 对 Blob 存储执行操作](storage-how-to-use-blobs-cli.md)
