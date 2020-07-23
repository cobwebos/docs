---
title: 设置专用链接
description: 在容器注册表上设置专用终结点，并通过本地虚拟网络中的专用链接启用访问。 私有链接访问是高级服务层的一项功能。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 713b19e4a60e5dcad6cfd92d65f97af2e921c0e9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523836"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>使用 Azure Private Link 将专用连接到 Azure 容器注册表


通过将虚拟网络专用 IP 地址分配到注册表终结点并使用[Azure 专用链接](../private-link/private-link-overview.md)，限制对注册表的访问。 虚拟网络上的客户端与注册表的专用终结点之间的网络流量将遍历 Microsoft 主干网络上的虚拟网络和专用链接，从而消除了公共 internet 的泄露。 通过 "专用链接"，还可以通过[Azure ExpressRoute](../expressroute/expressroute-introduction.MD)专用对等互连或[VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)启用从本地进行的私有注册表访问。

你可以为注册表的专用终结点[配置 DNS 设置](../private-link/private-endpoint-overview.md#dns-configuration)，以使这些设置解析为注册表分配的专用 IP 地址。 使用 DNS 配置时，网络中的客户端和服务可以继续按注册表的完全限定的域名（如 myregistry.azurecr.io）来访问注册表。 

此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表层级](container-registry-skus.md)。


## <a name="things-to-know"></a>使用须知

* 当前，在使用专用终结点配置的注册表无法使用 Azure 安全中心进行映像扫描。
* 目前，最多可以为注册表设置10个私有终结点。

## <a name="prerequisites"></a>先决条件

* 若要使用本文中所述的 Azure CLI 步骤，建议安装 Azure CLI 版本 2.6.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或是在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行。
* 如果还没有容器注册表，请创建一个（需要“高级”层级），并[导入](container-registry-import-images.md)示例映像，如来自 Docker 的 `hello-world`。 例如，使用 [Azure 门户][quickstart-portal]或 [Azure CLI][quickstart-cli] 创建注册表。
* 若要使用其他 Azure 订阅中的专用链接配置注册表访问，需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 示例使用以下环境变量。 替换为适合于环境的值。 所有示例都针对 Bash shell 进行格式设置：

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>设置专用链接 - CLI

### <a name="get-network-and-subnet-names"></a>获取网络和子网名称

如果尚未获取，则需要虚拟网络和子网的名称才能设置专用链接。 在此示例中，将对 VM 和注册表的专用终结点使用相同子网。 但是，在许多情况下，会在单独子网中设置终结点。 

创建 VM 时，Azure 默认情况下会在同一个资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为 myDockerVM，则默认虚拟网络名称为 myDockerVMVNET，其中包含名为 myDockerVMSubnet 的子网。 通过运行 [az network vnet list][az-network-vnet-list] 命令在环境变量中设置这些值：

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>在子网中禁用网络策略

[禁用网络策略](../private-link/disable-private-endpoint-network-policy.md)，如用于专用终结点的子网中的网络安全组。 使用 [az network vnet subnet update][az-network-vnet-subnet-update] 更新子网配置：

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

为专用 Azure 容器注册表域创建专用 DNS 区域。 在后续步骤中，你将在此 DNS 区域中为你的注册表域创建 DNS 记录。

若要使用专用区域替代 Azure 容器注册表的默认 DNS 解析，区域必须命名为 privatelink.azurecr.io。 运行以下 [az network private-dns zone create][az-network-private-dns-zone-create] 命令以创建专用区域：

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>创建关联链接

运行 [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] 以将专用区域与虚拟网络相关联。 此示例会创建名为 myDNSLink的链接。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>创建专用注册表终结点

在此部分中，将在虚拟网络中创建注册表的专用终结点。 首先，获取注册表的资源 ID：

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

运行 [az network private-endpoint create][az-network-private-endpoint-create] 命令以创建注册表的专用终结点。

