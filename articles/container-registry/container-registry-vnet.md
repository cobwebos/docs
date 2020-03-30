---
title: 限制使用虚拟网络的访问
description: 仅允许从 Azure 虚拟网络中的资源或公共 IP 地址范围访问 Azure 容器注册表。
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454340"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 Azure 资源和本地资源提供安全的专用网络。 通过限制从 Azure 虚拟网络访问专用 Azure 容器注册表，可确保只有虚拟网络中的资源访问注册表。 对于跨界方案，还可以配置防火墙规则，以便仅允许从特定 IP 地址进行注册表访问。

本文演示了两种在容器注册表上配置入站网络访问规则的方案：从部署在虚拟网络中的虚拟机或从 VM 的公共 IP 地址。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

如果需要为资源设置访问规则才能从防火墙后面到达容器注册表，请参阅[配置规则以访问防火墙后面的 Azure 容器注册表](container-registry-firewall-access-rules.md)。


## <a name="preview-limitations"></a>预览版限制

* 只能使用网络访问规则配置**高级**容器注册表。 有关注册表服务层级的信息，请参阅 [Azure 容器注册表 SKU](container-registry-skus.md)。 

* 只能将[Azure 库伯奈斯服务](../aks/intro-kubernetes.md)群集或 Azure[虚拟机](../virtual-machines/linux/overview.md)用作主机来访问虚拟网络中的容器注册表。 *当前不支持其他 Azure 服务（包括 Azure 容器实例）。*

* 在虚拟网络中访问的容器注册表中，当前不支持[ACR 任务](container-registry-tasks-overview.md)操作。

* 每个注册表最多支持 100 个虚拟网络规则。

## <a name="prerequisites"></a>先决条件

* 要使用本文中的 Azure CLI 步骤，需要 Azure CLI 版本 2.0.58 或更高版本。 如果需要安装或升级，请参阅[安装 Azure CLI][azure-cli]。

* 如果还没有容器注册表，请创建一个（需要高级 SKU）并推送示例映像（如`hello-world`从 Docker Hub）。 例如，使用[Azure 门户][quickstart-portal]或[Azure CLI][quickstart-cli]创建注册表。 

* 如果要限制使用其他 Azure 订阅中的虚拟网络进行注册表访问，则需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>关于容器注册表的网络规则

默认情况下，Azure 容器注册表接受来自任何网络上的主机的 Internet 连接。 使用虚拟网络，只能允许 Azure 资源（如 AKS 群集或 Azure VM）安全地访问注册表，而无需跨越网络边界。 您还可以配置网络防火墙规则，以仅允许特定的公共互联网 IP 地址范围。 

要限制对注册表的访问，首先更改注册表的默认操作，以便它拒绝所有网络连接。 然后，添加网络访问规则。 通过网络规则授予访问权限的客户端必须继续[对容器注册表进行身份验证](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)，并有权访问数据。

### <a name="service-endpoint-for-subnets"></a>子网的服务终结点

要允许从虚拟网络中的子网进行访问，需要为 Azure 容器注册表服务添加[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)。 

多租户服务（如 Azure 容器注册表）对所有客户使用一组 IP 地址。 服务终结点分配终结点以访问注册表。 此终结点为流量提供了通过 Azure 骨干网到资源的最佳路由。 虚拟网络和子网的标识也随每个请求进行传输。

### <a name="firewall-rules"></a>防火墙规则

对于 IP 网络规则，使用 CIDR 符号（如*16.17.18.0/24）* 或单个 IP 地址（如*16.17.18.19）* 提供允许的互联网地址范围。 IP 网络规则仅适用于*公共*互联网 IP 地址。 IP 规则不允许使用为专用网络保留的 IP 地址范围（如 RFC 1918 中所定义）。

## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用 Docker 的虚拟机

在本文中，使用启用 Docker 的 Ubuntu VM 访问 Azure 容器注册表。 要对注册表使用 Azure 活动目录身份验证，请在 VM 上安装[Azure CLI。][azure-cli] 如果您已有 Azure 虚拟机，请跳过此创建步骤。

您可以对虚拟机和容器注册表使用相同的资源组。 此设置简化了清理，但不需要。 如果选择为虚拟机和虚拟网络创建单独的资源组，请运行[az 组创建][az-group-create]。 下面的示例*在中西部*位置创建名为*myResourceGroup*的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

