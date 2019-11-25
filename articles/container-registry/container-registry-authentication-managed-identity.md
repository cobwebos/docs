---
title: 使用托管标识进行身份验证
description: 通过使用用户分配或系统分配的托管 Azure 标识，提供对专用容器注册表中映像的访问。
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456491"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>使用 Azure 托管标识向 Azure 容器注册表验证身份 

使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)从另一个 Azure 资源向 Azure 容器注册表验证身份，而无需提供或管理注册表凭据。 例如，在 Linux VM 上设置用户分配或系统分配的托管标识，以便从容器注册表访问容器映像，就像使用公共注册表一样容易。

本文将详细介绍托管标识以及如何：

> [!div class="checklist"]
> * 在 Azure VM 上启用用户分配或系统分配的标识
> * 授予标识对 Azure 容器注册表的访问权限
> * 使用托管标识访问注册表并拉取容器映像 

为了创建 Azure 资源，本文要求运行 Azure CLI 版本 2.0.55 或更高版本。 可以运行 `az --version` 来查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

若要设置容器注册表并向其推送容器映像，还必须在本地安装 Docker。 Docker 提供的包可在任何 [macOS][docker-mac]、[Windows][docker-windows] 或 [Linux][docker-linux] 系统上轻松配置 Docker。

## <a name="why-use-a-managed-identity"></a>为什么使用托管标识？

Azure 资源的托管标识可在 Azure Active Directory (Azure AD) 中为 Azure 服务提供一个自动托管标识。 你可以为[某些 Azure 资源](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)（包括虚拟机）配置托管标识。 然后使用该标识访问其他 Azure 资源，而无需在代码或脚本中传递凭据。

托管标识有两种类型：

* *用户分配的标识*，可以将其分配给多个资源，并根据需要持久保存。 用户分配的标识现提供预览版。

* *系统托管标识*，对于特定资源（如单个虚拟机）是唯一的，并且在该资源的生存期内持久保存。

为 Azure 资源设置托管标识后，便可以根据需要授予该标识对另一资源的访问权限，这一点与所有安全主体一样。 例如，为托管标识分配角色，该角色对 Azure 中的专用注册表具有拉取、推送和拉取或其他权限。 (For a complete list of registry roles, see [Azure Container Registry roles and permissions](container-registry-roles.md).) You can give an identity access to one or more resources.

然后使用该标识向[支持 Azure AD 身份验证的任何服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)进行身份验证，而无需在代码中放入任何凭据。 若要使用该标识从虚拟机访问 Azure 容器注册表，请向 Azure 资源管理器验证身份。 选择如何使用托管标识进行身份验证，具体取决于你的方案：

