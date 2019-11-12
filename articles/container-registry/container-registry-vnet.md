---
title: 使用虚拟网络限制对 Azure 容器注册表的访问
description: 仅允许从 Azure 虚拟网络中的资源或从公共 IP 地址范围访问 Azure 容器注册表。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 5ba5c180def9539c486fb8727a0a78b4f98fa185
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931321"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问权限

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 azure 和本地资源提供安全的专用网络。 通过限制对 Azure 虚拟网络中的专用 Azure 容器注册表的访问权限，你可以确保仅虚拟网络中的资源访问注册表。 对于跨界方案，你还可以将防火墙规则配置为仅允许来自特定 IP 地址的注册表访问。

本文介绍两种方案，用于在容器注册表上配置入站网络访问规则：从部署在虚拟网络中的虚拟机或从 VM 的公共 IP 地址。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版推出之前，此功能的某些方面可能会有所更改。
>

如果需要为资源设置访问规则以从防火墙后面访问容器注册表，请参阅[配置规则以访问防火墙后面的 Azure 容器注册表](container-registry-firewall-access-rules.md)。


## <a name="preview-limitations"></a>预览版限制

* 只能为**高级**容器注册表配置网络访问规则。 有关注册表服务层的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。 

* 只有[Azure Kubernetes Service](../aks/intro-kubernetes.md)群集或 azure[虚拟机](../virtual-machines/linux/overview.md)可以用作主机来访问虚拟网络中的容器注册表。 *当前不支持包括 Azure 容器实例的其他 Azure 服务。*

* 在虚拟网络中访问的容器注册表中当前不支持[ACR 任务](container-registry-tasks-overview.md)操作。

* 每个注册表最多支持100个虚拟网络规则。

## <a name="prerequisites"></a>先决条件

* 若要使用本文中 Azure CLI 的步骤，需要 Azure CLI 版本2.0.58 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

* 如果还没有容器注册表，请创建一个（需要高级 SKU），并从 Docker Hub 推送示例映像，例如 `hello-world`。 例如，使用[Azure 门户][quickstart-portal]或[Azure CLI][quickstart-cli]创建注册表。 

* 如果要使用其他 Azure 订阅中的虚拟网络限制注册表访问，则需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>关于容器注册表的网络规则

默认情况下，Azure 容器注册表接受来自任何网络上的主机的 internet 连接。 使用虚拟网络，可以仅允许 Azure 资源（例如 AKS 群集或 Azure VM）安全访问注册表，而无需跨越网络边界。 你还可以将网络防火墙规则配置为仅允许特定的公共 internet IP 地址范围。 

若要限制对注册表的访问权限，请首先更改注册表的默认操作，使其拒绝所有网络连接。 然后，添加网络访问规则。 通过网络规则授予访问权限的客户端必须继续对[容器注册表进行身份验证](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)，并且有权访问数据。

### <a name="service-endpoint-for-subnets"></a>子网的服务终结点

若要允许从虚拟网络中的子网进行访问，需要为 Azure 容器注册表服务添加[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

多租户服务（如 Azure 容器注册表）为所有客户使用一组 IP 地址。 服务终结点分配终结点以访问注册表。 此终结点通过 Azure 主干网络为流量提供到资源的最佳路由。 虚拟网络和子网的标识也随每个请求进行传输。

### <a name="firewall-rules"></a>防火墙规则

对于 IP 网络规则，请使用 CIDR 表示法（如*16.17.18.0/24* ）或单个 IP 地址（例如*16.17.18.19*）提供允许的 internet 地址范围。 仅允许*公共*internet ip 地址使用 IP 网络规则。 IP 规则中不允许为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。

## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用了 Docker 的虚拟机

本文介绍如何使用启用了 Docker 的 Ubuntu VM 来访问 Azure 容器注册表。 若要对注册表使用 Azure Active Directory 身份验证，请在 VM 上安装[Azure CLI][azure-cli] 。 如果已有 Azure 虚拟机，请跳过此创建步骤。

你可以对虚拟机和容器注册表使用同一资源组。 此设置简化了在结束时的清理，但并不是必需的。 如果选择为虚拟机和虚拟网络创建单独的资源组，请运行[az group create][az-group-create]。 以下示例在 " *westcentralus* " 位置创建名为 " *myResourceGroup* " 的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

现在使用[az vm create][az-vm-create]部署默认 Ubuntu Azure 虚拟机。 以下示例创建名为*myDockerVM*的 VM：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址来建立与 VM 的 SSH 连接，并根据需要为防火墙规则设置更高的连接。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令，在 Ubuntu VM 上安装 Docker：

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

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 对于本文，请确保安装2.0.58 或更高版本。

退出 SSH 连接。

## <a name="allow-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

在本部分中，将容器注册表配置为允许从 Azure 虚拟网络中的子网进行访问。 提供的等效步骤使用 Azure CLI 和 Azure 门户。

### <a name="allow-access-from-a-virtual-network---cli"></a>允许从虚拟网络进行访问-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>将服务终结点添加到子网

创建 VM 时，Azure 默认情况下会在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为*myDockerVM*，则默认虚拟网络名称为*myDockerVMVNET*，子网名为*myDockerVMSubnet*。 在 Azure 门户或使用[az network vnet list][az-network-vnet-list]命令验证此内容：

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

输出：

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

使用[az network vnet subnet update][az-network-vnet-subnet-update]命令将**microsoft.containerregistry**服务终结点添加到子网。 在以下命令中，将虚拟网络和子网的名称替换：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az network vnet subnet show][az-network-vnet-subnet-show]命令检索子网的资源 ID。 你需要在后面的步骤中配置网络访问规则。

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

