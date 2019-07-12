---
title: 配置使用 Azure 文件存储
description: 了解如何配置并连接到 Windows 容器中应用服务上的 Azure 文件。
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789041"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>在应用服务上的 Windows 容器中配置 Azure 文件

> [!NOTE]
> 本文适用于自定义 Windows 容器。 若要部署到应用服务上_Linux_，请参阅[从 Azure 存储提供内容](./containers/how-to-serve-content-from-azure-storage.md)。
>

本指南演示如何访问在 Windows 容器中的 Azure 存储。 仅[Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)并[高级文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare)支持。 在本操作指南中使用 Azure 文件共享。 优势包括内容受保护，内容可移植，可以访问多个应用以及使用多种传输方法。

## <a name="prerequisites"></a>系统必备

- [Azure CLI](/cli/azure/install-azure-cli)（2.0.46 或更高版本）。
- [在 Azure 应用服务中现有的 Windows 容器应用](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [创建 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [将文件上传到 Azure 文件共享](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Azure 文件是非默认存储和单独计费，未包含的 web 应用。 它不支持使用防火墙配置，因为基础结构限制。
>

## <a name="link-storage-to-your-web-app-preview"></a>将存储链接到 Web 应用（预览版）

 若要将 Azure 文件共享装载到应用服务应用程序中的目录，请使用[ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add)命令。 存储类型必须为 AzureFiles。

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

为任何其他的目录，你想要链接到 Azure 文件共享时，应执行此操作。

## <a name="verify"></a>验证

后的 Azure 文件共享链接到 web 应用后，可以通过运行以下命令来验证：

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>后续步骤

- [将 ASP.NET 应用迁移到 Azure 应用服务中使用 Windows 容器 （预览）](app-service-web-tutorial-windows-containers-custom-fonts.md)。