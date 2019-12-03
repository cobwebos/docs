---
title: 添加自定义存储（Windows 容器）
description: 了解如何在 Azure App Service 中的自定义 Windows 容器中附加自定义网络共享。 在应用之间共享文件、远程管理静态内容和本地访问等。
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: ad70bbe36369c03225079d1194043e6ceb109c6f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671012"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在应用服务上的 Windows 容器中配置 Azure 文件

> [!NOTE]
> 本文适用于自定义 Windows 容器。 若要部署到_Linux_上的应用服务，请参阅[从 Azure 存储提供内容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南说明如何访问 Windows 容器中的 Azure 存储。 仅支持[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)和[高级文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)。 本操作说明中使用的是 Azure 文件共享。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。

## <a name="prerequisites"></a>必备组件

- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。
- [Azure App Service 中的现有 Windows 容器应用](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [创建 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [将文件上传到 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 文件是非默认存储并单独计费，不包括在 web 应用中。 由于基础结构的限制，它不支持使用防火墙配置。
>

## <a name="link-storage-to-your-web-app-preview"></a>将存储链接到 Web 应用（预览版）

 若要将 Azure 文件共享装载到应用服务应用中的目录，请使用[`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 存储类型必须为 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

应为要链接到 Azure 文件共享的任何其他目录执行此操作。

## <a name="verify"></a>验证

将 Azure 文件共享链接到 web 应用后，可以通过运行以下命令来验证此内容：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>后续步骤

- [使用 Windows 容器（预览版）将 ASP.NET 应用迁移到 Azure App Service](app-service-web-tutorial-windows-containers-custom-fonts.md)。
