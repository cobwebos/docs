---
title: 如何配置 Azure 数据资源管理器群集的托管标识
description: 了解如何配置 Azure 数据资源管理器群集的托管标识。
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f9592f5d2666684e0cf5eef687b1e69cfb55066c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065562"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>配置 Azure 数据资源管理器群集的托管标识

借助 [Azure Active Directory 中的托管标识](/azure/active-directory/managed-identities-azure-resources/overview)，群集可以轻松访问其他受 AAD 保护的资源（例如 Azure Key Vault）。 标识由 Azure 平台托管，无需预配或轮换任何机密。 本文介绍如何为 Azure 数据资源管理器群集创建托管标识。 托管标识配置当前仅支持[为群集启用客户托管密钥](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)。

> [!Note]
> 如果在订阅或租户之间迁移了应用，Azure 数据资源管理器的托管标识将不按预期方式运行。 应用需要获取新的标识，这可以通过[禁用](#remove-a-system-assigned-identity)和[重新启用](#add-a-system-assigned-identity)该功能来完成。 还需要更新下游资源的访问策略才能使用新标识。

## <a name="add-a-system-assigned-identity"></a>添加系统分配的标识
                                                                                                    
分配与群集关联的系统分配的标识，如果删除群集，将被删除。 一个群集只能有一个系统分配的标识。 使用系统分配的标识创建群集需要在该群集上设置一个额外的属性。 系统分配的标识使用 C#、ARM 模板或 Azure 门户添加，详情如下。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 门户添加系统分配的标识

1. 登录到 Azure[门户](https://portal.azure.com/)。

#### <a name="new-azure-data-explorer-cluster"></a>新的 Azure 数据资源管理器群集

1. [创建 Azure 数据资源管理器群集](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. 在 **"安全**"选项卡>**系统分配标识**中，选择 **"打开**"。 要删除系统分配的标识，请选择 **"关闭**"。
2. 选择 **"下一步："标记>** 或**查看 + 创建**以创建群集。

    ![将系统分配的标识添加到新群集](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>现有 Azure 数据资源管理器群集

1. 打开现有的 Azure 数据资源管理器群集。
1. 在门户的左侧窗格中选择 **"设置** > **标识**"。
1. 在 **"标识**窗格>**系统分配**"选项卡中：
   1. 将 **"状态**"滑块移到 **.**
   1. 选择 **"保存"**
   1. 在弹出窗口中，选择 **"是**"

    ![添加系统分配的标识](media/managed-identities/turn-system-assigned-identity-on.png)

1. 几分钟后，屏幕将显示： 
  * **对象 ID** - 用于客户托管密钥 
  * **角色分配**- 单击链接分配相关角色

    ![系统分配的身份](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>使用 C# 添加系统分配的标识

#### <a name="prerequisites"></a>先决条件

要使用 Azure 数据资源管理器 C# 客户端设置托管标识，请使用以下方式设置：

* 安装[Azure 数据资源管理器 （Kusto） NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)。
* 安装[Microsoft.身份模型.客户端.ActiveDirectory NuGet 包](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)进行身份验证。
* [创建可以访问](/azure/active-directory/develop/howto-create-service-principal-portal)资源的 Azure AD 应用程序和服务主体。 在订阅范围内添加角色分配，并获取所需的`Directory (tenant) ID`和`Application ID`。 `Client Secret`

#### <a name="create-or-update-your-cluster"></a>创建或更新群集

1. 使用 `Identity` 属性创建或更新群集：

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. 运行以下命令，检查是否已使用标识成功创建或更新群集：

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    如果结果中包含具有 `Succeeded` 值的 `ProvisioningState`，则表示已创建或更新群集，群集应具有以下属性：

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId` 和 `TenantId` 已替换为 GUID。 `TenantId` 属性标识该标识所属的 AAD 租户。 `PrincipalId` 是群集的新标识的唯一标识符。 在 AAD 中，服务主体的名称与你为应用服务或 Azure Functions 实例提供的名称相同。

# <a name="arm-template"></a>[ARM 模板](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板添加系统分配的标识

Azure 资源管理器模板可以用于自动化 Azure 资源部署。 若要详细了解如何部署到 Azure 数据资源管理器，请参阅[使用 Azure 资源管理器模板创建 Azure 数据资源管理器群集和数据库](create-cluster-database-resource-manager.md)。

添加系统分配的类型将告知 Azure 要为群集创建和管理标识。 在资源定义包括以下属性，可以创建 `Microsoft.Kusto/clusters` 类型的任何有标识资源： 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

例如：

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

创建群集后，它具有以下附加属性：

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` 和 `<PRINCIPALID>` 已替换为 GUID。 `TenantId` 属性标识该标识所属的 AAD 租户。 `PrincipalId` 是群集的新标识的唯一标识符。 在 AAD 中，服务主体的名称与你为应用服务或 Azure Functions 实例提供的名称相同。

---

## <a name="remove-a-system-assigned-identity"></a>删除系统分配的标识

删除系统分配的标识也会将它从 AAD 中删除。 删除群集资源时，也会自动从 AAD 中删除系统分配的标识。 可以通过禁用该功能来删除系统分配的标识。  使用 C#、ARM 模板或 Azure 门户删除系统分配的标识，详情如下。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>使用 Azure 门户删除系统分配的标识

1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 在门户的左侧窗格中选择 **"设置** > **标识**"。
1. 在 **"标识**窗格>**系统分配**"选项卡中：
    1. 将 **"状态"** 滑块移动到 **"关闭**"。
    1. 选择 **"保存"**
    1. 在弹出窗口中，选择 **"是**"以禁用系统分配的标识。 **"标识"** 窗格将恢复为与添加系统分配的标识之前相同的条件。

    ![系统分配的身份关闭](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>使用 C 删除系统分配的标识#

运行以下内容以删除系统分配的标识：

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM 模板](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>使用 Azure 资源管理器模板删除系统分配的标识

运行以下内容以删除系统分配的标识：

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>后续步骤

* [在 Azure 中保护 Azure 数据资源管理器群集](security.md)
* 通过启用静态加密[保护 Azure 数据资源管理器中的群集 - Azure 门户](manage-cluster-security.md)。
 * [使用 C# 配置客户管理的密钥](customer-managed-keys-csharp.md)
 * [使用 Azure 资源管理器模板配置客户管理的密钥](customer-managed-keys-resource-manager.md)
