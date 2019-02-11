---
title: 将托管标识与 Azure 容器实例结合使用
description: 了解如何配合 Azure 容器实例中的其他 Azure 服务使用托管标识进行身份验证。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: bf807a4d24b72c948707ade28f06c4eb2a54c0a0
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657019"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>如何将托管标识与 Azure 容器实例结合使用

使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)在 Azure 容器实例中运行代码以便与其他 Azure 服务交互，而无需在代码中维护任何机密或凭据。 该功能提供了 Azure 容器实例部署，在 Azure Active Directory 中有一个自动托管标识。

本文将介绍有关 Azure 容器实例中托管标识的详细信息以及：

> [!div class="checklist"]
> * 在容器组中启用用户分配或系统分配的标识
> * 授予标识对 Azure Key Vault 的访问权限
> * 使用托管标识从正在运行的容器访问 Key Vault

调整示例，以启用并使用 Azure 容器实例中的标识来访问其他 Azure 服务。 这些示例是交互式的。 但实际上，容器映像将运行代码来访问 Azure 服务。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

在运行的容器中使用托管标识，可对[支持 Azure AD 身份验证的任何服务](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication)进行身份验证，而无需在容器代码中管理凭据。 对于不支持 AD 身份验证的服务，可以在 Azure Key Vault 中存储机密并使用托管标识来访问 Key Vault 以检索凭据。 有关使用托管标识的详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。 目前，托管标识仅在 Linux 容器实例中受支持。
>  

### <a name="enable-a-managed-identity"></a>启用托管标识

 在 Azure 容器实例中，Azure 资源的托管标识得到了 REST API 版本 2018-10-01 以及相应 SDK 和工具的支持。 创建容器组时，可通过设置 [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) 属性来启用一个或多个托管标识。 还可以在容器组运行后启用或更新托管标识；任何一个操作都会导致容器组重新启动。 若要在新容器组或现有容器组上设置标识，请使用 Azure CLI、资源管理器模板或 YAML 文件。 

Azure 容器实例支持以下两种类型的 Azure 托管标识：用户分配和系统分配。 在容器组中，可以启用系统分配的标识、一个或多个用户分配的标识或这两种类型的标识。 

* 用户分配的托管标识作为独立的 Azure 资源在使用中的订阅所信任的 Azure AD 租户中创建。 创建标识后，可以将标识分配到一个或多个 Azure 资源（在 Azure 容器实例或其他 Azure 服务中）。 用户分配标识的生命周期与它所分配到的容器组或其他服务资源的生命周期是分开管理的。 此行为在 Azure 容器实例中特别有用。 由于标识扩展到了容器组的生命周期之外，可以将其与其他标准设置一起重用，从而使容器组部署具有高度可重复性。

* 系统分配的托管标识直接在 Azure 容器实例的容器组上启用。 启用标识后，Azure 将在实例的订阅信任的 Azure AD 租户中创建组的标识。 创建标识后，系统会将凭据预配到容器组的每个容器中。 系统分配标识的生命周期直接绑定到启用它的容器组。 如果组遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。

### <a name="use-a-managed-identity"></a>使用托管标识

若要使用托管标识，必须在一开始便授予标识对订阅中一个或多个 Azure 服务资源的访问权限（例如 Web 应用、Key Vault 或存储帐户）。 若要从正在运行的容器访问 Azure 资源，代码必须从 Azure AD 终结点获得访问令牌。 然后，代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。 

在正在运行的容器中使用托管标识与在 Azure VM 中使用标识本质上是相同的。 请参阅有关使用[令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)、[Azure PowerShell 或 Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) 或 [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) 的 VM 指南。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.0.49 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-key-vault"></a>创建 Azure Key Vault

本文中示例使用 Azure 容器实例中的托管标识来访问 Azure Key Vault 机密。 

首先，使用以下 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 命令在 eastus 位置中创建一个名为“myResourceGroup”的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 命令来创建 Key Vault。 请务必指定唯一的 Key Vault 名称。 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

