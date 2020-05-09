---
title: 设置专用链接
description: 在容器注册表上设置专用终结点，并通过本地虚拟网络中的专用链接启用访问
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982411"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>配置 Azure 容器注册表的 Azure 专用链接 

使用[Azure Private Link](../private-link/private-link-overview.md)将虚拟网络专用 IP 地址分配到注册表终结点，以限制对注册表的访问。 虚拟网络上的客户端和注册表之间的网络流量会遍历 Microsoft 主干网络上的虚拟网络和专用链接，从而消除了公共 internet 的泄露。

你可以为专用终结点[配置 DNS 设置](../private-link/private-endpoint-overview.md#dns-configuration)，以使这些设置解析为注册表分配的专用 IP 地址。 使用 DNS 配置，网络中的客户端和服务可以继续访问注册表的完全限定域名（如*myregistry.azurecr.io*）中的注册表。

此功能在“高级”容器注册表服务层级中可用。  有关注册表服务层和限制的信息，请参阅[Azure 容器注册表层](container-registry-skus.md)。

## <a name="things-to-know"></a>使用须知

* 目前，使用 Azure 安全中心的映像扫描在配置有专用终结点的注册表中不可用。

## <a name="prerequisites"></a>必备条件

* 若要使用本文中 Azure CLI 的步骤，建议 Azure CLI 版本2.2.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或在[Azure Cloud Shell](../cloud-shell/quickstart.md)中运行。
* 如果还没有容器注册表，请创建一个（需要高级层），然后`hello-world`从 Docker Hub[导入](container-registry-import-images.md)示例映像。 例如，使用[Azure 门户][quickstart-portal]或[Azure CLI][quickstart-cli]创建注册表。
* 若要使用其他 Azure 订阅中的专用链接配置注册表访问，需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 示例使用以下环境变量。 替换适用于你的环境的值。 所有示例都是通过 Bash shell 进行格式设置的：

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>设置专用链接-CLI

### <a name="get-network-and-subnet-names"></a>获取网络和子网名称

如果尚未安装，则需要虚拟网络和子网的名称才能设置专用链接。 在此示例中，将为 VM 和注册表的专用终结点使用相同的子网。 但是，在许多情况下，会在单独的子网中设置终结点。 

创建 VM 时，Azure 默认情况下会在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为*myDockerVM*，则默认虚拟网络名称为*myDockerVMVNET*，子网名为*myDockerVMSubnet*。 通过运行[az network vnet list][az-network-vnet-list]命令，在环境变量中设置这些值：

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

### <a name="disable-network-policies-in-subnet"></a>禁用子网中的网络策略

[禁用](../private-link/disable-private-endpoint-network-policy.md)专用终结点的子网中的网络策略，例如网络安全组。 用[az network vnet subnet update][az-network-vnet-subnet-update]更新子网配置：

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

为专用 Azure 容器注册表域创建专用 DNS 区域。 在后续步骤中，你将在此 DNS 区域中为你的注册表域创建 DNS 记录。

若要使用专用区域替代 Azure 容器注册表的默认 DNS 解析，区域必须命名为**privatelink.azurecr.io**。 运行以下[az network private-dns zone create][az-network-private-dns-zone-create]命令以创建专用区域：

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>创建关联链接

运行[az network private-dns link vnet create][az-network-private-dns-link-vnet-create]以将专用区域与虚拟网络关联。 此示例将创建一个名为*myDNSLink*的链接。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>创建专用注册表终结点

在本部分中，将在虚拟网络中创建注册表的专用终结点。 首先，获取注册表的资源 ID：

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

运行[az network private-endpoint create][az-network-private-endpoint-create]命令，以创建注册表的专用终结点。

以下示例创建终结点*myPrivateEndpoint*和服务连接*myconnection.rdp*。 若要为终结点指定容器注册表资源，请`--group-ids registry`传递：

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

运行[az network private-endpoint show][az-network-private-endpoint-show]以查询网络接口 ID 的终结点：

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

在此示例中，与网络接口关联的是容器注册表的两个专用 IP 地址：一个用于注册表本身，另一个用于注册表的数据终结点。 以下[az resource show][az-resource-show]命令获取容器注册表和注册表数据终结点的专用 IP 地址：

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
> 如果你的注册表是[异地复制](container-registry-geo-replication.md)的，请查询每个注册表副本的其他数据终结点。

### <a name="create-dns-records-in-the-private-zone"></a>在专用区域中创建 DNS 记录

以下命令在专用区域中为注册表终结点及其数据终结点创建 DNS 记录。 例如，如果在*westeurope*区域中有一个名为*myregistry*的注册表，则终结点名称`myregistry.azurecr.io`为`myregistry.westeurope.data.azurecr.io`和。 

> [!NOTE]
> 如果你的注册表是[异地复制](container-registry-geo-replication.md)的，请为每个副本的数据终结点 IP 创建其他 DNS 记录。

第一次运行[az network private-dns 记录-设置 create][az-network-private-dns-record-set-a-create]以为注册表终结点和数据终结点创建空的记录集：

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

运行[az network private-dns 记录-设置添加记录][az-network-private-dns-record-set-a-add-record]命令，为注册表终结点和数据终结点创建 a 记录：

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

现在，专用链接已配置好，可供使用。

## <a name="set-up-private-link---portal"></a>设置专用链接-门户

在创建注册表时设置专用链接，或向现有注册表添加专用链接。 以下步骤假设已将虚拟网络和子网设置为使用 VM 进行测试。 你还可以[创建新的虚拟网络和子网](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint---new-registry"></a>创建专用终结点-新建注册表

1. 在门户中创建注册表时，请在 "**基本**信息" 选项卡上的 " **SKU**" 中选择 "**高级**"。
1. 选择“网络”选项卡。 
1. 在 "**网络连接**" 中，选择 "**专用终结点** > **+ 添加**"。
1. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组的名称或创建一个新组。|
    | 名称 | 输入唯一名称。 |
    | 子资源 |选择**注册表**|
    | **联网** | |
    | 虚拟网络| 选择要在其中部署虚拟机的虚拟网络，例如 " *myDockerVMVNET*"。 |
    | 子网 | 选择要在其中部署虚拟机的子网（如*myDockerVMSubnet* ）。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。  |
    |专用 DNS 区域 |选择 *（新） privatelink.azurecr.io* |
    |||
1. 配置剩余的注册表设置，然后选择 "**查看 + 创建**"。

  ![创建具有专用终结点的注册表](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>创建专用终结点-现有注册表

1. 在门户中，导航到容器注册表。
1. 在 "**设置**" 下，选择 "**网络**"。
1. 在 "**专用终结点**" 选项卡上，选择 " **+ 专用终结点**"。
1. 在 "**基本**信息" 选项卡中，输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组的名称或创建一个新组。|
    | **实例详细信息** |  |
    | 名称 | 输入名称。 |
    |区域|选择区域。|
    |||
5. 在完成时选择“下一步:  资源”。
6. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择“连接到我的目录中的 Azure 资源”。 |
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择**microsoft.containerregistry/注册表**。 |
    | 资源 |选择注册表的名称|
    |目标子资源 |选择**注册表**|
    |||
7. 在完成时选择“下一步:  配置”。
8. 输入或选择以下信息：

    | 设置 | Value |
    | ------- | ----- |
    |**联网**| |
    | 虚拟网络| 选择要在其中部署虚拟机的虚拟网络，例如 " *myDockerVMVNET*"。 |
    | 子网 | 选择要在其中部署虚拟机的子网（如*myDockerVMSubnet* ）。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。  |
    |专用 DNS 区域 |选择 *（新） privatelink.azurecr.io* |
    |||

1. 选择“查看 + 创建”  。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置  。 
2. 看到“验证通过”消息时，选择“创建”   。

创建专用终结点后，专用区域中的 DNS 设置会显示在门户中的 "**专用终结点**" 页上：

1. 在门户中，导航到容器注册表，选择 "**设置" > 网络**"。
1. 在 "**专用终结点**" 选项卡上，选择你创建的专用终结点。
1. 在 "**概述**" 页上，查看 "链接设置" 和 "自定义 DNS 设置"。

  ![终结点 DNS 设置](./media/container-registry-private-link/private-endpoint-overview.png)

你的私有链接现已配置，可以使用。

## <a name="disable-public-access"></a>禁用公共访问

在许多情况下，禁用从公共网络进行的注册表访问。 此配置会阻止虚拟网络外部的客户端到达注册表终结点。 使用门户禁用公共访问：

1. 在门户中，导航到容器注册表，选择 "**设置" > 网络**"。
1. 在 "**公共访问**" 选项卡上的 "**允许公共访问**" 中，选择 "**禁用**"。 再选择“保存”  。

## <a name="validate-private-link-connection"></a>验证专用链接连接

应该验证专用终结点的子网中的资源是否通过专用 IP 地址连接到注册表，并且具有正确的专用 DNS 区域集成。

若要验证专用链接连接，请通过 SSH 连接到虚拟网络中设置的虚拟机。

运行`nslookup`命令，通过专用链接解析注册表的 IP 地址：

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

示例输出显示了子网的地址空间中的注册表 IP 地址：

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

将此结果与公共终结点上同`nslookup`一个注册表的输出中的公共 IP 地址进行比较：

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>针对专用链接的注册表操作

同时，验证是否可以从子网中的虚拟机执行注册表操作。 建立与虚拟机的 SSH 连接，并运行[az acr login][az-acr-login]登录到注册表。 根据 VM 配置，可能需要将以下命令作为前缀`sudo`。

```bash
az acr login --name $REGISTRY_NAME
```

执行注册表操作（如`docker pull` ）从注册表提取示例映像。 将`hello-world:v1`替换为适用于注册表的映像和标记，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像提取到 VM。

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

使用 Azure 门户或使用[az acr 专用终结点连接][az-acr-private-endpoint-connection]命令组中的命令管理注册表的专用终结点连接。 操作包括 "批准"、"删除"、"列出"、"拒绝" 或 "显示注册表的专用终结点连接" 的详细信息。

例如，若要列出注册表的专用终结点连接，请运行[az acr private-connection list][az-acr-private-endpoint-connection-list]命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

使用本文中的步骤设置专用终结点连接时，注册表会自动接受来自具有对注册表的 RBAC 权限的客户端和服务的连接。 可以设置终结点以要求手动批准连接。 有关如何批准和拒绝专用终结点连接的信息，请参阅[管理专用终结点连接](../private-link/manage-private-endpoint.md)。

## <a name="add-zone-records-for-replicas"></a>为副本添加区域记录

如本文中所示，当你将专用终结点连接添加到注册表时，将在`privatelink.azurecr.io` [复制](container-registry-geo-replication.md)注册表的区域中为注册表及其数据终结点创建区域中的 DNS 记录。 

如果以后添加新的副本，则需要在该区域中手动为数据终结点添加新的区域记录。 例如，如果在*northeurope*位置创建`myregistry.northeurope.data.azurecr.io` *myregistry*的副本，则为添加区域记录。 有关步骤，请参阅本文中[的在专用区域中创建 DNS 记录](#create-dns-records-in-the-private-zone)。

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要这些资源，可以选择使用单个[az group delete](/cli/azure/group)命令删除资源：

```azurecli
az group delete --name $RESOURCE_GROUP
```

若要在门户中清理资源，请导航到资源组。 加载资源组后，单击 "**删除资源组**" 以删除存储在其中的资源组和资源。

## <a name="next-steps"></a>后续步骤

* 若要详细了解专用链接，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)文档。
* 如果需要从客户端防火墙后面设置注册表访问规则，请参阅[配置规则以访问防火墙后面的 Azure 容器注册表](container-registry-firewall-access-rules.md)。

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
