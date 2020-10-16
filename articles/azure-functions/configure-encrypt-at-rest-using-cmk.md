---
title: 静态加密应用程序源
description: 加密 Azure 存储中的应用程序数据，并将其部署为包文件。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 6e4cf16118e748e3ee1d1ff4899730463565ec70
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108025"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>使用客户管理的密钥加密静态应用程序数据

静态加密函数应用的应用程序数据需要一个 Azure 存储帐户和一个 Azure Key Vault。 从部署包运行应用时，将使用这些服务。

  - [Azure 存储提供静态加密](../storage/common/storage-service-encryption.md)。 你可以使用系统提供的密钥或自己的密钥（客户管理的密钥）。 当 Azure 存储不是在 Azure 上的函数应用中运行时，将在其中存储应用程序数据。
  - [从部署包运行](run-functions-from-deployment-package.md)是应用服务的部署功能。 借助此功能可以使用共享访问签名 (SAS) URL 从 Azure 存储帐户部署站点内容。
  - [Key Vault 引用](../app-service/app-service-key-vault-references.md)是应用服务的一项安全功能。 借助此功能可以在运行时将机密作为应用程序设置导入。 使用此机密可以加密 Azure 存储帐户的 SAS URL。

## <a name="set-up-encryption-at-rest"></a>设置静态加密

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

首先，[创建一个 Azure 存储帐户](../storage/common/storage-account-create.md)并[使用客户管理的密钥将其加密](../storage/common/customer-managed-keys-overview.md)。 创建存储帐户后，使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)上传包文件。

接下来，使用存储资源管理器[生成 SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> 请保存此 SAS URL，因为以后在运行时启用部署包的安全访问时需要用到它。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>配置从存储帐户中的包运行
  
将文件上传到 Blob 存储并获取该文件的 SAS URL 后，请将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置指定为该 SAS URL。 以下示例使用 Azure CLI 执行此操作：

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

添加此应用程序设置会导致函数应用重启。 重启应用后，浏览到该应用，并确保它已使用部署包正常启动。 如果应用程序未正常启动，请参阅[“从包运行”故障排除指南](run-functions-from-deployment-package.md#troubleshooting)。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>使用 Key Vault 引用加密应用程序设置

现在，可将 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置的值替换为对 SAS 编码 URL 的 Key Vault 引用。 这会使 SAS URL 在 Key Vault 中加密，从而提供附加的安全层。

1. 使用以下 [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) 命令创建 Key Vault 实例。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. 按照[这些说明](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault)向应用授予对 Key Vault 的访问权限：

1. 使用以下 [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) 命令将外部 URL 添加为 Key Vault 中的机密：   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  使用以下 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令创建 `WEBSITE_RUN_FROM_PACKAGE` 应用程序设置，该设置的值为对外部 URL 的 Key Vault 引用：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    上述 `az keyvault secret set` 命令的输出中会显示 `<secret-version>`。

更新此应用程序设置会导致函数应用重启。 重启应用后，请浏览到该应用，并确保它已使用 Key Vault 引用正常启动。

## <a name="how-to-rotate-the-access-token"></a>如何轮换访问令牌

最佳做法是定期轮换存储帐户的 SAS 密钥。 为了确保函数应用不会无意中失去访问权限，还必须更新 Key Vault 中的 SAS URL。

1. 若要轮换 SAS 密钥，请在 Azure 门户中导航到你的存储帐户。 在“设置” > “访问密钥”下，单击相应的图标来轮换 SAS 密钥。

1. 复制新的 SAS URL，并使用以下命令在 Key Vault 中设置更新的 SAS URL：

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. 将应用程序设置中的 Key Vault 引用更新为新的机密版本：

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    上述 `az keyvault secret set` 命令的输出中会显示 `<secret-version>`。

## <a name="how-to-revoke-the-function-apps-data-access"></a>如何撤销函数应用的数据访问权限

可通过两种方法撤消函数应用对存储帐户的访问权限。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>轮换 Azure 存储帐户的 SAS 密钥

如果轮换了存储帐户的 SAS 密钥，则函数应用将不再有权访问该存储帐户，但它会使用上次下载的包文件版本继续运行。 重启函数应用可以清除上次下载的版本。

### <a name="remove-the-function-apps-access-to-key-vault"></a>删除函数应用对 Key Vault 的访问权限

通过禁用函数应用对 Key Vault 的访问权限，来撤销函数应用对站点数据的访问权限。 为此，请删除针对函数应用的标识的访问策略。 此标识是之前在配置 Key Vault 引用时创建的同一标识。

## <a name="summary"></a>摘要

现在，应用程序文件已在存储帐户中静态加密。 当函数应用启动时，它会从 Key Vault 检索 SAS URL。 最后，函数应用会从存储帐户加载应用程序文件。 

如果需要撤销函数应用对存储帐户的访问权限，可以撤销对 Key Vault 的访问权限，或轮换存储帐户密钥，使 SAS URL 失效。

## <a name="frequently-asked-questions"></a>常见问题解答

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>从部署包运行函数应用是否会产生任何额外的费用？

只会产生 Azure 存储帐户相关的费用，以及任何适用的传出费用。

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>从部署包运行会给函数应用产生怎样的影响？

- 从部署包运行应用会使 `wwwroot/` 变为只读。 应用在尝试写入此目录时会收到错误。
- 不支持 TAR 和 GZIP 格式。
- 此功能与本地缓存不兼容。

## <a name="next-steps"></a>后续步骤

- [应用服务的 Key Vault 引用](../app-service/app-service-key-vault-references.md)
- [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)