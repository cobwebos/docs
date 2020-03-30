---
title: 容器组的静态 IP 地址
description: 在虚拟网络中创建容器组，并使用 Azure 应用程序网关将静态前端 IP 地址公开给容器化 Web 应用
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481783"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>公开容器组的静态 IP 地址

本文展示了使用 Azure[应用程序网关](../application-gateway/overview.md)公开[容器组的](container-instances-container-groups.md)静态公共 IP 地址的一种方法。 当您需要在 Azure 容器实例中运行的外部容器化应用的静态入口点时，请执行以下步骤。 

在本文中，您可以使用 Azure CLI 为此方案创建资源：

* Azure 虚拟网络
* 部署在[虚拟网络（预览）中的](container-instances-vnet.md)容器组，该容器组承载小型 Web 应用
* 具有公共前端 IP 地址的应用程序网关、在网关上托管网站的侦听器以及到后端容器组的路由

只要应用程序网关运行，并且容器组在网络委派的子网中公开稳定的专用 IP 地址，容器组即可在此公共 IP 地址访问。

> [!NOTE]
> Azure 根据网关的预配和可用时间以及它处理的数据量对应用程序网关收费。 请参阅[定价](https://azure.microsoft.com/pricing/details/application-gateway/)。

## <a name="create-virtual-network"></a>创建虚拟网络

在典型情况下，您可能已经拥有 Azure 虚拟网络。 如果没有，请创建一个，如以下示例命令所示。 虚拟网络需要应用程序网关和容器组的单独子网。

如果需要，请创建 Azure 资源组。 例如：

```azureci
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create][az-network-vnet-create] 命令创建虚拟网络。 此命令在网络中创建*myAG 子网*子网。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

使用[az 网络 vnet 子网创建][az-network-vnet-subnet-create]命令为后端容器组创建子网。 这里被命名为*myACISubnet。*

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

使用[az 网络公共 ip 创建][az-network-public-ip-create]命令创建静态公共 IP 资源。 在后面的步骤中，此地址配置为应用程序网关的前端。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>创建容器组

运行以下[az 容器创建][az-container-create]，在上一步骤中配置的虚拟网络中创建容器组。 

该组部署在*myACISubnet*子网中，其中包含一个名为*应用容器*的实例，该实例名为`aci-helloworld`应用容器，用于提取映像。 如文档中的其他文章所示，此映像打包了在 Node.js 中编写的小型 Web 应用，该应用提供静态 HTML 页面。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

成功部署后，将为虚拟网络中的容器组分配专用 IP 地址。 例如，运行以下[az 容器显示][az-container-show]命令以检索组的 IP 地址：

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

输出类似于：`10.0.2.4`。

要在后面的步骤中使用，将 IP 地址保存在环境变量中：

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>创建应用程序网关

按照[应用程序网关快速入门](../application-gateway/quick-create-cli.md)中的步骤在虚拟网络中创建应用程序网关。 以下[az 网络应用程序网关创建][az-network-application-gateway-create]命令创建具有公共前端 IP 地址和到后端容器组的路由的网关。 有关网关设置的详细信息，请参阅[应用程序网关文档](/azure/application-gateway/)。

```azurecli
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroup \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-protocol http \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$ACI_IP" 
```


Azure 最多可能需要 15 分钟才能创建应用程序网关。 

## <a name="test-public-ip-address"></a>测试公共 IP 地址
  
现在，您可以测试对在应用程序网关后面的容器组中运行的 Web 应用的访问。

运行[az 网络公共 ip 显示][az-network-public-ip-show]命令以检索网关的前端公共 IP 地址：

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

输出是公共 IP 地址，类似于： `52.142.18.133`。

要在成功配置时查看正在运行的 Web 应用，请导航到浏览器中的网关公共 IP 地址。 成功访问类似于：

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>后续步骤

* 请参阅[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)，以创建具有 WordPress 容器实例的容器组，作为应用程序网关后面的后端服务器。
* 您还可以使用用于 SSL 终止的证书配置应用程序网关。 请参阅[概述](../application-gateway/ssl-overview.md)和[教程](../application-gateway/create-ssl-portal.md)。
* 根据您的方案，请考虑将其他 Azure 负载平衡解决方案与 Azure 容器实例一起使用。 例如，使用[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)跨多个容器实例和多个区域分发流量。 请参阅此 [博客文章](https://aaronmsft.com/posts/azure-container-instances/)。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show