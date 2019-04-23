---
title: 从 Linux 上的 Azure 存储提供内容 - 应用服务
description: 如何从 Linux 上的应用服务中的 Azure 存储配置并提供内容。
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 02ebd0629fed9037054a5c22931f3e99c09073c4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998594"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>从 Linux 上的应用服务中的 Azure 存储提供内容

本指南展示了如何在 Linux 上的应用服务中使用 [Azure 存储](/azure/storage/common/storage-introduction)提供静态内容。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。 在本指南中，您将了解如何通过配置自定义存储在 Azure 存储空间提供的内容。

## <a name="prerequisites"></a>必备组件

- 现有的 Web 应用（Linux 上的应用服务或用于容器的 Web 应用）。
- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。

## <a name="create-azure-storage"></a>创建 Azure 存储

> [!NOTE]
> Azure 存储是非默认存储并且单独计费，不包括在 Web 应用中。
>

创建一个 [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)。

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>将文件上传到 Azure 存储

若要将本地目录上传到存储帐户，请使用如下例所示的 [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) 命令：

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>将存储链接到 Web 应用（预览版）

> [!CAUTION]
> 将 Web 应用中的现有目录链接到存储帐户将删除目录内容。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

若要将存储帐户装载到你的应用服务应用中的某个目录，请使用 [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) 命令。 存储类型可以是 AzureBlob 或 AzureFiles。 对于此容器，请使用 AzureBlob。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

应当对要链接到存储帐户的任何其他目录执行此操作。

## <a name="verify"></a>验证

将存储容器链接到 Web 应用后，可以通过运行以下命令验证此情况：

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>后续步骤

- [在 Azure 应用服务中配置 Web 应用](https://docs.microsoft.com/azure/app-service/web-sites-configure)。