使用 [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 命令在 Key Vault 中存储实例机密：

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

继续下面的示例，使用 Azure 容器实例中用户分配或系统分配的托管标识访问 Key Vault。

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>示例 1：使用用户分配的标识访问 Azure Key Vault

### <a name="create-an-identity"></a>创建标识

首先使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令在订阅中创建标识。 可以使用用于创建 Key Vault 的相同资源组，也可以使用不同的资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

若要在以下步骤中使用标识，请使用 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 命令在变量中存储标识的服务主体 ID 和资源 ID。

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>在容器组中启用用户分配的标识

运行以下 [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) 命令基于 Ubuntu Server 创建容器实例。 此示例提供了单一容器组，可用于以交互方式访问其他 Azure 服务。 `--assign-identity` 参数将用户分配的托管标识传递到组。 长时间运行命令将使容器保持运行状态。 此示例使用用于创建 Key Vault 的相同资源组，但可以指定不同的资源组。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

在几秒钟内，你应当会从 Azure CLI 收到响应，它指出部署已完成。 使用 [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 命令检查它的状态。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

输出的 `identity` 部分类似于以下内容，显示在容器组中设置的标识。 `userAssignedIdentities` 下的 `principalID` 是在 Azure Active Directory 中创建的标识的服务主体：

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>授予用户分配的标识对 Key Vault 的访问权限

运行以下 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) 命令在 Key Vault 上设置访问策略。 以下示例允许用户分配的标识从 Key Vault 中获取机密：

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>使用用户分配的标识从 Key Vault 中获取机密

现在可以使用托管标识来访问正在运行的容器实例中的 Key Vault。 此示例中，首先在容器中启动 bash shell：

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

在容器的 bash shell 中运行以下命令。 若要获取访问令牌以使用 Azure Active Directory 对 Key Vault 进行身份验证，请运行以下命令：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

输出：

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

若要在变量中存储访问令牌以便在后续命令中进行身份验证，运行以下命令：

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

现在使用访问令牌对 Key Vault 进行身份验证并读取机密。 请务必替换 URL 中 Key Vault 的名称 (*https://mykeyvault.vault.azure.net/...*)：

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

响应类似于以下内容，其中显示机密。 在代码中，将分析此输出，以便获取机密。 然后，在后续操作中使用机密来访问另一个 Azure 资源。

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>示例 2：使用系统分配的标识访问 Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>在容器组中启用系统分配的标识

运行以下 [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) 命令基于 Ubuntu Server 创建容器实例。 此示例提供了单一容器组，可用于以交互方式访问其他 Azure 服务。 没有任何附加值的 `--assign-identity` 参数在组上启用系统分配的托管标识。 长时间运行命令将使容器保持运行状态。 此示例使用用于创建 Key Vault 的相同资源组，但可以指定不同的资源组。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

在几秒钟内，你应当会从 Azure CLI 收到响应，它指出部署已完成。 使用 [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) 命令检查它的状态。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

输出中的 `identity` 部分类似于以下内容，其中显示系统分配的标识在 Azure Active Directory 中创建：

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

将变量设置为标识的值 `principalId`（服务主体 ID），以便在后续步骤中使用。

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>授予容器组对 Key Vault 的访问权限

运行以下 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) 命令在 Key Vault 上设置访问策略。 以下示例允许系统托管标识从 Key Vault 中获取机密：

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>使用容器组标识从 Key Vault 中获取机密

现在可以使用托管标识来访问正在运行的容器实例中的 Key Vault。 此示例中，首先在容器中启动 bash shell：

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

在容器的 bash shell 中运行以下命令。 若要获取访问令牌以使用 Azure Active Directory 对 Key Vault 进行身份验证，请运行以下命令：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

输出：

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

若要在变量中存储访问令牌以便在后续命令中进行身份验证，运行以下命令：

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

现在使用访问令牌对 Key Vault 进行身份验证并读取机密。 请务必替换 URL 中 Key Vault 的名称 (*https://mykeyvault.vault.azure.net/...*)：

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

响应类似于以下内容，其中显示机密。 在代码中，将分析此输出，以便获取机密。 然后，在后续操作中使用机密来访问另一个 Azure 资源。

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>使用资源管理器模板启用托管标识

若要使用[资源管理器模板](container-instances-multi-container-group.md)在容器组中启用托管标识，请使用 `ContainerGroupIdentity` 对象设置 `Microsoft.ContainerInstance/containerGroups` 对象的 `identity` 属性。 下面的代码段演示针对不同方案配置的 `identity` 属性。 请参阅[资源管理器模板引用](/azure/templates/microsoft.containerinstance/containergroups)。 指定 `2018-10-01` 的 `apiVersion`。

### <a name="user-assigned-identity"></a>用户分配的标识

用户分配的标识是窗体的资源 ID：

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

可以启用一个或多个用户分配的标识。

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>系统分配的标识

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>系统和用户分配的标识

在容器组中，可以同时启用系统分配的标识和一个或多个用户分配的标识。

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>使用 YAML 文件启用托管标识

若要在使用 [YAML 文件](container-instances-multi-container-yaml.md)部署的容器组中启用托管标识，请包含以下 YAML。
指定 `2018-10-01` 的 `apiVersion`。

### <a name="user-assigned-identity"></a>用户分配的标识

用户分配的标识是窗体的资源 ID 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

可以启用一个或多个用户分配的标识。

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>系统分配的标识

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>系统和用户分配的标识

在容器组中，可以同时启用系统分配的标识和一个或多个用户分配的标识。

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>后续步骤

本文将介绍有关 Azure 容器实例中托管标识的信息以及如何：

> [!div class="checklist"]
> * 在容器组中启用用户分配或系统分配的标识
> * 授予标识对 Azure Key Vault 的访问权限
> * 使用托管标识从正在运行的容器访问 Key Vault

* 详细了解 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/)。

* 有关使用托管标识从 Azure 容器实例访问 Key Vault 的信息，请参阅 [Azure Go SDK 示例](https://medium.com/@samkreter/c98911206328)。
