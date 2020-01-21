---
title: 使用配置客户管理的密钥C#
description: 本文介绍如何在 Azure 数据资源管理器中对数据配置客户管理的密钥加密。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 16c108790dd696e98a1264691254c9e99dac6cd3
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280590"
---
# <a name="configure-customer-managed-keys-using-c"></a>使用配置客户管理的密钥C#

> [!div class="op_single_selector"]
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 模板](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>通过客户托管的密钥配置加密

本部分说明如何使用 Azure 数据资源管理器C#客户端配置客户管理的密钥加密。 

### <a name="prerequisites"></a>必备组件

* 如果尚未安装 Visual Studio 2019，可以下载并使用**免费**的[Visual Studio 2019 社区版](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

### <a name="install-c-nuget"></a>安装C# NuGet

* 安装[Azure 数据资源管理器（Kusto） NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。

* 安装[System.identitymodel NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)进行身份验证。

### <a name="authentication"></a>身份验证

若要运行本文中的示例，请创建一个可访问资源的[Azure AD 应用程序](/azure/active-directory/develop/howto-create-service-principal-portal)和服务主体。 可以在订阅范围添加角色分配，并获取所需的 `Directory (tenant) ID`、`Application ID`和 `Client Secret`。

### <a name="configure-cluster"></a>配置群集

默认情况下，Azure 数据资源管理器加密使用 Microsoft 托管密钥。 将 Azure 数据资源管理器群集配置为使用客户管理的密钥，并指定要与群集关联的密钥。

1. 使用以下代码更新群集：

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. 运行以下命令以检查是否已成功更新群集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果结果包含具有 `Succeeded` 值 `ProvisioningState`，则已成功更新群集。

## <a name="update-the-key-version"></a>更新密钥版本

创建新的密钥版本时，需要更新群集以使用新版本。 首先，调用 `Get-AzKeyVaultKey` 获取密钥的最新版本。 然后，将群集的密钥保管库属性更新为使用新版本的密钥，如[配置群集](#configure-cluster)中所示。

## <a name="next-steps"></a>后续步骤

* [在 Azure 中保护 Azure 数据资源管理器群集](security.md)
* [配置 Azure 数据资源管理器群集的托管标识](managed-identities.md)
* 通过启用静态加密来[保护 Azure 中的群集数据资源管理器 Azure 门户](manage-cluster-security.md)。
* [使用 Azure 资源管理器模板配置客户托管密钥](customer-managed-keys-resource-manager.md)