* 使用 HTTP 或 REST 调用以编程方式[获取 Azure AD 访问令牌](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

* 使用 [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* 使用标识[登录 Azure CLI 或 PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)。 

## <a name="create-a-container-registry"></a>创建容器注册表

如果还没有 Azure 容器注册表，请创建一个注册表并向其推送示例容器映像。 For steps, see [Quickstart: Create a private container registry using the Azure CLI](container-registry-get-started-azure-cli.md).

本文假设你在注册表中存储了 `aci-helloworld:v1` 容器映像。 以下示例使用注册表名称 *myContainerRegistry*。 请在后续步骤中替换为你自己的注册表和映像名称。

## <a name="create-a-docker-enabled-vm"></a>创建一个启用了 Docker 的 VM

创建一个启用了 Docker 的 Ubuntu 虚拟机。 还需要在该虚拟机上安装 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 如果已有 Azure 虚拟机，请跳过此虚拟机创建步骤。

Deploy a default Ubuntu Azure virtual machine with [az vm create][az-vm-create]. 以下示例在名为 *myResourceGroup* 的现有资源组中创建名为 *myDockerVM* 的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址与 VM 建立 SSH 连接。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令以在 VM 上安装 Docker：

```bash
sudo apt install docker.io -y
```

安装完成后，运行以下命令验证 Docker 在 VM 上是否正常运行：

```bash
sudo docker run -it hello-world
```

输出：

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>安装 Azure CLI

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 在本文中，请确保安装版本 2.0.55 或更高版本。

退出 SSH 会话。

## <a name="example-1-access-with-a-user-assigned-identity"></a>Example 1: Access with a user-assigned identity

### <a name="create-an-identity"></a>创建标识

使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令在订阅中创建标识。 可以使用先前用于创建容器注册表或虚拟机的相同资源组，也可以使用不同的资源组。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

To configure the identity in the following steps, use the [az identity show][az-identity-show] command to store the identity's resource ID and service principal ID in variables.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Because you need the identity's ID in a later step when you sign in to the CLI from your virtual machine, show the value:

```bash
echo $userID
```

ID 的格式如下：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>为 VM 配置标识

The following [az vm identity assign][az-vm-identity-assign] command configures your Docker VM with the user-assigned identity:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>授予标识对容器注册表的访问权限

现在将标识配置为访问容器注册表。 First use the [az acr show][az-acr-show] command to get the resource ID of the registry:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use the [az role assignment create][az-role-assignment-create] command to assign the AcrPull role to the registry. 此角色将提供对注册表的[拉取权限](container-registry-roles.md)。 若要同时提供拉取和推送权限，请分配 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用标识访问注册表

通过 SSH 连接到配置了标识的 Docker 虚拟机。 使用 VM 上安装的 Azure CLI 运行以下 Azure CLI 命令。

First, authenticate to the Azure CLI with [az login][az-login], using the identity you configured on the VM. 对于 `<userID>`，请替换成在上一步中检索到的标识 ID。 

```azurecli
az login --identity --username <userID>
```

Then, authenticate to the registry with [az acr login][az-acr-login]. 使用此命令时，CLI 使用运行 `az login` 时创建的 Active Directory 令牌，以无缝的方式向容器注册表验证会话的身份。 （根据 VM 的设置，可能需要使用 `sudo` 运行此命令和 docker 命令。）

```azurecli
az acr login --name myContainerRegistry
```

你应该会看到 `Login succeeded` 消息。 之后，便可以在不提供凭据的情况下运行 `docker` 命令。 For example, run [docker pull][docker-pull] to pull the `aci-helloworld:v1` image, specifying the login server name of your registry. 登录服务器名称由容器注册表名称（全部小写）后跟 `.azurecr.io` 组成 - 例如，`mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Example 2: Access with a system-assigned identity

### <a name="configure-the-vm-with-a-system-managed-identity"></a>为 VM 配置系统托管标识

The following [az vm identity assign][az-vm-identity-assign] command configures your Docker VM with a system-assigned identity:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Use the [az vm show][az-vm-show] command to set a variable to the value of `principalId` (the service principal ID) of the VM's identity, to use in later steps.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>授予标识对容器注册表的访问权限

现在将标识配置为访问容器注册表。 First use the [az acr show][az-acr-show] command to get the resource ID of the registry:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Use the [az role assignment create][az-role-assignment-create] command to assign the AcrPull role to the identity. 此角色将提供对注册表的[拉取权限](container-registry-roles.md)。 若要同时提供拉取和推送权限，请分配 ACRPush 角色。

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>使用标识访问注册表

通过 SSH 连接到配置了标识的 Docker 虚拟机。 使用 VM 上安装的 Azure CLI 运行以下 Azure CLI 命令。

First, authenticate the Azure CLI with [az login][az-login], using the system-assigned identity on the VM.

```azurecli
az login --identity
```

Then, authenticate to the registry with [az acr login][az-acr-login]. 使用此命令时，CLI 使用运行 `az login` 时创建的 Active Directory 令牌，以无缝的方式向容器注册表验证会话的身份。 （根据 VM 的设置，可能需要使用 `sudo` 运行此命令和 docker 命令。）

```azurecli
az acr login --name myContainerRegistry
```

你应该会看到 `Login succeeded` 消息。 之后，便可以在不提供凭据的情况下运行 `docker` 命令。 For example, run [docker pull][docker-pull] to pull the `aci-helloworld:v1` image, specifying the login server name of your registry. 登录服务器名称由容器注册表名称（全部小写）后跟 `.azurecr.io` 组成 - 例如，`mycontainerregistry.azurecr.io`。

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何将托管标识与 Azure 容器注册表配合使用，以及如何：

> [!div class="checklist"]
> * 在 Azure VM 中启用用户分配或系统分配的标识
> * 授予标识对 Azure 容器注册表的访问权限
> * 使用托管标识访问注册表并拉取容器映像

* 详细了解 [Azure 资源的托管标识](/azure/active-directory/managed-identities-azure-resources/)。


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