下面的示例创建终结点 myPrivateEndpoint 和服务连接 myConnection。 若要为终结点指定容器注册表资源，请传递 `--group-ids registry`：

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>获取专用 IP 地址

运行 [az network private-endpoint show][az-network-private-endpoint-show] 以查询网络接口 ID 的终结点：

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

在此示例中，与网络接口关联的是容器注册表的两个专用 IP 地址：一个用于注册表本身，另一个用于注册表的数据终结点。 以下 [az resource show][az-resource-show] 命令获取容器注册表和注册表数据终结点的专用 IP 地址：

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> 如果注册表是[异地复制](container-registry-geo-replication.md)，请查询每个注册表副本的附加数据终结点。

### <a name="create-dns-records-in-the-private-zone"></a>在专用区域中创建 DNS 记录

以下命令在专用区域中为注册表终结点及其数据终结点创建 DNS 记录。 例如，如果在 westeurope 区域中有一个名为 myregistry 的注册表，则终结点名称是 `myregistry.azurecr.io` 和 `myregistry.westeurope.data.azurecr.io`。 

> [!NOTE]
> 如果注册表是[异地复制](container-registry-geo-replication.md)，请为每个副本的数据终结点 IP 创建附加 DNS 记录。

首先运行 [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] 以便为注册表终结点和数据终结点创建空的 A 记录集：

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

运行 [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] 命令以便为注册表终结点和数据终结点创建 A 记录：

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

专用链接现已配置，可供使用。

## <a name="set-up-private-link---portal"></a>设置专用链接 - 门户

