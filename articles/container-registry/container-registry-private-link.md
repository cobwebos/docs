---
title: 设置专用链接
description: 在容器注册表上设置专用终结点，并在本地虚拟网络中启用专用链接
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 57c2a59ad8b16c39c7c577173feae68dcb263277
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203349"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>配置 Azure 容器注册表的 Azure 专用链接 

你可以为 Azure 容器注册表设置[专用终结点](../private-link/private-endpoint-overview.md)，以便在 azure 虚拟网络上的客户端通过[专用链接](../private-link/private-link-overview.md)安全访问注册表。 专用终结点使用来自你的注册表的虚拟网络地址空间中的 IP 地址。 虚拟网络上的客户端和注册表之间的网络流量会遍历 Microsoft 主干网络上的虚拟网络和专用链接，从而消除了公共 internet 的泄露。

你可以为专用终结点[配置 DNS 设置](../private-link/private-endpoint-overview.md#dns-configuration)，以使这些设置解析为注册表分配的专用 IP 地址。 使用 DNS 配置，网络中的客户端和服务可以继续访问注册表的完全限定域名（如*myregistry.azurecr.io*）中的注册表。

此功能在**高级**容器注册表服务层中提供。 有关注册表服务层和限制的信息，请参阅[Azure 容器注册表 sku](container-registry-skus.md)。

> [!IMPORTANT]
> 此功能目前处于预览阶段，但有一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="preview-limitations"></a>预览版限制

* 目前，你不能使用[异地复制的注册表](container-registry-geo-replication.md)上的专用终结点设置专用链接。 

## <a name="prerequisites"></a>必备条件

* 若要使用本文中 Azure CLI 的步骤，建议 Azure CLI 版本2.2.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或在[Azure Cloud Shell](../cloud-shell/quickstart.md)中运行。
* 如果还没有容器注册表，请创建一个（需要高级层），并推送示例映像，例如从 Docker 中心 `hello-world`。 例如，使用[Azure 门户][quickstart-portal]或[Azure CLI][quickstart-cli]创建注册表。
* 如果要使用其他 Azure 订阅中的专用链接来配置注册表访问，则需要在该订阅中注册 Azure 容器注册表的资源提供程序。 例如：

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

本文中的 Azure CLI 示例使用以下环境变量。 替换适用于你的环境的值。 所有示例都是通过 Bash shell 进行格式设置的：

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>创建启用了 Docker 的虚拟机

出于测试目的，请使用启用了 Docker 的 Ubuntu VM 来访问 Azure 容器注册表。 若要对注册表使用 Azure Active Directory 身份验证，请在 VM 上安装[Azure CLI][azure-cli] 。 如果已有 Azure 虚拟机，请跳过此创建步骤。

你可以对虚拟机和容器注册表使用同一资源组。 此设置简化了在结束时的清理，但并不是必需的。 如果选择为虚拟机和虚拟网络创建单独的资源组，请运行[az group create][az-group-create]：

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

现在使用[az vm create][az-vm-create]部署默认 Ubuntu Azure 虚拟机。 以下示例创建名为*myDockerVM*的 VM。

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

运行以下命令，在 Ubuntu VM 上安装 Docker：

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

## <a name="set-up-private-link---cli"></a>设置专用链接-CLI

### <a name="get-network-and-subnet-names"></a>获取网络和子网名称

如果尚未安装，则需要虚拟网络和子网的名称才能设置专用链接。 在此示例中，将为 VM 和注册表的专用终结点使用相同的子网。 但是，在许多情况下，会在单独的子网中设置终结点。 

创建 VM 时，Azure 默认情况下会在同一资源组中创建虚拟网络。 虚拟网络的名称基于虚拟机的名称。 例如，如果将虚拟机命名为*myDockerVM*，则默认虚拟网络名称为*myDockerVMVNET*，子网名为*myDockerVMSubnet*。 通过运行[az network vnet list][az-network-vnet-list]命令，在环境变量中设置这些值：

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

[禁用](../private-link/disable-private-endpoint-network-policy.md)专用终结点的子网中的网络策略，例如网络安全组。 用[az network vnet subnet update][az-network-vnet-subnet-update]更新子网配置：

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

为私钥 Azure 容器注册表域创建专用 DNS 区域。 在后续步骤中，你将在此 DNS 区域内为你的注册表域创建 DNS 记录。

若要使用专用区域替代 Azure 容器注册表的默认 DNS 解析，区域必须命名为**privatelink.azurecr.io**。 运行以下[az network private-dns zone create][az-network-private-dns-zone-create]命令以创建专用区域：

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>创建关联链接

运行[az network private-dns link vnet create][az-network-private-dns-link-vnet-create]以将专用区域与虚拟网络关联。 此示例将创建一个名为*myDNSLink*的链接。

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>创建专用注册表终结点

在本部分中，将在虚拟网络中创建注册表的专用终结点。 首先，获取注册表的资源 ID：

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

运行[az network private-endpoint create][az-network-private-endpoint-create]命令，以创建注册表的专用终结点。

以下示例创建终结点*myPrivateEndpoint*和服务连接*myconnection.rdp*。 若要为终结点指定容器注册表资源，请通过 `--group-ids registry`：

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

运行[az network private-endpoint show][az-network-private-endpoint-show]以查询网络接口 ID 的终结点：

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

与网络接口关联的是容器注册表的两个专用 IP 地址：一个用于注册表本身，另一个用于注册表的数据终结点。 运行以下[az resource show][az-resource-show]命令以获取容器注册表和注册表数据终结点的专用 IP 地址：

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

以下命令在专用区域中为注册表终结点及其数据终结点创建 DNS 记录。 例如，如果在*westeurope*区域中有一个名为*myregistry*的注册表，则终结点名称将 `myregistry.azurecr.io` 并 `myregistry.westeurope.data.azurecr.io`。 

第一次运行[az network private-dns 记录-设置 create][az-network-private-dns-record-set-a-create]以为注册表终结点和数据终结点创建空的记录集：

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

运行[az network private-dns 记录-设置添加记录][az-network-private-dns-record-set-a-add-record]命令，为注册表终结点和数据终结点创建 a 记录：

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

现在，专用链接已配置好，可供使用。

## <a name="set-up-private-link---portal"></a>设置专用链接-门户

以下步骤假设已将虚拟网络和子网设置为使用 VM 进行测试。 你还可以[创建新的虚拟网络和子网](../virtual-network/quick-create-portal.md)。

### <a name="create-a-private-endpoint"></a>创建专用终结点

1. 在门户中，导航到容器注册表。
1. 在 "**设置**" 下，选择 "**专用终结点连接（预览版）** "。
1. 选择 " **+ 专用终结点**"。
1. 在 "**基本**信息" 选项卡中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组的名称或创建一个新组。|
    | **实例详细信息** |  |
    | 名称 | 输入唯一名称。 |
    |区域|选择区域。|
    |||
5. 选择 "**下一步：资源**"。
6. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 选择 **"连接到我的目录中的 Azure 资源"** 。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择**microsoft.containerregistry/注册表**。 |
    | 资源 |选择注册表的名称|
    |目标 subresource |选择**注册表**|
    |||
7. 选择**下一步：配置**。
8. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**网络**| |
    | 虚拟网络| 选择要在其中部署虚拟机的虚拟网络，例如 " *myDockerVMVNET*"。 |
    | 子网 | 选择要在其中部署虚拟机的子网（如*myDockerVMSubnet* ）。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择 *（新） privatelink.azurecr.io* |
    |||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
2. 看到“验证通过”消息时，选择“创建”。

创建专用终结点后，专用区域中的 DNS 设置将显示在终结点的 "**概述**" 页上。

![终结点 DNS 设置](./media/container-registry-private-link/private-endpoint-overview.png)

你的私有链接现已配置，可以使用。

## <a name="validate-private-link-connection"></a>验证专用链接连接

应该验证专用终结点的子网中的资源是否通过专用 IP 地址连接到注册表，并且具有正确的专用 DNS 区域集成。

若要验证专用链接连接，请通过 SSH 连接到虚拟网络中设置的虚拟机。

运行 `nslookup` 命令，通过专用链接解析注册表的 IP 地址：

```bash
nslookup $registryName.azurecr.io
```

示例输出显示了子网的地址空间中的注册表 IP 地址：

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

同时，验证是否可以从子网中的虚拟机执行注册表操作。 建立与虚拟机的 SSH 连接，并运行[az acr login][az-acr-login]登录到注册表。 根据你的 VM 配置，你可能需要将以下命令作为 `sudo`的前缀。

```bash
az acr login --name $registryName
```

执行注册表操作（如 `docker pull`）从注册表提取示例映像。 将 `hello-world:v1` 替换为适用于注册表的映像和标记，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像提取到 VM。

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

使用 Azure 门户或使用[az acr 专用终结点连接][az-acr-private-endpoint-connection]命令组中的命令管理注册表的专用终结点连接。 操作包括 "批准"、"删除"、"列出"、"拒绝" 或 "显示注册表的专用终结点连接" 的详细信息。

例如，若要列出注册表的专用终结点连接，请运行[az acr private-connection list][az-acr-private-endpoint-connection-list]命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

使用本文中的步骤设置专用终结点连接时，注册表会自动接受来自具有对注册表的 RBAC 权限的客户端和服务的连接。 可以设置终结点以要求手动批准连接。 有关如何批准和拒绝专用终结点连接的信息，请参阅[管理专用终结点连接](../private-link/manage-private-endpoint.md)。

## <a name="clean-up-resources"></a>清理资源

如果在同一资源组中创建了所有 Azure 资源，并且不再需要这些资源，可以选择使用单个[az group delete](/cli/azure/group)命令删除资源：

```azurecli
az group delete --name $resourceGroup
```

若要在门户中清理资源，请导航到资源组。 加载资源组后，单击 "**删除资源组**" 以删除存储在其中的资源组和资源。

## <a name="next-steps"></a>后续步骤

* 若要了解有关专用链接的详细信息，请参阅[Azure 专用链接](../private-link/private-link-overview.md)文档。
* 专用链接的替代方法是设置网络访问规则以限制注册表访问。 若要了解详细信息，请参阅[使用 azure 虚拟网络或防火墙规则限制对 azure 容器注册表的访问权限](container-registry-vnet.md)。

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
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
