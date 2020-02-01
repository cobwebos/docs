---
title: 在容器组中启用托管标识
description: 了解如何在 Azure 容器实例中启用可以使用其他 Azure 服务进行身份验证的托管标识
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901929"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>如何将托管标识用于 Azure 容器实例

使用[azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)在与其他 azure 服务交互的 Azure 容器实例中运行代码，而无需在代码中维护任何机密或凭据。 此功能通过 Azure Active Directory 中的自动托管标识提供 Azure 容器实例部署。

本文详细了解 Azure 容器实例中的托管标识，以及：

> [!div class="checklist"]
> * 在容器组中启用用户分配的或系统分配的标识
> * 向标识授予对 Azure 密钥保管库的访问权限
> * 使用托管标识从正在运行的容器中访问密钥保管库

调整示例以启用和使用 Azure 容器实例中的标识来访问其他 Azure 服务。 这些示例是交互式的。 但是，在实践中，容器映像将运行代码来访问 Azure 服务。

> [!NOTE]
> 目前不能在部署到虚拟网络的容器组中使用托管标识。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

使用正在运行的容器中的托管标识向任何[支持 Azure AD 身份验证的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)进行身份验证，而无需在容器代码中管理凭据。 对于不支持 AD 身份验证的服务，可以在 Azure 密钥保管库中存储机密，并使用托管标识访问密钥保管库来检索凭据。 有关使用托管标识的详细信息，请参阅[Azure 资源的托管标识是什么？](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> 此功能目前处于预览阶段。 如果你同意[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，则会向你提供预览。 在公开上市（GA）之前，此功能的某些方面可能会发生更改。 目前，Azure 容器实例上的托管标识仅支持 Linux 容器，但不支持 Windows 容器。
>  

### <a name="enable-a-managed-identity"></a>启用托管标识

 在 Azure 容器实例中，支持 Azure 资源的托管标识，REST API 版本2018-10-01 及相应的 Sdk 和工具。 创建容器组时，可以通过设置[ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity)属性来启用一个或多个托管标识。 你还可以在容器组运行后启用或更新托管标识-这两个操作都会使容器组重新启动。 若要在新的或现有的容器组上设置标识，请使用 Azure CLI、资源管理器模板或 YAML 文件。 

Azure 容器实例支持这两种类型的托管 Azure 身份：用户分配和系统分配。 在容器组上，你可以启用系统分配的标识、一个或多个用户分配的标识，也可以启用这两种类型的标识。 

* **用户分配**的托管标识作为 Azure AD 租户中的独立 Azure 资源创建，该订阅受使用的订阅信任。 创建标识后，可以将该标识分配给一个或多个 Azure 资源（在 Azure 容器实例或其他 Azure 服务中）。 用户分配的标识的生命周期与容器组或其分配到的其他服务资源的生命周期分开管理。 此行为在 Azure 容器实例中特别有用。 由于该标识超出了容器组的生存期，因此你可以将其与其他标准设置一起使用，以使容器组部署高度可重复。

* **系统分配**的托管标识直接在 Azure 容器实例中的容器组上启用。 启用后，Azure 将为 Azure AD 租户中的组创建一个标识，该标识由实例的订阅所信任。 创建标识后，会在容器组中的每个容器中设置凭据。 系统分配的标识的生命周期直接绑定到启用它的容器组。 删除组时，Azure 会自动清理 Azure AD 中的凭据和标识。

### <a name="use-a-managed-identity"></a>使用托管标识

若要使用托管标识，必须在订阅中最初向标识授予一个或多个 Azure 服务资源（例如 web 应用、密钥保管库或存储帐户）的访问权限。 若要从正在运行的容器访问 Azure 资源，你的代码必须从 Azure AD 终结点获取*访问令牌*。 然后，你的代码将在对支持 Azure AD authentication 的服务的调用上发送访问令牌。 

在运行的容器中使用托管标识实质上与使用 Azure VM 中的标识相同。 有关使用[令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)、 [Azure PowerShell 或 Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)或[Azure sdk](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)的 VM 指南，请参阅。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 版本2.0.49 或更高版本。 运行 `az --version` 以查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-an-azure-key-vault"></a>创建 Azure 密钥保管库

本文中的示例使用 Azure 容器实例中的托管标识访问 Azure 密钥保管库机密。 

首先，在*eastus*位置创建一个名为*myResourceGroup*的资源组，并在其中包含以下[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)命令：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

使用[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)命令创建密钥保管库。 请确保指定唯一的密钥保管库名称。 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

使用[az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)命令将示例机密存储在密钥保管库中：

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

继续执行以下示例，使用 Azure 容器实例中用户分配的托管标识或系统分配的托管标识访问密钥保管库。

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>示例1：使用用户分配的标识访问 Azure 密钥保管库

### <a name="create-an-identity"></a>创建标识

首先，使用[az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create)命令在订阅中创建一个标识。 可以使用用于创建密钥保管库的同一个资源组，或使用其他资源组。

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

若要在以下步骤中使用该标识，请使用[az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show)命令将标识的服务主体 id 和资源 id 存储在变量中。

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>在容器组上启用用户分配的标识

运行以下[az container create](/cli/azure/container?view=azure-cli-latest#az-container-create)命令，创建基于 Microsoft `azure-cli` 映像的容器实例。 此示例提供了一个可交互使用的单容器组，用于运行 Azure CLI 来访问其他 Azure 服务。 在本部分中，只使用基本 Ubuntu 操作系统。 

`--assign-identity` 参数将用户分配的托管标识传递给组。 长时间运行的命令使容器保持运行。 此示例使用用于创建密钥保管库的同一资源组，但可以指定其他资源组。

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

在几秒钟内，你应该会收到来自 Azure CLI 的响应，指出部署已完成。 用[az container show](/cli/azure/container?view=azure-cli-latest#az-container-show)命令检查其状态。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

输出中的 "`identity`" 部分如下所示，其中显示了 "容器" 组中设置的标识。 `userAssignedIdentities` 下的 `principalID` 是在 Azure Active Directory 中创建的标识的服务主体：

```console
[...]
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
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>向用户分配的标识授予对密钥保管库的访问权限

运行以下[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest)命令来设置密钥保管库上的访问策略。 以下示例允许用户分配的标识从密钥保管库获取机密：

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>使用用户分配的标识从密钥保管库获取密钥

现在可以使用正在运行的容器实例中的托管标识访问密钥保管库。 首先在容器中启动 bash shell：

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

在容器中的 bash shell 中运行以下命令。 若要获取访问令牌以使用 Azure Active Directory 向密钥保管库进行身份验证，请运行以下命令：

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

输出：

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

若要在用于验证的后续命令中存储访问令牌，请运行以下命令：

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

现在使用访问令牌向密钥保管库进行身份验证并读取机密。 请务必在 URL 中替换密钥保管库的名称（ *https://mykeyvault.vault.azure.net/.. 。* ）：

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

响应类似于以下内容，其中显示了机密。 在代码中，你将分析此输出以获取机密。 然后，在后续操作中使用机密来访问其他 Azure 资源。

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>示例2：使用系统分配的标识访问 Azure 密钥保管库

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>在容器组中启用系统分配的标识

运行以下[az container create](/cli/azure/container?view=azure-cli-latest#az-container-create)命令，创建基于 Microsoft `azure-cli` 映像的容器实例。 此示例提供了一个可交互使用的单容器组，用于运行 Azure CLI 来访问其他 Azure 服务。 

没有其他值的 `--assign-identity` 参数将在组上启用系统分配的托管标识。 标识的范围限定为容器组的资源组。 长时间运行的命令使容器保持运行。 此示例使用用于创建密钥保管库的同一资源组，但可以指定其他资源组。

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

在几秒钟内，你应该会收到来自 Azure CLI 的响应，指出部署已完成。 用[az container show](/cli/azure/container?view=azure-cli-latest#az-container-show)命令检查其状态。

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

输出中的 "`identity`" 部分如下所示，其中显示系统分配的标识是在 Azure Active Directory 中创建的：

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

将变量设置为标识的 `principalId` （服务主体 ID）的值，以便在后续步骤中使用。

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>授予容器组对密钥保管库的访问权限

运行以下[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest)命令来设置密钥保管库上的访问策略。 以下示例允许系统管理的标识从密钥保管库中获取机密：

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>使用容器组标识从密钥保管库获取密钥

现在，你可以使用托管标识访问正在运行的容器实例中的密钥保管库。 首先在容器中启动 bash shell：

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

在容器中的 bash shell 中运行以下命令。 首先使用托管标识登录到 Azure CLI：

```bash
az login --identity
```

从正在运行的容器中检索密钥保管库中的机密：

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

将检索机密值：

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>使用资源管理器模板启用托管标识

若要使用[资源管理器模板](container-instances-multi-container-group.md)在容器组中启用托管标识，请使用 `ContainerGroupIdentity` 对象设置 `Microsoft.ContainerInstance/containerGroups` 对象的 `identity` 属性。 以下代码片段显示了为不同方案配置的 `identity` 属性。 请参阅[资源管理器的模板参考](/azure/templates/microsoft.containerinstance/containergroups)。 指定 `2018-10-01`的最小 `apiVersion`。

### <a name="user-assigned-identity"></a>用户分配的标识

用户分配的标识是以下形式的资源 ID：

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

若要在使用[YAML 文件](container-instances-multi-container-yaml.md)部署的容器组中启用托管标识，请包含以下 YAML。
指定 `2018-10-01`的最小 `apiVersion`。

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

本文介绍了 Azure 容器实例中的托管标识，以及如何执行以下操作：

> [!div class="checklist"]
> * 在容器组中启用用户分配的或系统分配的标识
> * 向标识授予对 Azure 密钥保管库的访问权限
> * 使用托管标识从正在运行的容器中访问密钥保管库

* 详细了解[Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/)。

* 请参阅[Azure 中转 SDK 示例](https://medium.com/@samkreter/c98911206328)，了解如何使用托管标识从 Azure 容器实例中访问密钥保管库。
