---
title: 将容器组部署到 Azure 虚拟网络
description: 了解如何使用 Azure 命令行接口将容器组部署到新的或现有的 Azure 虚拟网络。
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 7e54690efc7955eaaa88ca87a6f7a086dd3e19a4
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583642"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>将容器实例部署到 Azure 虚拟网络

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)为 Azure 资源和本地资源提供安全的专用网络。 将容器组部署到 Azure 虚拟网络后，容器可与该虚拟网络中的其他资源安全通信。

本文介绍如何使用 Azure CLI 中的[az container create][az-container-create]命令将容器组部署到新的虚拟网络或现有虚拟网络。 

有关网络方案和限制，请参阅[Azure 容器实例的虚拟网络方案和资源](container-instances-virtual-network-concepts.md)。

> [!IMPORTANT]
> 在大多数 Azure 容器实例可用的区域中，对虚拟网络的容器组部署通常适用于 Linux 容器。 有关详细信息，请参阅[区域和资源可用性](container-instances-virtual-network-concepts.md#where-to-deploy)。 

本文中的示例是针对 Bash shell 进行格式设置的。 若要使用其他 shell（例如 PowerShell 或命令提示符），请相应地调整续行符。


## <a name="deploy-to-new-virtual-network"></a>部署到新虚拟网络

若要部署到新虚拟网络并让 Azure 自动创建网络资源，请在执行 [az container create][az-container-create] 时指定以下信息：

* 虚拟网络名称
* 采用 CIDR 格式的虚拟网络地址前缀
* 子网名称
* 采用 CIDR 格式的子网地址前缀

虚拟网络和子网地址前缀分别指定虚拟网络和子网的地址空间。 这些值以无类域间路由 (CIDR) 表示法表示，例如 `10.0.0.0/16`。 有关使用子网的详细信息，请参阅[添加、更改或删除虚拟网络子网](../virtual-network/virtual-network-manage-subnet.md)。

使用此方法部署第一个容器组后，可以通过指定虚拟网络和子网名称或者 Azure 自动创建的网络配置文件，来部署到同一子网。 由于 Azure 将该子网委托给了 Azure 容器实例，因此只能将容器组部署到该子网。**

### <a name="example"></a>示例

以下[az container create][az-container-create]命令指定新的虚拟网络和子网的设置。 提供一个资源组的名称，该资源组在虚拟网络中的容器组部署[可用](container-instances-region-availability.md#availability---virtual-network-deployment)的区域中创建。 此命令将部署公共 Microsoft [aci-helloworld][aci-helloworld] 容器，该容器运行一个提供静态网页的小型 Node.js Web 服务器。 在下一部分，我们要将另一个容器组部署到同一子网，并测试这两个容器实例之间的通信。

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

使用此方法部署到新虚拟网络时，部署可能需要花费几分钟时间，因为在此期间需要创建网络资源。 初始部署完成后，对同一子网的附加容器组部署的速度将更快。

## <a name="deploy-to-existing-virtual-network"></a>部署到现有虚拟网络

将容器组部署到现有虚拟网络：

1. 在现有虚拟网络中创建一个子网，使用已在其中部署了容器组的现有子网，或使用已腾空了所有** 其他资源的现有子网
1. 使用 [az container create][az-container-create] 并指定以下信息之一来部署容器组：
   * 虚拟网络名称和子网名称
   * 虚拟网络资源 ID 和子网资源 ID，它允许使用其他资源组中的虚拟网络
   * 网络配置文件名称或 ID，可以使用 [az network profile list][az-network-profile-list] 获取

### <a name="example"></a>示例

以下示例会将第二个容器组部署到之前创建的同一子网，并验证两个容器实例之间的通信。

首先，获取部署的第一个容器组 *appcontainer* 的 IP 地址：

```azurecli
az container show --resource-group myResourceGroup \
  --name appcontainer \
  --query ipAddress.ip --output tsv
```

输出显示专用子网中的容器组的 IP 地址。 例如：

```console
10.0.0.4
```

现在，请将 `CONTAINER_GROUP_IP` 设置为使用 `az container show` 命令检索到的 IP，并执行以下 `az container create` 命令。 这第二个容器 *commchecker* 运行基于 Alpine Linux 的映像，并针对第一个容器组的专用子网 IP 地址执行 `wget`。

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-address>

az container create \
  --resource-group myResourceGroup \
  --name commchecker \
  --image alpine:3.5 \
  --command-line "wget $CONTAINER_GROUP_IP" \
  --restart-policy never \
  --vnet aci-vnet \
  --subnet aci-subnet
```

完成第二个容器的部署后，请提取其日志，以查看它执行的 `wget` 命令的输出：

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

如果第二个容器成功与第一个容器通信，则输出类似于：

```console
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

日志输出应显示 `wget` 可以在本地子网中使用第一个容器的专用 IP 地址连接到该容器，并可从中下载索引文件。 两个容器组之间的网络流量保留在虚拟网络中。

### <a name="example---yaml"></a>示例-YAML

还可以通过使用 YAML 文件、[资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)或其他编程方法（如使用 Python SDK）将容器组部署到现有虚拟网络。 

例如，使用 YAML 文件时，可以部署到一个虚拟网络，并将子网委托给 Azure 容器实例。 指定以下属性：

* `ipAddress`：容器组的专用 IP 地址设置。
  * `ports`：要打开的端口（如果有）。
  * `protocol`：打开的端口的协议（TCP 或 UDP）。
* `networkProfile`：虚拟网络和子网的网络设置。
  * `id`：`networkProfile` 的完整资源管理器资源 ID。

若要获取网络配置文件的 ID，请运行[az network profile list][az-network-profile-list]命令，并指定包含虚拟网络和委托子网的资源组的名称。

``` azurecli
az network profile list --resource-group myResourceGroup \
  --query [0].id --output tsv
```

示例输出：

```console
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

获取网络配置文件 ID 后，将以下 YAML 复制到名为 *vnet-deploy-aci.yaml* 的新文件中。 在 `networkProfile` 下，将 `id` 值替换为刚刚检索到的 ID，然后保存文件。 此 YAML 会在虚拟网络中创建名为 *appcontaineryaml* 的容器组。

```YAML
apiVersion: '2018-10-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

使用 [az container create][az-container-create] 命令并在 `--file` 参数中指定 YAML 文件名，以部署容器组：

```azurecli
az container create --resource-group myResourceGroup \
  --file vnet-deploy-aci.yaml
```

部署完成后，运行[az container show][az-container-show]命令以显示其状态。 示例输出：

```console
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>清理资源

### <a name="delete-container-instances"></a>删除容器实例

处理完所创建的容器实例后，使用以下命令将其删除：

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>删除网络资源

此功能当前需要执行几个其他命令才能删除前面创建的网络资源。 如果你在本文的前面几个部分中使用示例命令创建了虚拟网络和子网，则可以使用以下脚本来删除这些网络资源。 此脚本假设资源组包含单个网络配置文件的单个虚拟网络。

执行该脚本之前，请将 `RES_GROUP` 变量设置为包含所要删除的虚拟网络和子网的资源组的名称。 如果未使用之前建议的 `aci-vnet` 名称，请更新虚拟网络的名称。 此脚本已针对 Bash Shell 格式化。 若要使用其他 shell（例如 PowerShell 或命令提示符），需要相应地调整变量赋值和访问器。

> [!WARNING]
> 此脚本会删除资源！ 它会删除虚拟网络及其包含的所有子网。 运行此脚本之前，请确认你不再需要该虚拟网络中的任何资源，包括其中的任何子网。** 一旦删除，**这些资源就不可恢复**。

```azurecli
# Replace <my-resource-group> with the name of your resource group
# Assumes one virtual network in resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
# Assumes one profile in virtual network
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>后续步骤

若要使用资源管理器模板部署新的虚拟网络、子网、网络配置文件和容器组，请参阅 [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)（使用 VNet 创建 Azure 容器组）。

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
