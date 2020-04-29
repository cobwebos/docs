---
title: 容器组的静态 IP 地址
description: 在虚拟网络中创建容器组，并使用 Azure 应用程序网关向容器化 web 应用公开静态前端 IP 地址
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: 5c3a14f93af3ecc614dc296f0a4d2815d7a64a66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481783"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>公开容器组的静态 IP 地址

本文介绍了使用 Azure[应用程序网关](../application-gateway/overview.md)公开[容器组](container-instances-container-groups.md)的静态公共 IP 地址的一种方法。 如果需要在 Azure 容器实例中运行面向外部容器的应用的静态入口点，请执行以下步骤。 

本文使用 Azure CLI 为此方案创建资源：

* Azure 虚拟网络
* 部署[在虚拟网络（预览版）中](container-instances-vnet.md)的容器组，托管小型 web 应用
* 具有公用前端 IP 地址的应用程序网关、用于在网关上托管网站的侦听程序以及到后端容器组的路由

只要应用程序网关运行并且容器组公开网络的委派子网中的稳定专用 IP 地址，就可以在此公共 IP 地址访问该容器组。

> [!NOTE]
> Azure 会根据预配和提供网关的时间量以及它处理的数据量对应用程序网关进行收费。 请参阅[定价](https://azure.microsoft.com/pricing/details/application-gateway/)。

## <a name="create-virtual-network"></a>创建虚拟网络

在典型情况下，你可能已经有了一个 Azure 虚拟网络。 如果没有，请创建一个，如以下示例命令所示。 虚拟网络需要为应用程序网关和容器组提供单独的子网。

如果需要，请创建一个 Azure 资源组。 例如：

```azureci
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create][az-network-vnet-create] 命令创建虚拟网络。 此命令在网络中创建*myAGSubnet*子网。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

使用[az network vnet subnet create][az-network-vnet-subnet-create]命令为后端容器组创建子网。 此处命名为*myACISubnet*。

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

使用[az network 公共 ip create][az-network-public-ip-create]命令创建静态公共 ip 资源。 在后面的步骤中，此地址已配置为应用程序网关的前端。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>创建容器组

运行以下[az container create][az-container-create]以在上一步中配置的虚拟网络中创建容器组。 

组部署在*myACISubnet*子网中，包含一个名为*appcontainer*的实例，该实例`aci-helloworld`用于提取映像。 如本文档中的其他文章所示，此图像打包了用 node.js 编写的小型 web 应用，该应用提供了一个用于静态 HTML 页面的。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

成功部署后，会在虚拟网络中为容器组分配一个专用 IP 地址。 例如，运行以下[az container show][az-container-show]命令以检索组的 IP 地址：

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

输出类似于：`10.0.2.4`。

若要在后面的步骤中使用，请将 IP 地址保存在环境变量中：

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>创建应用程序网关

按照[应用程序网关快速入门](../application-gateway/quick-create-cli.md)中的步骤，在虚拟网络中创建应用程序网关。 下面的[az 网络应用程序网关 create][az-network-application-gateway-create]命令创建一个网关，该网关具有公用前端 IP 地址和后端容器组的路由。 有关网关设置的详细信息，请参阅[应用程序网关文档](/azure/application-gateway/)。

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


Azure 最多可能需要15分钟才能创建应用程序网关。 

## <a name="test-public-ip-address"></a>测试公共 IP 地址
  
现在，你可以测试对应用程序网关后面的容器组中运行的 web 应用的访问。

运行[az network public-lip show][az-network-public-ip-show]命令以检索网关的前端公共 ip 地址：

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

输出是一个公共 IP 地址，类似于： `52.142.18.133`。

若要在成功配置后查看正在运行的 web 应用，请在浏览器中导航到该网关的公共 IP 地址。 成功访问类似于：

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>后续步骤

* 请参阅[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)，以创建包含 WordPress 容器实例的容器组，作为应用程序网关后端服务器。
* 你还可以使用用于 SSL 终止的证书配置应用程序网关。 请参阅[概述](../application-gateway/ssl-overview.md)和[教程](../application-gateway/create-ssl-portal.md)。
* 根据你的方案，请考虑将其他 Azure 负载平衡解决方案用于 Azure 容器实例。 例如，使用[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)将流量分布到多个容器实例和多个区域。 请参阅此 [博客文章](https://aaronmsft.com/posts/azure-container-instances/)。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show