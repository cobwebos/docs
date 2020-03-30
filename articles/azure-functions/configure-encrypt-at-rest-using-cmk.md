---
title: 静态加密应用程序源
description: 在 Azure 存储中加密应用程序数据并将其部署为包文件。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 62179e900ace0d6d7b8b1f07e8f0ab685508f991
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408718"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥进行静态加密

在静态时加密函数应用的应用程序数据需要 Azure 存储帐户和 Azure 密钥保管库。 当您从部署包运行应用时，将使用这些服务。

  - [Azure 存储提供静态加密](../storage/common/storage-service-encryption.md)。 您可以使用系统提供的密钥或您自己的客户管理的密钥。 这是应用程序数据在 Azure 中的函数应用中未运行时存储的位置。
  - [从部署包运行]（run-functions-from-deployment-package.md）是应用服务的一个部署功能。 它允许您使用共享访问签名 （SAS） URL 从 Azure 存储帐户部署网站内容。
  - [密钥保管库引用](../app-service/app-service-key-vault-references.md)是应用服务的安全功能。 它允许您在运行时作为应用程序设置导入机密。 使用此选项可加密 Azure 存储帐户的 SAS URL。

## <a name="set-up-encryption-at-rest"></a>设置静态加密

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

首先，[创建一个 Azure 存储帐户](../storage/common/storage-account-create.md)[，并使用客户托管密钥对其进行加密](../storage/common/encryption-customer-managed-keys.md)。 创建存储帐户后，使用 Azure[存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)上载包文件。

接下来，使用存储资源管理器生成[SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> 保存此 SAS URL，稍后用于在运行时启用部署包的安全访问。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>配置从存储帐户的包运行
  
将文件上载到 Blob 存储并具有文件的 SAS URL 后，请将`WEBSITE_RUN_FROM_PACKAGE`应用程序设置设置为 SAS URL。 以下示例使用 Azure CLI 执行此操作：

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

添加此应用程序设置会导致函数应用重新启动。 重新启动应用后，浏览到它并确保应用已使用部署包正确启动。 如果应用程序未正确启动，请参阅["从包运行疑难解答指南](run-functions-from-deployment-package.md#troubleshooting)"。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>使用密钥保管库引用加密应用程序设置

现在，您可以将`WEBSITE_RUN_FROM_PACKAGE`应用程序设置的值替换为对 SAS 编码 URL 的密钥保管库引用。 这样可以保持密钥保管库中的 SAS URL 加密，从而提供额外的安全层。

1. 使用以下[`az keyvault create`](/cli/azure/keyvault#az-keyvault-create)命令创建密钥保管库实例。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 按照[以下说明授予应用](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault)对密钥保管库的访问权限：

1. 使用以下[`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set)命令将外部 URL 添加为密钥保管库中的机密：   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  使用以下[`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)命令创建具有该值`WEBSITE_RUN_FROM_PACKAGE`的应用程序设置作为对外部 URL 的密钥保管库引用：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`将在上一个`az keyvault secret set`命令的输出中。

更新此应用程序设置会导致函数应用重新启动。 重新启动应用后，浏览到它请确保使用密钥保管库引用正确启动。

## <a name="how-to-rotate-the-access-token"></a>如何旋转访问令牌

最佳做法是定期轮换存储帐户的 SAS 密钥。 为了确保函数应用不会无意中失去访问，还必须更新密钥保管库中的 SAS URL。

1. 通过在 Azure 门户中导航到存储帐户来旋转 SAS 密钥。 在 **"设置** > **访问键**"下，单击该图标以旋转 SAS 键。

1. 复制新的 SAS URL，并使用以下命令在密钥保管库中设置更新的 SAS URL：

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 将应用程序设置中的密钥保管库引用更新为新的秘密版本：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>`将在上一个`az keyvault secret set`命令的输出中。

## <a name="how-to-revoke-the-function-apps-data-access"></a>如何撤销函数应用的数据访问

有两种方法可以撤消函数应用对存储帐户的访问权限。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>旋转 Azure 存储帐户的 SAS 密钥

如果旋转存储帐户的 SAS 密钥，则函数应用将不再有权访问存储帐户，但它将继续使用包文件的最后一个下载版本运行。 重新启动函数应用以清除上次下载的版本。

### <a name="remove-the-function-apps-access-to-key-vault"></a>删除功能应用对密钥保管库的访问权限

您可以通过禁用函数应用对密钥保管库的访问来撤消函数应用对站点数据的访问。 为此，请删除函数应用标识的访问策略。 这与之前在配置密钥保管库引用时创建的相同标识相同。

## <a name="summary"></a>总结

您的应用程序文件现在在存储帐户中静态加密。 当函数应用启动时，它会从密钥保管库检索 SAS URL。 最后，函数应用从存储帐户加载应用程序文件。 

如果需要撤消函数应用对存储帐户的访问权限，可以撤消对密钥保管库的访问权限或轮换存储帐户密钥，这会使 SAS URL 失效。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>从部署包运行我的函数应用需要额外付费吗？

仅与 Azure 存储帐户关联的成本和任何适用的出口费用。

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>从部署包运行如何影响我的函数应用？

- 从部署包运行应用可使`wwwroot/`只读。 应用尝试写入此目录时收到错误。
- 不支持 TAR 和 GZIP 格式。
- 此功能与本地缓存不兼容。

## <a name="next-steps"></a>后续步骤

- [应用服务的关键保管库引用](../app-service/app-service-key-vault-references.md)
- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
