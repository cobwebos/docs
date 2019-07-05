---
title: 从虚拟网络到 Azure 容器注册表限制访问
description: 允许访问到 Azure 容器注册表中，仅从 Azure 虚拟网络中的资源或公共 IP 地址范围。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/01/2019
ms.author: danlep
ms.openlocfilehash: 06e45127f940e01de5f3ceeefc354014a88014db
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514397"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>限制对 Azure 容器注册表使用 Azure 虚拟网络或防火墙规则的访问

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)提供安全、 专用网络，以便将你的 Azure 和本地资源。 通过限制访问到专用 Azure 容器注册表从 Azure 虚拟网络，可确保仅在虚拟网络中的资源访问注册表。 对于跨界方案，还可以配置防火墙规则以允许仅从特定 IP 地址的注册表访问权限。

本文介绍两个方案，以创建网络访问规则，以限制对 Azure 容器注册表的访问： 从虚拟网络中部署的虚拟机或虚拟机的公共 IP 地址。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

## <a name="preview-limitations"></a>预览版限制

* 仅**高级**可以使用网络访问规则来配置容器注册表。 有关注册表服务层的信息，请参阅[Azure 容器注册表 Sku](container-registry-skus.md)。 

* 仅[Azure Kubernetes 服务](../aks/intro-kubernetes.md)群集或 Azure[虚拟机](../virtual-machines/linux/overview.md)可用于为主机访问虚拟网络中的容器注册表。 *目前不支持其他 Azure 服务包括 Azure 容器实例。*

* [ACR 任务](container-registry-tasks-overview.md)操作当前不支持在容器注册表中访问虚拟网络中。

* 每个注册表支持最多为 100 的虚拟网络规则。

## <a name="prerequisites"></a>必备组件

* 若要使用 Azure CLI 在本文中，Azure CLI 版本 2.0.58 中执行步骤或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。

* 如果还没有容器注册表，创建一个 (所需的高级 SKU)，并如推送的示例图像`hello-world`从 Docker 中心。 例如，使用[Azure 门户][quickstart-portal]or the [Azure CLI][quickstart-cli]创建注册表。 

## <a name="about-network-rules-for-a-container-registry"></a>有关容器注册表的网络规则

默认情况下的 Azure 容器注册表接受通过 internet 从任何网络上的主机的连接。 通过虚拟网络，可以允许仅 Azure 资源，例如 AKS 群集或 Azure VM 可以安全地访问注册表中，而无需跨网络边界。 此外可以配置网络防火墙规则允许列表到特定的公共 internet IP 地址范围。 