现在部署默认的 Ubuntu Azure 虚拟机，该虚拟机具有[az vm 创建][az-vm-create]。 下面的示例创建名为*myDockerVM 的 VM*：

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址可以使 SSH 连接到 VM，并可以选择以后设置防火墙规则。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令以在 Ubuntu VM 上安装 Docker：

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

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 对于本文，请确保安装版本 2.0.58 或更高版本。

退出 SSH 连接。

## <a name="allow-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

在本节中，将容器注册表配置为允许从 Azure 虚拟网络中的子网进行访问。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="allow-access-from-a-virtual-network---cli"></a>允许从虚拟网络访问 - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>将服务终结点添加到子网

创建 VM 时，Azure 默认情况下在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果您命名虚拟机*myDockerVM，* 则默认虚拟网络名称为*myDockerVMVNET，* 其子网名为*myDockerVMSubnet*。 在 Azure 门户中或使用 az[网络 vnet 列表][az-network-vnet-list]命令验证此情况：

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

使用[az 网络 vnet 子网更新][az-network-vnet-subnet-update]命令将**Microsoft.ContainerRegistry**服务终结点添加到子网。 在以下命令中替换虚拟网络和子网的名称：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az 网络 vnet 子网显示][az-network-vnet-subnet-show]命令检索子网的资源 ID。 您需要在后面的步骤中配置网络访问规则。

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

#### <a name="change-default-network-access-to-registry"></a>更改对注册表的默认网络访问

默认情况下，Azure 容器注册表允许从任何网络上的主机进行连接。 要限制对所选网络的访问，更改默认操作以拒绝访问。 在以下[az acr 更新][az-acr-update]命令中替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>将网络规则添加到注册表

使用[az acr 网络规则添加][az-acr-network-rule-add]命令向注册表添加网络规则，允许从 VM 的子网进行访问。 在以下命令中替换容器注册表的名称和子网的资源 ID： 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