输出：

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>更改注册表的默认网络访问权限

默认情况下，Azure 容器注册表允许来自任何网络上的主机的连接。 若要限制对所选网络的访问，请将默认操作更改为 "拒绝访问"。 在下面的[az acr update][az-acr-update]命令中替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

使用[az acr 网络规则添加][az-acr-network-rule-add]命令将网络规则添加到允许从 VM 子网进行访问的注册表。 将容器注册表的名称和子网的资源 ID 替换为以下命令： 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

继续[验证对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允许从虚拟网络进行访问-门户

#### <a name="add-service-endpoint-to-subnet"></a>将服务终结点添加到子网

创建 VM 时，Azure 默认情况下会在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为*myDockerVM*，则默认虚拟网络名称为*myDockerVMVNET*，子网名为*myDockerVMSubnet*。

将 Azure 容器注册表的服务终结点添加到子网：

1. 在 " [Azure 门户][azure-portal]顶部的搜索框中，输入"*虚拟网络*"。 当“虚拟网络”出现在搜索结果中时，请将其选中。
1. 从虚拟网络列表中，选择要在其中部署虚拟机的虚拟网络，例如 " *myDockerVMVNET*"。
1. 在 "**设置**" 下，选择 "**子网**"。
1. 选择要在其中部署虚拟机的子网，例如 " *myDockerVMSubnet*"。
1. 在 "**服务终结点**" 下，选择**microsoft.containerregistry**。
1. 选择“保存”。

![将服务终结点添加到子网][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>为注册表配置网络访问权限

默认情况下，Azure 容器注册表允许来自任何网络上的主机的连接。 若要限制对虚拟网络的访问权限：

1. 在门户中，导航到容器注册表。
1. 在 "**设置**" 下，选择 "**防火墙和虚拟网络**"。
1. 若要默认拒绝访问，请选择允许从“所选网络”进行访问。 
1. 选择 "**添加现有虚拟网络**"，并选择使用服务终结点配置的虚拟网络和子网。 选择“添加”。
1. 选择“保存”。

![为容器注册表配置虚拟网络][acr-vnet-portal]

继续[验证对注册表的访问](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允许来自 IP 地址的访问

在本部分中，将容器注册表配置为允许从特定 IP 地址或范围进行访问。 提供的等效步骤使用 Azure CLI 和 Azure 门户。

### <a name="allow-access-from-an-ip-address---cli"></a>允许从 IP 地址进行访问-CLI

#### <a name="change-default-network-access-to-registry"></a>更改注册表的默认网络访问权限

如果尚未执行此操作，请将注册表配置更新为默认拒绝访问。 在下面的[az acr update][az-acr-update]命令中替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>从注册表删除网络规则

如果你以前添加了允许从 VM 的子网访问的网络规则，请删除子网的服务终结点和网络规则。 将容器注册表的名称和在 " [az acr 网络-规则" 删除][az-acr-network-rule-remove]命令的前面步骤中检索到的子网的资源 ID 替换为： 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

使用[az acr 网络规则添加][az-acr-network-rule-add]命令将网络规则添加到允许从 VM 的 IP 地址进行访问的注册表。 在以下命令中，将容器注册表的名称和 VM 的公共 IP 地址替换为。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

继续[验证对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允许来自 IP 地址的访问-门户

#### <a name="remove-existing-network-rule-from-registry"></a>从注册表中删除现有网络规则

如果你以前添加了允许从 VM 的子网访问的网络规则，请删除现有规则。 如果要从不同的 VM 访问注册表，请跳过此部分。

* 更新子网设置，删除 Azure 容器注册表的子网的服务终结点。 

  1. 在[Azure 门户][azure-portal]中，导航到部署了虚拟机的虚拟网络。
  1. 在 "**设置**" 下，选择 "**子网**"。
  1. 选择要在其中部署虚拟机的子网。
  1. 在 "**服务终结点**" 下，删除**microsoft.containerregistry**的复选框。 
  1. 选择“保存”。

* 删除允许子网访问注册表的网络规则。

  1. 在门户中，导航到容器注册表。
  1. 在 "**设置**" 下，选择 "**防火墙和虚拟网络**"。
  1. 在 "**虚拟网络**" 下，选择虚拟网络的名称，然后选择 "**删除**"。
  1. 选择“保存”。

#### <a name="add-network-rule-to-registry"></a>向注册表添加网络规则

1. 在门户中，导航到容器注册表。
1. 在 "**设置**" 下，选择 "**防火墙和虚拟网络**"。
1. 如果尚未执行此操作，请选择允许从**所选网络**进行访问。 
1. 在 "**虚拟网络**" 下，确保未选择网络。
1. 在 "**防火墙**" 下，输入 VM 的公共 IP 地址。 或者，以 CIDR 表示法输入包含 VM IP 地址的地址范围。
1. 选择“保存”。

![为容器注册表配置防火墙规则][acr-vnet-firewall-portal]

继续[验证对注册表的访问](#verify-access-to-the-registry)。

## <a name="verify-access-to-the-registry"></a>验证对注册表的访问权限

等待几分钟后更新配置，并验证 VM 是否可以访问容器注册表。 与 VM 建立 SSH 连接，并运行[az acr login][az-acr-login]命令以登录到注册表。 

```bash
az acr login --name mycontainerregistry
```

你可以执行注册表操作（如运行 `docker pull`）从注册表提取示例映像。 替换适用于注册表的图像和标记值，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像提取到 VM。

此示例演示了如何通过网络访问规则访问专用容器注册表。 但是，无法从未配置网络访问规则的其他登录主机访问注册表。 如果尝试使用 `az acr login` 命令或 `docker login` 命令从另一个主机登录，则输出类似于以下内容：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>还原默认注册表访问

若要将注册表还原为默认允许访问，请删除配置的所有网络规则。 然后，设置默认操作以允许访问。 提供的等效步骤使用 Azure CLI 和 Azure 门户。

### <a name="restore-default-registry-access---cli"></a>还原默认注册表访问-CLI

#### <a name="remove-network-rules"></a>删除网络规则

若要查看为你的注册表配置的网络规则列表，请运行以下[az acr 网络规则列表][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

对于配置的每个规则，请运行[az acr 网络规则 remove][az-acr-network-rule-remove]命令将其删除。 例如：

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>允许访问

在下面的[az acr update][az-acr-update]命令中替换注册表的名称：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>还原默认注册表访问门户


1. 在门户中，导航到容器注册表，选择 "**防火墙和虚拟网络**"。
1. 在 "**虚拟网络**" 下，选择每个虚拟网络，然后选择 "**删除**"。
1. 在 "**防火墙**" 下，选择每个地址范围，然后选择 "删除" 图标。
1. 在 "**允许访问**" 下，选择 "**所有网络**"。 
1. 选择“保存”。

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要这些资源，可以选择使用单个[az group delete](/cli/azure/group)命令删除资源：

```azurecli
az group delete --name myResourceGroup
```

若要在门户中清理资源，请导航到 myResourceGroup 资源组。 加载资源组后，单击 "**删除资源组**" 以删除存储在其中的资源组和资源。

## <a name="next-steps"></a>后续步骤

本文简要介绍了多个虚拟网络资源和功能。 Azure 虚拟网络文档全面介绍了这些主题：

* [虚拟网络](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [服务终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
