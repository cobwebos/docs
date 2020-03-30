---
title: 设置专用链接
description: 在容器注册表上设置专用终结点，并在本地虚拟网络中启用专用链接
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498914"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>为 Azure 容器注册表配置 Azure 专用链接 

可以为 Azure 容器注册表设置[专用终结点](../private-link/private-endpoint-overview.md)，以便 Azure 虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全地访问注册表。 专用终结点使用来自虚拟网络地址空间的 IP 地址进行注册表。 虚拟网络上的客户端和注册表之间的网络流量遍历虚拟网络和 Microsoft 骨干网络上的专用链路，从而消除了公共 Internet 的暴露。

您可以为专用终结点[配置 DNS 设置](../private-link/private-endpoint-overview.md#dns-configuration)，以便这些设置解析为注册表分配的专用 IP 地址。 使用 DNS 配置，网络中的客户端和服务可以继续访问注册表的完全限定域名，如*myregistry.azurecr.io*。

此功能在**高级**容器注册表服务层中可用。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 SKU](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能当前处于预览状态，并应用一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 目前，您无法在[异地复制的注册表](container-registry-geo-replication.md)上设置具有私有终结点的专用链接。 

## <a name="prerequisites"></a>先决条件

* 要使用本文中的 Azure CLI 步骤，建议使用 Azure CLI 版本 2.2.0 或更高版本。 如果需要安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或在[Azure 云外壳](../cloud-shell/quickstart.md)中运行。
* 如果还没有容器注册表，请创建一个（需要高级层）并推送示例映像（如`hello-world`Docker Hub）。 例如，使用[Azure 门户][quickstart-portal]或[Azure CLI][quickstart-cli]创建注册表。
* 如果要使用其他 Azure 订阅中的专用链接配置注册表访问，则需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 示例使用以下环境变量。 替换适合您的环境的值。 所有示例都为 Bash 外壳设置格式：

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用 Docker 的虚拟机

出于测试目的，请使用启用 Docker 的 Ubuntu VM 访问 Azure 容器注册表。 要对注册表使用 Azure 活动目录身份验证，请在 VM 上安装[Azure CLI。][azure-cli] 如果您已有 Azure 虚拟机，请跳过此创建步骤。

您可以对虚拟机和容器注册表使用相同的资源组。 此设置简化了清理，但不需要。 如果选择为虚拟机和虚拟网络创建单独的资源组，请运行[az 组创建][az-group-create]：

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

现在部署默认的 Ubuntu Azure 虚拟机，该虚拟机具有[az vm 创建][az-vm-create]。 下面的示例创建名为*myDockerVM*的 VM。

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
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

运行以下命令以在 Ubuntu VM 上安装 Docker：

```bash
sudo apt-get update
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

按照[使用 apt 安装 Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 中的步骤在 Ubuntu 虚拟机上安装 Azure CLI。 例如：

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

退出 SSH 连接。

## <a name="set-up-private-link---cli"></a>设置专用链路 - CLI

### <a name="get-network-and-subnet-names"></a>获取网络和子网名称

如果还没有虚拟网络和子网的名称来设置专用链接。 在此示例中，对 VM 和注册表的专用终结点使用相同的子网。 但是，在许多情况下，您将在单独的子网中设置终结点。 

创建 VM 时，Azure 默认情况下在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果您命名虚拟机*myDockerVM，* 则默认虚拟网络名称为*myDockerVMVNET，* 其子网名为*myDockerVMSubnet*。 通过运行[az 网络 vnet 列表][az-network-vnet-list]命令在环境变量中设置这些值：

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>禁用子网中的网络策略

[禁用网络策略](../private-link/disable-private-endpoint-network-policy.md)，如专用终结点的子网中的网络安全组。 使用 az 网络[vnet 子网更新子网][az-network-vnet-subnet-update]配置：

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

为私有 Azure 容器注册表域创建专用 DNS 区域。 在后面的步骤中，您将在此 DNS 区域内为注册表域创建 DNS 记录。

要使用专用区域覆盖 Azure 容器注册表的默认 DNS 解析，必须**privatelink.azurecr.io**命名该区域。 运行以下[az 网络专用 dns 区域创建][az-network-private-dns-zone-create]命令以创建专用区域：

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>创建关联链接

运行[az 网络专用 dns 链路 vnet 创建][az-network-private-dns-link-vnet-create]以将专用区域与虚拟网络关联。 本示例创建一个名为*myDNSLink*的链接。

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>创建专用注册表终结点

在本节中，在虚拟网络中创建注册表的专用终结点。 首先，获取注册表的资源 ID：

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

运行[az 网络专用终结点创建][az-network-private-endpoint-create]命令以创建注册表的私有终结点。

下面的示例创建终结点*myPrivate终结点*和服务连接*myConnect*。 要为终结点指定容器注册表资源，请传递`--group-ids registry`：

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>获取专用 IP 地址

运行[az 网络专用终结点显示][az-network-private-endpoint-show]以查询网络接口 ID 的终结点：

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

与网络接口关联的是容器注册表的两个专用 IP 地址：一个用于注册表本身，另一个用于注册表的数据终结点。 运行以下[az 资源显示][az-resource-show]命令以获取容器注册表和注册表的数据终结点的专用 IP 地址：

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>在专用区域中创建 DNS 记录

以下命令在注册表终结点及其数据终结点的专用区域中创建 DNS 记录。 例如，如果您在*西欧*区域有名为`myregistry.azurecr.io`*myregistry*的注册表，则终结点名称为 和`myregistry.westeurope.data.azurecr.io`。 

首先运行[az 网络专用 dns 记录集创建，][az-network-private-dns-record-set-a-create]以创建注册表终结点和数据终结点的空 A 记录集：

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

运行[az 网络专用 dns 记录集一个附加记录][az-network-private-dns-record-set-a-add-record]命令，为注册表终结点和数据终结点创建 A 记录：

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

专用链路现已配置完毕，可供使用。

## <a name="set-up-private-link---portal"></a>设置专用链接 - 门户

以下步骤假定您已经设置了虚拟网络和子网，并设置了 VM 进行测试。 您还可以[创建新的虚拟网络和子网](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint"></a>创建专用终结点

1. 在门户中，导航到容器注册表。
1. 在 **"设置"** 下，选择**专用终结点连接（预览）。**
1. 选择 **= 专用终结点**。
1. 在 **"基础知识"** 选项卡中，输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    | **项目详情** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组的名称或创建新组。|
    | **实例详细信息** |  |
    | “属性” | 输入唯一名称。 |
    |区域|选择区域。|
    |||
5. 选择 **"下一步"：资源**。
6. 输入或选择以下信息：

    | 设置 | “值” |
    | ------- | ----- |
    |连接方法  | 选择 **"连接到目录中的 Azure 资源**"。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择**微软.集装箱注册/注册**。 |
    | 资源 |选择注册表的名称|
    |目标子资源 |选择**注册表**|
    |||
7. 选择 **"下一步"：配置**。
8. 输入或选择信息：

    | 设置 | “值” |
    | ------- | ----- |
    |**网络连接**| |
    | 虚拟网络| 选择部署虚拟机的虚拟网络，例如*myDockerVMVNET*。 |
    | 子网 | 选择子网，例如部署虚拟机的*myDockerVM Subnet。* |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |选择 **“是”**。 |
    |专用 DNS 区域 |选择 *（新建）privatelink.azurecr.io* |
    |||

1. 选择“查看 + 创建”****。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置****。 
2. 当您看到**验证传递**的消息时，选择 **"创建**"。

创建专用终结点后，专用区域中的 DNS 设置将显示在终结点的 **"概述"** 页上。

![端点 DNS 设置](./media/container-registry-private-link/private-endpoint-overview.png)

您的专用链接现已配置完毕，可供使用。

## <a name="validate-private-link-connection"></a>验证专用链路连接

应验证专用终结点子网中的资源是否通过专用 IP 地址连接到注册表，并且具有正确的专用 DNS 区域集成。

要验证专用链路连接，SSH 到您在虚拟网络中设置的虚拟机。

运行命令`nslookup`通过专用链路解析注册表的 IP 地址：

```bash
nslookup $registryName.azurecr.io
```

示例输出显示子网的地址空间中的注册表的 IP 地址：

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

将此结果与公共终结点上同一注册表`nslookup`的输出中的公共 IP 地址进行比较：

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>通过专用链接的注册表操作

还要验证是否可以从子网中的虚拟机执行注册表操作。 使 SSH 连接到虚拟机，并运行[az acr 登录][az-acr-login]以登录到注册表。 根据 VM 配置，您可能需要使用 前缀以下命令`sudo`。

```bash
az acr login --name $registryName
```

执行注册表操作，例如`docker pull`从注册表中提取示例映像。 替换为`hello-world:v1`适合您的注册表的映像和标记，该名称以注册表登录服务器名称（所有小写）为预缀：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像拉到 VM。

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

使用 Azure 门户或使用[az acr 私有终结点连接][az-acr-private-endpoint-connection]命令组中的命令管理注册表的专用终结点连接。 操作包括批准、删除、列出、拒绝或显示注册表的专用终结点连接的详细信息。

例如，要列出注册表的专用终结点连接，运行[az acr 私有终结点连接列表][az-acr-private-endpoint-connection-list]命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

使用本文中的步骤设置专用终结点连接时，注册表会自动接受来自对注册表具有 RBAC 权限的客户端和服务的连接。 您可以设置终结点以需要手动批准连接。 有关如何批准和拒绝专用终结点连接的信息，请参阅[管理专用终结点连接](../private-link/manage-private-endpoint.md)。

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要它们，则可以选择使用单个[az 组删除](/cli/azure/group)命令删除资源：

```azurecli
az group delete --name $resourceGroup
```

要清理门户中的资源，请导航到资源组。 加载资源组后，单击"**删除资源组"** 以删除资源组和存储的资源。

## <a name="next-steps"></a>后续步骤

* 要了解有关私有链接的更多详细信息，请参阅[Azure 专用链接](../private-link/private-link-overview.md)文档。
* 私有链路的替代方法是设置网络访问规则以限制注册表访问。 要了解更多信息，请参阅[使用 Azure 虚拟网络或防火墙规则限制对 Azure 容器注册表的访问](container-registry-vnet.md)。

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
