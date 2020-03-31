---
title: 将内容从 Azure 存储服务到 Linux 容器
description: 了解如何在 Azure 应用服务中将自定义网络共享附加到 Linux 容器。 在应用之间共享文件、远程管理静态内容和在本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297911"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>从 Linux 上的应用服务中的 Azure 存储提供内容

> [!NOTE]
> 本文适用于 Linux 容器。 要部署到自定义 Windows 容器，请参阅[在应用服务上的 Windows 容器中配置 Azure 文件](../configure-connect-to-azure-storage.md)。 Linux 上应用服务中的 Azure 存储是一个**预览**功能。 生产**方案不支持**此功能。
>

本指南演示如何将 Azure 存储附加到 Linux 上的应用服务。 优点包括安全内容、内容可移植性、持久存储、访问多个应用以及多种传输方法。

## <a name="prerequisites"></a>先决条件

- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。
- Linux[应用程序上的现有应用程序服务](https://docs.microsoft.com/azure/app-service/containers/)。
- [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 文件共享和目录](../../storage/files/storage-how-to-use-files-cli.md)。


## <a name="limitations-of-azure-storage-with-app-service"></a>使用应用服务的 Azure 存储限制

- 带有应用服务的 Azure 存储在 Linux 上的应用程序服务和容器的 Web 应用的**预览**版中。 生产**方案****不支持**它。
- 具有应用服务的 Azure 存储支持安装**Azure 文件容器**（读取/写入）和**Azure Blob 容器**（只读）
- 由于基础结构限制，使用应用服务的 Azure 存储**不支持**使用**存储防火墙**配置。
- 使用应用服务的 Azure 存储允许您指定每个应用**最多五个**装载点。
- 通过应用服务 FTP/FTP 终结点无法访问装载到应用的 Azure 存储。 使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 存储**不包括**在 Web 应用中，并单独计费。 了解有关[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage)的更多。

> [!WARNING]
> 使用 Azure Blob 存储的应用服务配置将在 2020 年 2 月变为只读。 [了解详细信息](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>使用 Azure 存储配置应用

创建[Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以使用 Azure 存储配置应用。

要将存储帐户装载到应用服务应用中的目录，请使用 该[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 存储类型可以是 AzureBlob 或 AzureFiles。 此示例中使用了 Azure 文件。


> [!CAUTION]
> 指定为 Web 应用中装载路径的目录应为空。 添加外部装载时，将删除存储在此目录中的任何内容。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

应当对要链接到存储帐户的任何其他目录执行此操作。

## <a name="verify-azure-storage-link-to-the-web-app"></a>验证 Azure 存储链接到 Web 应用

将存储容器链接到 Web 应用后，可以通过运行以下命令验证此情况：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>在 Docker 撰写中使用 Azure 存储

可以使用自定义 id 与多容器应用一起装载 Azure 存储。要查看自定义 id 名称，请查看[`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)。

在*docker-compose.yml*文件中，将`volumes`选项映射到`custom-id`。 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>后续步骤

- [在 Azure 应用服务 中配置 Web 应用](../configure-common.md)。

