---
title: 加密静态应用程序源
description: 加密 Azure 存储中的应用程序数据，并将其部署为包文件。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: b1cfd99e2cc7c87675c72f679139a4299486cca6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79374896"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客户托管密钥进行静态加密

静态加密 web 应用的应用程序数据需要 Azure 存储帐户和 Azure Key Vault。 当你从部署包运行你的应用时，将使用这些服务。

  - [Azure 存储提供静态加密](../storage/common/storage-service-encryption.md)。 您可以使用系统提供的密钥，也可以使用客户管理的密钥。 当应用程序数据未在 Azure 中的 web 应用中运行时，会将其存储在其中。
  - [从部署包运行](deploy-run-package.md)是应用服务的一项部署功能。 它允许你使用共享访问签名（SAS） URL 从 Azure 存储帐户部署网站内容。
  - [Key Vault 引用](app-service-key-vault-references.md)是应用服务的一项安全功能。 它允许你在运行时导入机密作为应用程序设置。 使用此加密可以加密 Azure 存储帐户的 SAS URL。

## <a name="set-up-encryption-at-rest"></a>设置静态加密

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

首先，[创建一个 Azure 存储帐户](../storage/common/storage-account-create.md)，并[使用客户托管密钥对其进行加密](../storage/common/storage-service-encryption.md#customer-managed-keys-with-azure-key-vault)。 创建存储帐户后，请使用[Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)上传包文件。

接下来，使用存储资源管理器[生成 SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> 保存此 SAS URL，以后将使用此 URL 在运行时启用部署包的安全访问。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>配置从存储帐户中的包运行
  
将文件上传到 Blob 存储并为该文件设置 SAS URL 后，请将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置设置为 SAS URL。 下面的示例使用 Azure CLI 执行此操作：

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

添加此应用程序设置会导致 web 应用重新启动。 在应用程序重新启动后，浏览到该应用程序，并确保该应用程序已使用部署包正确启动。 如果应用程序未正确启动，请参阅[从包运行疑难解答指南](deploy-run-package.md#troubleshooting)。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>使用 Key Vault 引用对应用程序设置进行加密

现在可以将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置的值替换为对 SAS 编码 URL 的 Key Vault 引用。 这会使 SAS URL 在 Key Vault 中进行加密，从而提供额外的安全层。

1. 使用以下[`az keyvault create`](/cli/azure/keyvault#az-keyvault-create)命令创建 Key Vault 实例。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 按照[以下说明授予你的应用程序访问](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)密钥保管库的权限：

1. 使用以下[`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set)命令将外部 URL 添加为密钥保管库中的机密：   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  使用以下[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)命令创建 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置，将值作为对外部 URL 的 Key Vault 引用：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>` 将位于之前 `az keyvault secret set` 命令的输出中。

更新此应用程序设置会导致 web 应用重新启动。 重新启动应用程序后，请浏览到它，确保它已使用 Key Vault 引用正确启动。

## <a name="how-to-rotate-the-access-token"></a>如何旋转访问令牌

最佳做法是定期轮换存储帐户的 SAS 密钥。 若要确保 web 应用不会无意间丢失访问权限，你还必须更新 Key Vault 中的 SAS URL。

1. 通过导航到 Azure 门户中的存储帐户来旋转 SAS 密钥。 在 "**设置**" > **访问密钥**"下，单击图标来旋转 SAS 密钥。

1. 复制新的 SAS URL，并使用以下命令在 key vault 中设置更新的 SAS URL：

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 将应用程序设置中的密钥保管库引用更新为新的密钥版本：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>` 将位于之前 `az keyvault secret set` 命令的输出中。

## <a name="how-to-revoke-the-web-apps-data-access"></a>如何撤消 web 应用的数据访问

可通过两种方法撤消 web 应用对存储帐户的访问权限。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>轮换 Azure 存储帐户的 SAS 密钥

如果存储帐户的 SAS 密钥是轮换密钥，则 web 应用将无法再访问存储帐户，但它将继续使用上次下载的包文件版本运行。 重新启动 web 应用以清除上次下载的版本。

### <a name="remove-the-web-apps-access-to-key-vault"></a>删除 web 应用对 Key Vault 的访问权限

可以通过禁用 web 应用对 Key Vault 的访问权限来撤消 web 应用对站点数据的访问。 为此，请删除 web 应用的标识的访问策略。 这是你之前在配置密钥保管库引用时创建的相同标识。

## <a name="summary"></a>摘要

现在，你的应用程序文件在存储帐户中进行了静态加密。 当 web 应用启动时，它将从密钥保管库中检索 SAS URL。 最后，web 应用从存储帐户加载应用程序文件。 

如果需要撤消 web 应用对存储帐户的访问权限，可以撤消对密钥保管库的访问权限，或轮换存储帐户密钥，使 SAS URL 失效。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>从部署包运行 web 应用是否需要支付额外费用？

仅与 Azure 存储帐户相关联的成本和所有适用的出口费用。

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>从部署包运行如何影响我的 web 应用？

- 从部署包运行应用会使 `wwwroot/` 成为只读的。 应用尝试写入此目录时收到错误。
- TAR 和 GZIP 格式不受支持。
- 此功能与本地缓存不兼容。

## <a name="next-steps"></a>后续步骤

- [应用服务的 Key Vault 参考](app-service-key-vault-references.md)
- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