继续[验证对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允许从虚拟网络访问 - 门户

#### <a name="add-service-endpoint-to-subnet"></a>将服务终结点添加到子网

创建 VM 时，Azure 默认情况下在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果您命名虚拟机*myDockerVM，* 则默认虚拟网络名称为*myDockerVMVNET，* 其子网名为*myDockerVMSubnet*。

要将 Azure 容器注册表的服务终结点添加到子网：

1. 在[Azure 门户][azure-portal]顶部的搜索框中，输入*虚拟网络*。 当“虚拟网络”出现在搜索结果中时，请将其选中****。
1. 从虚拟网络列表中，选择部署虚拟机的虚拟网络，如*myDockerVMVNET*。
1. 在 **"设置"** 下，选择**子网**。
1. 选择部署虚拟机的子网，例如*myDockerVM Subnet*。
1. 在**服务终结点**下，选择**Microsoft.Container 注册 。**
1. 选择“保存”。****

![将服务终结点添加到子网][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>为注册表配置网络访问

默认情况下，Azure 容器注册表允许从任何网络上的主机进行连接。 要限制对虚拟网络的访问，可以：

1. 在门户中，导航到容器注册表。
1. 在 **"设置"** 下，选择**防火墙和虚拟网络**。
1. 默认情况下，要拒绝访问，请选择允许从**选定网络**进行访问。 
1. 选择 **"添加现有虚拟网络**"，然后选择使用服务终结点配置的虚拟网络和子网。 选择“添加”****。
1. 选择“保存”。****

![为容器注册表配置虚拟网络][acr-vnet-portal]

继续[验证对注册表的访问](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允许从 IP 地址访问

在本节中，将容器注册表配置为允许从特定 IP 地址或范围进行访问。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="allow-access-from-an-ip-address---cli"></a>允许从 IP 地址访问 - CLI

#### <a name="change-default-network-access-to-registry"></a>更改对注册表的默认网络访问

如果尚未这样做，请更新注册表配置以默认拒绝访问。 在以下[az acr 更新][az-acr-update]命令中替换注册表的名称：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>从注册表中删除网络规则

如果以前添加了网络规则以允许从 VM 的子网进行访问，请删除子网的服务终结点和网络规则。 替换在[az acr 网络规则删除][az-acr-network-rule-remove]命令中较早步骤中检索的容器注册表的名称和子网的资源 ID： 

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

#### <a name="add-network-rule-to-registry"></a>将网络规则添加到注册表

使用[az acr 网络规则添加][az-acr-network-rule-add]命令向注册表添加网络规则，允许从 VM 的 IP 地址进行访问。 在以下命令中替换容器注册表的名称和 VM 的公共 IP 地址。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

继续[验证对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允许从 IP 地址访问 - 门户

#### <a name="remove-existing-network-rule-from-registry"></a>从注册表中删除现有网络规则

如果以前添加了网络规则以允许从 VM 的子网进行访问，请删除现有规则。 如果要从其他 VM 访问注册表，请跳过此部分。

* 更新子网设置以删除 Azure 容器注册表的子网的服务终结点。 

  1. 在[Azure 门户][azure-portal]中，导航到部署虚拟机的虚拟网络。
  1. 在 **"设置"** 下，选择**子网**。
  1. 选择部署虚拟机的子网。
  1. 在**服务终结点**下，删除**Microsoft.Container 注册处**的复选框。 
  1. 选择“保存”。****

* 删除允许子网访问注册表的网络规则。

  1. 在门户中，导航到容器注册表。
  1. 在 **"设置"** 下，选择**防火墙和虚拟网络**。
  1. 在 **"虚拟网络**"下，选择虚拟网络的名称，然后选择 **"删除**"。
  1. 选择“保存”。****

#### <a name="add-network-rule-to-registry"></a>将网络规则添加到注册表

1. 在门户中，导航到容器注册表。
1. 在 **"设置"** 下，选择**防火墙和虚拟网络**。
1. 如果尚未这样做，请选择允许从**选定网络**进行访问。 
1. 在**虚拟网络**下，确保不选择网络。
1. 在**防火墙**下，输入 VM 的公共 IP 地址。 或者，在包含 VM IP 地址的 CIDR 表示法中输入地址范围。
1. 选择“保存”。****

![为容器注册表配置防火墙规则][acr-vnet-firewall-portal]

继续[验证对注册表的访问](#verify-access-to-the-registry)。

## <a name="verify-access-to-the-registry"></a>验证对注册表的访问

等待配置更新几分钟后，验证 VM 是否可以访问容器注册表。 建立与 VM 的 SSH 连接，并运行[az acr 登录][az-acr-login]命令以登录到注册表。 

```bash
az acr login --name mycontainerregistry
```

可以执行注册表操作（如运行`docker pull`）从注册表中提取示例映像。 替换适合您的注册表的图像和标记值，该值以注册表登录服务器名称（所有小写）为预缀：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像拉到 VM。

此示例演示了您可以通过网络访问规则访问专用容器注册表。 但是，无法从未配置网络访问规则的不同登录主机访问注册表。 如果尝试使用`az acr login`命令或`docker login`命令从其他主机登录，则输出类似于以下内容：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>恢复默认注册表访问

要还原注册表以默认允许访问，请删除配置的任何网络规则。 然后设置默认操作以允许访问。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="restore-default-registry-access---cli"></a>恢复默认注册表访问 - CLI

#### <a name="remove-network-rules"></a>删除网络规则

要查看为注册表配置的网络规则列表，请运行以下[az acr 网络规则列表][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

对于配置的每个规则，运行 az [acr 网络规则删除][az-acr-network-rule-remove]命令以将其删除。 例如：

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

在以下[az acr 更新][az-acr-update]命令中替换注册表的名称：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>恢复默认注册表访问 - 门户


1. 在门户中，导航到容器注册表并选择**防火墙和虚拟网络**。
1. 在**虚拟网络**下，选择每个虚拟网络，然后选择 **"删除**"。
1. 在 **"防火墙"** 下，选择每个地址范围，然后选择"删除"图标。
1. 在 **"允许从 "访问**"下，选择 **"所有网络**"。 
1. 选择“保存”。****

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要它们，则可以选择使用单个[az 组删除](/cli/azure/group)命令删除资源：

```azurecli
az group delete --name myResourceGroup
```

要清理门户中的资源，请导航到 myResourceGroup 资源组。 加载资源组后，单击"**删除资源组"** 以删除资源组和存储的资源。

## <a name="next-steps"></a>后续步骤

本文简要介绍了多个虚拟网络资源和功能。 Azure 虚拟网络文档全面介绍了这些主题：

* [虚拟网络](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [子](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
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
