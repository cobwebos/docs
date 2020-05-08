---
title: 向 Linux 容器提供 Azure 存储中的内容
description: 了解如何在 Azure App Service 中将自定义网络共享附加到 Linux 容器。 在应用之间共享文件、远程管理静态内容和本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625317"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>从 Linux 上的应用服务中的 Azure 存储提供内容

> [!NOTE]
> 本文适用于 Linux 容器。 若要部署到自定义 Windows 容器，请参阅[在应用服务上的 Windows 容器中配置 Azure 文件](../configure-connect-to-azure-storage.md)。 Linux 应用服务中的 Azure 存储是一项**预览**功能。 **生产方案不支持**此功能。
>

本指南说明如何将 Azure 存储附加到 Linux 上的应用服务。 优点包括受保护的内容、内容可移植性、持久存储、对多个应用的访问和多种传输方法。

## <a name="prerequisites"></a>先决条件

- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。
- [Linux 应用上的现有应用服务](https://docs.microsoft.com/azure/app-service/containers/)。
- [Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- [Azure 文件共享和目录](../../storage/files/storage-how-to-use-files-cli.md)。


## <a name="limitations-of-azure-storage-with-app-service"></a>Azure 存储与应用服务的限制

- 适用于 Linux 和用于容器的 Web 应用上的应用服务的 Azure 存储处于**预览阶段**。 对于**生产方案**，**不支持**此方法。
- 带有应用服务的 azure 存储支持装载**Azure 文件容器**（读/写）和**azure Blob 容器**（只读）
- 使用应用服务的 Azure 存储**不支持**使用**存储防火墙**配置，因为存在基础结构限制。
- 利用应用服务的 Azure 存储，可以为每个应用指定**最多5个**装入点。
- 无法通过应用服务 FTP/FTPs 终结点访问装载到应用的 Azure 存储。 使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 存储**不包括**在 web 应用中，并单独进行计费。 了解有关[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage)的详细信息。

> [!WARNING]
> 使用 Azure Blob 存储的应用服务配置将在2020年2月变为只读。 [了解详细信息](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>通过 Azure 存储空间配置应用

创建[Azure 存储帐户、文件共享和目录](#prerequisites)后，现在可以通过 Azure 存储空间配置应用。

若要在应用服务应用中将存储帐户装载到目录中，请使用[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 存储类型可以是 AzureBlob 或 AzureFiles。 本示例使用 AzureFiles。 装载路径设置对应于要从 Azure 存储装载的文件夹。 将其设置为 "/" 即可装入整个 Azure 存储。


> [!CAUTION]
> 指定为 web 应用中的装载路径的目录应为空。 添加外部装载时，此目录中存储的任何内容都将被删除。 如果要迁移现有应用的文件，在开始之前，请备份你的应用及其内容。
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

应当对要链接到存储帐户的任何其他目录执行此操作。

## <a name="verify-azure-storage-link-to-the-web-app"></a>验证到 web 应用的 Azure 存储链接

将存储容器链接到 Web 应用后，可以通过运行以下命令验证此情况：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>在 Docker Compose 中使用 Azure 存储

可以使用自定义 id 在多容器应用中装载 Azure 存储。若要查看自定义 id 名称，请[`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)运行。

在*docker-compose.yml*文件中，将`volumes`选项映射到。 `custom-id` 例如：

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>后续步骤

- [在 Azure App Service 中配置 web 应用](../configure-common.md)。

