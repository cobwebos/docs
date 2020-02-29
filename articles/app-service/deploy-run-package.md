---
title: 从 ZIP 包运行你的应用
description: 部署具有原子性的应用的 ZIP 包。 提高应用程序在 ZIP 部署过程中的可预测性和可靠性。
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 316ada7700a5cf45ee90f515336039702bab48c0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920716"
---
# <a name="run-your-app-in-azure-app-service-directly-from-a-zip-package"></a>直接从 ZIP 包 Azure App Service 运行应用

在[Azure App Service](overview.md)中，可以直接从部署 ZIP 包文件运行应用。 本文介绍如何在你的应用程序中启用此功能。

应用服务中的所有其他部署方法都有共同的功能：将文件部署到应用中的*D:\home\site\wwwroot* （或 */home/site/wwwroot 中*for Linux 应用）。 由于你的应用程序在运行时使用相同的目录，因此部署可能会因为文件锁定冲突而失败，并且由于某些文件尚未更新，导致应用程序的行为无法预测。

相反，当你直接从包运行时，包中的文件不会复制到*wwwroot*目录。 相反，ZIP 包本身会直接作为只读*wwwroot*目录进行装载。 直接从包运行有以下优点：

- 消除部署与运行时之间的文件锁定冲突。
- 确保每次仅运行完全部署的应用程序。
- 可部署到生产应用（需重启）。
- 提高 Azure 资源管理器部署的性能。
- 可以减少冷启动时间，特别是对于具有大型 npm 包树的 JavaScript 函数。

> [!NOTE]
> 目前仅支持 ZIP 包文件。

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="enable-running-from-package"></a>启用从包运行

`WEBSITE_RUN_FROM_PACKAGE` 应用设置允许从包运行。 若要设置它，请在 Azure CLI 中运行以下命令。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_RUN_FROM_PACKAGE="1"
```

`WEBSITE_RUN_FROM_PACKAGE="1"` 使你可以从应用的本地包运行应用。 你还可以[从远程包运行](#run-from-external-url-instead)。

## <a name="run-the-package"></a>运行包

在应用服务中运行包的最简单方法是使用 Azure CLI [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip)命令。 例如：

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src <filename>.zip
```

由于已设置 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置，因此此命令不会将包内容提取到应用程序的*D:\home\site\wwwroot*目录中。 相反，它会按原样将 ZIP 文件上传到*D:\home\data\SitePackages*，并在同一目录中创建*packagename* ，其中包含要在运行时加载的 ZIP 包的名称。 如果以其他方式（如[FTP](deploy-ftp.md)）上传 ZIP 包，则需要手动创建*D:\home\data\SitePackages*目录和*packagename*文件。

命令还将重新启动应用。 由于设置了 `WEBSITE_RUN_FROM_PACKAGE`，应用服务会将已上载的包作为只读*wwwroot*目录进行装载，并直接从该装载的目录运行应用。

## <a name="run-from-external-url-instead"></a>改为从外部 URL 运行

还可以从外部 URL （例如 Azure Blob 存储）运行包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。 应将专用存储容器与[共享访问签名（SAS）](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer)结合使用，以使应用服务运行时可以安全地访问该程序包。 

将文件上传到 Blob 存储并为该文件设置 SAS URL 后，请将 `WEBSITE_RUN_FROM_PACKAGE` 应用设置设置为 URL。 下面的示例使用 Azure CLI 执行此操作：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="https://myblobstorage.blob.core.windows.net/content/SampleCoreMVCApp.zip?st=2018-02-13T09%3A48%3A00Z&se=2044-06-14T09%3A48%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=bNrVrEFzRHQB17GFJ7boEanetyJ9DGwBSV8OM3Mdh%2FM%3D"
```

如果将具有相同名称的更新包发布到 Blob 存储，则需要重启应用，以便将更新的包加载到应用服务中。

### <a name="use-key-vault-references"></a>使用 Key Vault 引用

为了增加安全性，你可以将 Key Vault 引用与外部 URL 一起使用。 这会使 URL 处于静态加密状态，并允许利用 Key Vault 进行机密管理和旋转。 建议使用 Azure Blob 存储，以便轻松旋转关联的 SAS 密钥。 Azure Blob 存储是静态加密的，这使应用程序数据在未部署到应用服务时保持安全。

1. 创建 Azure 密钥保管库。

    ```azurecli
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus
    ```

1. 添加外部 URL 作为 Key Vault 中的机密。

    ```azurecli
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<insert-your-URL>"
    ```

1. 创建 `WEBSITE_RUN_FROM_PACKAGE` 应用设置，并将值设置为对外部 URL 的 Key Vault 引用。

    ```azurecli
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"
    ```

有关详细信息，请参阅以下文章。

- [应用服务的 Key Vault 参考](app-service-key-vault-references.md)
- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)

## <a name="troubleshooting"></a>故障排除

- 直接从包运行会使 `wwwroot` 成为只读的。 如果应用尝试将文件写入此目录，则会收到错误。
- TAR 和 GZIP 格式不受支持。
- 此功能与[本地缓存](overview-local-cache.md)不兼容。
- 为了改进冷启动性能，请使用本地 Zip 选项（`WEBSITE_RUN_FROM_PACKAGE`= 1）。

## <a name="more-resources"></a>更多资源

- [Azure App Service 的持续部署](deploy-continuous-deployment.md)
- [使用 ZIP 或 WAR 文件部署代码](deploy-zip.md)
