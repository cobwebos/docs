---
title: 添加自定义存储（Windows 容器）
description: 了解如何在 Azure 应用服务中的自定义 Windows 容器中附加自定义网络共享。 在应用之间共享文件、远程管理静态内容和在本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120668"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在应用服务上的 Windows 容器中配置 Azure 文件

> [!NOTE]
> 本文适用于自定义 Windows 容器。 要部署到_Linux_上的应用服务，请参阅[从 Azure 存储中提供内容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南演示如何访问 Windows 容器中的 Azure 存储。 仅支持[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)和[高级文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)。 在此操作操作中使用 Azure 文件共享。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。

## <a name="prerequisites"></a>先决条件

- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。
- [Azure 应用服务中的现有 Windows 容器应用](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [创建 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [将文件上载到 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 文件是非默认存储，单独计费，未包含在 Web 应用中。 由于基础结构限制，它不支持使用防火墙配置。
>

## <a name="limitations"></a>限制

- Windows 容器中的 Azure 存储**处于预览状态**，**不支持****生产方案**。
- Windows 容器中的 Azure 存储仅支持安装**Azure 文件容器**（读/写）。
- 当前**不支持**在 Windows 应用服务计划上携带自己的代码方案在 Windows 容器中的 Azure 存储。
- 由于基础结构限制，Windows 容器中的 Azure 存储**不支持**使用**存储防火墙**配置。
- Windows 容器中的 Azure 存储允许您指定每个应用**最多五个**装载点。
- 通过应用服务 FTP/FTP 终结点无法访问装载到应用的 Azure 存储。 使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
- Azure 存储是独立计费的，**不包括**在 Web 应用中。 了解有关[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage)的更多。

## <a name="link-storage-to-your-web-app-preview"></a>将存储链接到 Web 应用（预览版）

 要将 Azure 文件共享装载到应用服务应用中的目录，请使用 命令[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)。 存储类型必须是 Azure 文件。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

对于要链接到 Azure 文件共享的任何其他目录，应执行此操作。

## <a name="verify"></a>Verify

将 Azure 文件共享链接到 Web 应用后，可以通过运行以下命令来验证这一点：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>后续步骤

- [使用 Windows 容器（预览）将ASP.NET应用迁移到 Azure 应用服务](app-service-web-tutorial-windows-containers-custom-fonts.md)。
