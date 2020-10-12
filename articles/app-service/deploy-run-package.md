---
title: 从 ZIP 包运行应用
description: 部署应用的具有原子性的 ZIP 包。 提高应用在 ZIP 部署过程中的行为的可预测性和可靠性。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 5cc909d79b3f5ea2b4c6a3da12bc7250addbe00c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77920716"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>直接从 ZIP 包运行 Azure 应用服务中的应用

在 [Azure 应用服务](overview.md)中，可以直接从部署 ZIP 包文件运行应用。 本文将介绍如何在应用中启用此功能。

应用服务中的所有其他部署方法具有一定的共同之处：文件将部署到应用中的 *D:\home\site\wwwroot*（或 Linux 应用的 */home/site/wwwroot*）。 由于应用在运行时使用同一个目录，因此部署可能会因文件锁定冲突而失败，并且应用可能会由于某些文件尚未更新而出现不可预测的行为。

相反，直接从包运行时，包中的文件不会复制到 *wwwroot* 目录， 而是将 ZIP 包本身直接装载为只读的 *wwwroot* 目录。 从包直接运行可提供多种好处：

- 消除部署与运行时之间的文件锁定冲突。
- 确保在任意时候只运行完全部署的应用。
- 可部署到生产应用（需重启）。
- 改善 Azure 资源管理器部署的性能。
- 可以减少冷启动时间，特别是对于具有大型 npm 包树的 JavaScript 函数。

> [!NOTE]
> 目前仅支持 ZIP 包文件。

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>启用从包运行的功能

`WEBSITE_RUN_FROM_PACKAGE` 应用设置启用从包运行的功能。 若要指定此设置，请使用 Azure CLI 运行以下命令。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` 允许从应用本地的包运行应用。 也可以[从远程包运行](#run-from-external-url-instead)。

## <a name="run-the-package"></a>运行包

在应用服务中运行包的最简单方法是使用 Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 命令。 例如：

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

由于已指定 `WEBSITE_RUN_FROM_PACKAGE` 应用设置，因此，此命令不会将包内容解压缩到应用的 *D:\home\site\wwwroot* 目录。 而是将 ZIP 文件按原样上传到 *D:\home\data\SitePackages*，并在同一目录中创建 *packagename.txt*，其中包含要在运行时加载的 ZIP 包的名称。 如果以不同的方式（例如使用 [FTP](deploy-ftp.md)）上传 ZIP 包，需要手动创建 *D:\home\data\SitePackages* 目录和 *packagename.txt* 文件。

该命令还会重启应用。 由于设置了 `WEBSITE_RUN_FROM_PACKAGE`，应用服务会将上传的包装载为只读的 *wwwroot* 目录，并直接从该装载目录运行应用。

## <a name="run-from-external-url-instead"></a>改为从外部 URL 运行

还可以从外部 URL（例如 Azure Blob 存储）运行包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。 应通过[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 使用专用存储容器，使应用服务运行时能够安全访问包。 

将文件上传到 Blob 存储并获取该文件的 SAS URL 后，请将 `WEBSITE_RUN_FROM_PACKAGE` 应用设置指定为该 URL。 以下示例使用 Azure CLI 执行此操作：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

如果将同名的已更新包发布到 Blob 存储，则需要重启应用，以便将更新的包加载到应用服务中。

## <a name="troubleshooting"></a>故障排除

- 直接从包运行会使 `wwwroot` 变为只读目录。 如果应用尝试将文件写入此目录，将会收到错误。
- 不支持 TAR 和 GZIP 格式。
- 此功能与[本地缓存](overview-local-cache.md)不兼容。
- 若要提高冷启动性能，请使用本地 Zip 选项 (`WEBSITE_RUN_FROM_PACKAGE`=1)。

## <a name="more-resources"></a>更多资源

- [Azure App Service 的持续部署](deploy-continuous-deployment.md)
- [使用 ZIP 或 WAR 文件部署代码](deploy-zip.md)