可在创建注册表时设置专用链接，或向现有注册表添加专用链接。 以下步骤假设已使用 VM 设置虚拟网络和子网以进行测试。 也可以[创建新虚拟网络和子网](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint---new-registry"></a>创建专用终结点 - 新注册表

1. 在门户中创建注册表时，请在“基本信息”选项卡上的“SKU”中，选择“高级”。
1. 选择“网络”选项卡。
1. 在“网络连接”中，选择“专用终结点” > “+ 添加”。
1. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组名称或创建一个新组。|
    | 名称 | 输入唯一名称。 |
    | 子资源 |选择“注册表”|
    | **网络** | |
    | 虚拟网络| 选择要在其中部署虚拟机的虚拟网络，例如 myDockerVMVNET。 |
    | 子网 | 选择要在其中部署虚拟机的子网，例如 myDockerVMSubnet。 |
    |专用 DNS 集成||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择“(新) privatelink.azurecr.io” |
    |||
1. 配置其余注册表设置，然后选择“审阅 + 创建”。

  ![通过专用终结点创建注册表](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>创建专用终结点 - 现有注册表

1. 在门户中，导航到容器注册表。
1. 在“设置”下选择“网络” 。
1. 在“专用终结点”选项卡上，选择“+ 专用终结点”。
1. 在“基本信息”中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组名称或创建一个新组。|
    | **实例详细信息** |  |
    | 名称 | 输入名称。 |
    |区域|选择区域。|
    |||
5. 在完成时选择“下一步:资源”。
6. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择“Microsoft.ContainerRegistry/registries”。 |
    | 资源 |选择注册表的名称|
    |目标子资源 |选择“注册表”|
    |||
7. 在完成时选择“下一步:配置”。
8. 输入或选择信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**联网**| |
    | 虚拟网络| 选择要在其中部署虚拟机的虚拟网络，例如 myDockerVMVNET。 |
    | 子网 | 选择要在其中部署虚拟机的子网，例如 myDockerVMSubnet。 |
    |专用 DNS 集成||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择“(新) privatelink.azurecr.io” |
    |||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
2. 看到“验证通过”消息时，选择“创建” 。

创建专用终结点之后，专用区域中的 DNS 设置会显示在门户中的“专用终结点”页上：

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“专用终结点”选项卡上，选择创建的专用这节点。
1. 在“概述”页上，审阅链接设置和自定义 DNS 设置。

  ![站项目 DNS 设置](./media/container-registry-private-link/private-endpoint-overview.png)

专用链接现已配置，可供使用。

## <a name="disable-public-access"></a>禁用公共访问

在许多情况下，禁用从公用网络进行的注册表访问。 此配置会阻止虚拟网络外部的客户端访问注册表终结点。 

### <a name="disable-public-access---cli"></a>禁用公共访问 - CLI

若要使用 Azure CLI 禁用公共访问，请运行 [az acr update][az-acr-update]，并将 `--public-network-enabled` 设置为 `false`。 

> [!NOTE]
> `public-network-enabled` 参数需要 Azure CLI 2.6.0 或更高版本。 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>禁用公共访问 - 门户

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“公共访问”选项卡上的“允许公用网络访问”中，选择“禁用”  。 再选择“保存”。

## <a name="validate-private-link-connection"></a>验证专用链接连接

应该验证专用终结点的子网中的资源是否可以通过专用 IP 地址连接到注册表，以及它们是否具有正确的专用 DNS 区域集成。

若要验证专用链接连接，请通过 SSH 连接到虚拟网络中设置的虚拟机。

运行 `nslookup` 命令以通过专用链接解析注册表的 IP 地址：

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

示例输出演示子网的地址空间中的注册表 IP 地址：

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

将此结果与公共终结点上相同注册表的 `nslookup` 输出中的公共 IP 地址进行比较：

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>针对专用链接的注册表操作

此外，验证是否可以从子网中的虚拟机执行注册表操作。 建立与虚拟机的 SSH 连接，并运行 [az acr login][az-acr-login] 以登录注册表。 根据 VM 配置，可能需要将 `sudo` 作为以下命令的前缀。

```bash
az acr login --name $REGISTRY_NAME
```

执行注册表操作（如 `docker pull`），以从注册表拉取示例映像。 将 `hello-world:v1` 替换为适用于注册表的映像和标记，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像拉取到 VM。

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

使用 Azure 门户或使用 [az acr private-endpoint-connection][az-acr-private-endpoint-connection] 命令组中的命令管理注册表的专用终结点连接。 操作包括批准、删除、列出、拒绝注册表专用终结点连接或显示其详细信息。

例如，若要列出注册表的专用终结点连接，请运行 [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] 命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

使用本文中的步骤设置专用终结点连接时，注册表会自动接受来自对注册表拥有 RBAC 权限的客户端和服务的连接。 可以设置终结点以要求手动批准连接。 有关如何批准和拒绝专用终结点连接的信息，请参阅[管理专用终结点连接](../private-link/manage-private-endpoint.md)。

## <a name="add-zone-records-for-replicas"></a>为副本添加区域记录

如本文中所示，将专用终结点连接添加到注册表时，将在 `privatelink.azurecr.io` 区域中为注册表进行[复制](container-registry-geo-replication.md)的区域中的注册表及其数据终结点创建 DNS 记录。 

如果以后添加新副本，则需要为该区域中的数据终结点手动添加新的区域记录。 例如，如果在 northeurope 位置创建 myregistry 的副本，则会为 `myregistry.northeurope.data.azurecr.io` 添加区域记录。 有关步骤，请参阅本文中的[在专用区域中创建 DNS 记录](#create-dns-records-in-the-private-zone)。

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要这些资源，则可以选择使用单个 [az group delete](/cli/azure/group) 命令删除资源：

```azurecli
az group delete --name $RESOURCE_GROUP
```

若要在门户中清理资源，请导航到资源组。 加载资源组后，单击“删除资源组”以删除该资源组和其中存储的资源。

## <a name="next-steps"></a>后续步骤

* 若要了解有关专用链接的更多信息，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)文档。
* 如需设置从客户端防火墙后访问注册表的规则，请参阅[配置从防火墙后访问 Azure 容器注册表的规则](container-registry-firewall-access-rules.md)。

<!-- LINKS - external -->
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