若要限制对注册表的访问，请首先更改注册表的默认操作，以便拒绝所有网络连接。 然后，添加网络访问规则。 客户端授予访问权限通过网络规则必须继续[到容器注册表进行身份验证](https://docs.microsoft.com/azure/container-registry/container-registry-authentication)和有权访问的数据。

### <a name="service-endpoint-for-subnets"></a>服务终结点的子网

若要允许来自子网中访问虚拟网络，需要添加[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)Azure 容器注册表服务。 

多租户服务，如 Azure 容器注册表，使用单一组的 IP 地址的所有客户。 服务终结点将分配一个终结点来访问注册表。 此终结点提供流量的最佳路由到的资源通过 Azure 主干网络。 虚拟网络和子网的标识也随每个请求进行传输。

### <a name="firewall-rules"></a>防火墙规则

对于 IP 网络规则，提供允许从 internet 地址范围，如使用 CIDR 表示法*16.17.18.0/24*或单独 IP 地址将像*16.17.18.19*。 IP 网络规则仅适用于*公共*internet IP 地址。 （如在 RFC 1918 中所定义），为专用网络保留的 IP 地址范围不允许在 IP 规则。

## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用 Docker 的虚拟机

在本文中，使用在启用 Docker 的 Ubuntu 虚拟机访问的 Azure 容器注册表。 若要对注册表使用 Azure Active Directory 身份验证，还安装[Azure CLI][azure-cli]在 VM 上。 如果已有 Azure 虚拟机，跳过此步骤中创建。

你的虚拟机和容器注册表，可能会使用相同的资源组。 此安装程序简化了清理结束时，但不是必需的。 如果您选择创建的虚拟机和虚拟网络的单独的资源组，运行[az 组创建][az-group-create]。 下面的示例创建名为的资源组*myResourceGroup*中*westcentralus*位置：

```azurecli
az group create --name myResourceGroup --location westus
```

现在，将默认值部署 Ubuntu Azure 虚拟机[az vm 创建][az-vm-create]。 下面的示例创建名为的 VM *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 需要几分钟时间。 等该命令完成后，记下 Azure CLI 显示的 `publicIpAddress`。 使用此地址来建立 SSH 连接到 VM，并可用于更高版本安装的防火墙规则。

### <a name="install-docker-on-the-vm"></a>在 VM 上安装 Docker

等 VM 正常运行后，与 VM 建立 SSH 连接。 将 *publicIpAddress* 替换为 VM 的公共 IP 地址。

```bash
ssh azureuser@publicIpAddress
```

运行以下命令以在 Ubuntu VM 上安装 Docker:

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

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 对于本文中，请确保安装版本 2.0.58 或更高版本。

退出 SSH 连接。

## <a name="allow-access-from-a-virtual-network"></a>允许从虚拟网络进行访问

在本部分中，将容器注册表配置为允许来自子网中的 Azure 虚拟网络访问。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="allow-access-from-a-virtual-network---cli"></a>允许从虚拟网络-CLI 进行访问

#### <a name="add-a-service-endpoint-to-a-subnet"></a>将服务终结点添加到子网

在创建 VM 时，默认情况下的 Azure 同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果为虚拟机命名*myDockerVM*，默认虚拟网络名称是*myDockerVMVNET*，与名为的子网*myDockerVMSubnet*。 在 Azure 门户或通过使用对此进行验证[az 网络 vnet 列表][az-network-vnet-list]命令：

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

使用[az 网络 vnet 子网更新][az-network-vnet-subnet-update]命令，将添加**Microsoft.ContainerRegistry**与子网的服务终结点。 替换为虚拟网络和以下命令中的子网的名称：

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

使用[az 网络 vnet 子网显示][az-network-vnet-subnet-show]命令来检索子网的资源 ID。 您需要它在稍后的步骤来配置网络访问规则。

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

#### <a name="change-default-network-access-to-registry"></a>更改注册表的默认网络访问

默认情况下，Azure 容器注册表允许任何网络上的主机的连接。 若要限制对所选网络的访问，更改用于拒绝访问的默认操作。 在下面的示例注册表的名称替换[az acr update][az-acr-update]命令：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>将网络规则添加到注册表

使用[az acr 网络规则添加][az-acr-network-rule-add]命令，将网络规则添加到注册表，允许从虚拟机的子网访问。 替换为容器注册表的名称和以下命令中的子网的资源 ID: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

继续[验证是否对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-a-virtual-network---portal"></a>允许访问从虚拟网络-门户

#### <a name="add-service-endpoint-to-subnet"></a>将服务终结点添加到子网

在创建 VM 时，默认情况下的 Azure 同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果为虚拟机命名*myDockerVM*，默认虚拟网络名称是*myDockerVMVNET*，与名为的子网*myDockerVMSubnet*。

若要将服务终结点为 Azure 容器注册表添加到子网：

1. 在顶部的搜索框[Azure 门户][azure-portal]，输入*虚拟网络*。 当“虚拟网络”出现在搜索结果中时，请将其选中  。
1. 从虚拟网络列表中，选择你的虚拟机部署的位置，如虚拟网络*myDockerVMVNET*。
1. 下**设置**，选择**子网**。
1. 选择你的虚拟机部署的位置，如子网*myDockerVMSubnet*。
1. 下**服务终结点**，选择**Microsoft.ContainerRegistry**。
1. 选择“保存”。 

![将服务终结点添加到子网][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>配置网络访问的注册表

默认情况下，Azure 容器注册表允许任何网络上的主机的连接。 若要限制对虚拟网络的访问：

1. 在门户中，导航到容器注册表。
1. 下**设置**，选择**防火墙和虚拟网络**。
1. 若要默认拒绝访问，请选择允许从“所选网络”进行访问  。 
1. 选择**将现有虚拟网络添加**，并选择虚拟网络和子网配置的服务终结点。 选择 **添加** 。
1. 选择“保存”。 

![配置容器注册表的虚拟网络][acr-vnet-portal]

继续[验证是否对注册表的访问](#verify-access-to-the-registry)。

## <a name="allow-access-from-an-ip-address"></a>允许从 IP 地址进行访问

在本部分中，将容器注册表配置为允许来自特定 IP 地址或范围的访问。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="allow-access-from-an-ip-address---cli"></a>允许访问来自某个 IP 地址-CLI

#### <a name="change-default-network-access-to-registry"></a>更改注册表的默认网络访问

如果尚未这样做，更新要默认拒绝访问的注册表配置。 在下面的示例注册表的名称替换[az acr update][az-acr-update]命令：

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>从注册表中删除网络规则

如果你之前添加网络规则，以允许从虚拟机的子网进行访问，请删除子网的服务终结点和网络规则。 使用容器注册表的名称和在前面步骤中检索到的子网的资源 ID 替换[az acr 网络规则删除][az-acr-network-rule-remove]命令： 

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

使用[az acr 网络规则添加][az-acr-network-rule-add]命令，将网络规则添加到注册表，允许来自 VM 的 IP 地址的访问。 替换为容器注册表的名称和以下命令中的 VM 的公共 IP 地址。

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

继续[验证是否对注册表的访问](#verify-access-to-the-registry)。

### <a name="allow-access-from-an-ip-address---portal"></a>允许访问来自某个 IP 地址-门户

#### <a name="remove-existing-network-rule-from-registry"></a>从注册表中删除现有的网络规则

如果你之前添加网络规则，以允许从虚拟机的子网进行访问，请删除现有规则。 如果你想要从不同的 VM 访问注册表，请跳过此部分。

* 更新子网设置，为 Azure 容器注册表中删除子网的服务终结点。 

  1. 在中[Azure 门户][azure-portal]，导航到在其中部署你的虚拟机的虚拟网络。
  1. 下**设置**，选择**子网**。
  1. 选择在其中部署你的虚拟机的子网。
  1. 下**服务终结点**，删除的复选框**Microsoft.ContainerRegistry**。 
  1. 选择“保存”。 

* 删除允许访问注册表的子网的网络规则。

  1. 在门户中，导航到容器注册表。
  1. 下**设置**，选择**防火墙和虚拟网络**。
  1. 下**虚拟网络**，选择虚拟网络的名称，然后选择**删除**。
  1. 选择“保存”。 

#### <a name="add-network-rule-to-registry"></a>将网络规则添加到注册表

1. 在门户中，导航到容器注册表。
1. 下**设置**，选择**防火墙和虚拟网络**。
1. 如果尚未执行此操作，选择允许从访问**选定的网络**。 
1. 下**虚拟网络**，确保已选中任何网络。
1. 下**防火墙**，输入 VM 的公共 IP 地址。 或者，在包含 VM 的 IP 地址的 CIDR 表示法输入的地址范围。
1. 选择“保存”。 

![配置容器注册表的防火墙规则][acr-vnet-firewall-portal]

继续[验证是否对注册表的访问](#verify-access-to-the-registry)。

## <a name="verify-access-to-the-registry"></a>验证对注册表的访问

之后等待几分钟的配置更新，请验证 VM 可以访问容器注册表。 请通过 SSH 连接到你的 VM，并运行[az acr login][az-acr-login]命令登录到注册表。 

```bash
az acr login --name mycontainerregistry
```

你可以执行注册表操作，例如运行`docker pull`从注册表提取的示例图像。 替换为适用于你使用注册表登录服务器名称 （全部小写） 作为前缀的注册表的映像和标记值：

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将该映像拉取到 VM。

此示例演示您可以通过网络访问规则访问专用容器注册表。 但是，从不同的登录名主机不具有配置了网络访问规则，不能访问注册表。 如果你尝试从另一台主机使用登录`az acr login`命令或`docker login`命令中，输出为如下所示：

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>还原默认注册表访问权限

若要还原注册表，以允许访问默认情况下，删除任何配置的网络规则。 然后设置以允许访问的默认操作。 提供了使用 Azure CLI 和 Azure 门户的等效步骤。

### <a name="restore-default-registry-access---cli"></a>还原默认注册表访问-CLI

#### <a name="remove-network-rules"></a>删除网络规则

若要查看为您的注册表配置的网络规则的列表，请运行以下[az acr 网络规则列表][az-acr-network-rule-list]命令：

```azurecli
az acr network-rule list--name mycontainerregistry 
```

对于配置为每个规则，请运行[az acr 网络规则删除][az-acr-network-rule-remove]命令以将其删除。 例如：

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

在下面的示例注册表的名称替换[az acr update][az-acr-update]命令：
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>还原默认注册表访问-门户


1. 在门户中，导航到容器注册表，并选择**防火墙和虚拟网络**。
1. 下**虚拟网络**，选择每个虚拟网络，然后选择**删除**。
1. 下**防火墙**，选择每个地址范围，并选择删除图标。
1. 下**允许从访问**，选择**的所有网络**。 
1. 选择“保存”。 

## <a name="clean-up-resources"></a>清理资源

如果你创建所有相同的资源中的 Azure 资源组，并不再需要它们，或者可以使用单个删除的资源[az 组删除](/cli/azure/group)命令：

```azurecli
az group delete --name myResourceGroup
```

若要清理你的资源在门户中，导航到 myResourceGroup 资源组。 已加载的资源组，请单击**删除资源组**来删除资源组和存储在此处的资源。

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
