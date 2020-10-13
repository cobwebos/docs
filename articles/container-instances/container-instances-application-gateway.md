---
title: 容器组的静态 IP 地址
description: 在虚拟网络中创建容器组，并使用 Azure 应用程序网关向容器化 Web 应用公开静态前端 IP 地址
ms.topic: article
ms.date: 03/16/2020
ms.openlocfilehash: bc128da0f4c2e92af98781cef45f48f9e8aeab31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260784"
---
# <a name="expose-a-static-ip-address-for-a-container-group"></a>公开容器组的静态 IP 地址

本文介绍一种使用 Azure [应用程序网关](../application-gateway/overview.md)公开[容器组](container-instances-container-groups.md)的静态公共 IP 地址的方法。 如果需要在 Azure 容器实例中运行的某个面向外部的容器化应用的静态入口点，请按这些步骤操作。 

本文使用 Azure CLI 为此方案创建资源：

* Azure 虚拟网络
* 部署在[虚拟网络](container-instances-vnet.md)中的一个容器组，其中托管了一个小型 Web 应用
* 一个使用公共前端 IP 地址的应用程序网关、一个用于在网关上托管网站的侦听器，以及一个用于连接后端容器组的路由

只要应用程序网关运行，并且容器组公开网络委托子网中的某个稳定专用 IP 地址，就可以通过此公共 IP 地址访问该容器组。

> [!NOTE]
> Azure 根据为网关预配的及可用时间的时间长短及其处理的数据量对应用程序网关进行收费。 请参阅[定价](https://azure.microsoft.com/pricing/details/application-gateway/)。

## <a name="create-virtual-network"></a>创建虚拟网络

在典型案例中，你可能已有一个 Azure 虚拟网络。 如果没有，请按以下示例命令所示创建一个。 该虚拟网络需要为应用程序网关和容器组提供单独的子网。

如果需要，请创建一个 Azure 资源组。 例如：

```azureci
az group create --name myResourceGroup --location eastus
```

使用 [az network vnet create][az-network-vnet-create] 命令创建虚拟网络。 此命令在网络中创建 *myAGSubnet* 子网。

```azurecli
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroup \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
```

使用 [az network vnet subnet create][az-network-vnet-subnet-create] 命令为后端容器组创建子网。 该子网在这里命名为 *myACISubnet*。

```azurecli
az network vnet subnet create \
  --name myACISubnet \
  --resource-group myResourceGroup \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
```

使用 [az network public-ip create][az-network-public-ip-create] 命令创建静态公共 IP 资源。 稍后的某个步骤会将此地址配置为应用程序网关的前端。

```azurecli
az network public-ip create \
  --resource-group myResourceGroup \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-container-group"></a>创建容器组

运行以下 [az container create][az-container-create] 命令，在上一步配置的虚拟网络中创建一个容器组。 

该组会部署到 *myACISubnet* 子网中，包含用于拉取 `aci-helloworld` 映像的名为 *appcontainer* 的单个实例。 如本文档中的其他文章所述，此映像会打包一个以 Node.js 编写的、可提供静态 HTML 页面的小型 Web 应用。 

```azurecli
az container create \
  --name appcontainer \
  --resource-group myResourceGroup \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet myVNet \
  --subnet myACISubnet
```

成功部署后，将为该容器组分配虚拟网络中的某个专用 IP 地址。 例如，运行以下 [az container show][az-container-show] 命令可以检索该组的 IP 地址：

```azurecli
az container show \
  --name appcontainer --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv
```

输出类似于：`10.0.2.4`。

请将该 IP 地址保存到环境变量中，以便在稍后的步骤中使用：

```azurecli
ACI_IP=$(az container show \
  --name appcontainer \
  --resource-group myResourceGroup \
  --query ipAddress.ip --output tsv)
```

## <a name="create-application-gateway"></a>创建应用程序网关

按照[应用程序网关快速入门](../application-gateway/quick-create-cli.md)中的步骤，在虚拟网络中创建应用程序网关。 以下 [az network application-gateway create][az-network-application-gateway-create] 命令创建一个网关，该网关使用某个公共前端 IP 地址以及用于连接后端容器组的路由。 有关网关设置的详细信息，请参阅[应用程序网关文档](../application-gateway/index.yml)。

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


Azure 最长可能需要花费 15 分钟时间来创建应用程序网关。 

## <a name="test-public-ip-address"></a>测试公共 IP 地址
  
现在，可以测试性地访问在应用程序网关后面的容器组中运行的 Web 应用。

运行 [az network public-ip show][az-network-public-ip-show] 命令以检索网关的前端公共 IP 地址：

```azurecli
az network public-ip show \
--resource-group myresourcegroup \
--name myAGPublicIPAddress \
--query [ipAddress] \
--output tsv
```

输出是一个公共 IP 地址，类似于 `52.142.18.133`。

若要在成功配置后查看正在运行的 Web 应用，请在浏览器中导航到该网关的公共 IP 地址。 成功访问后，会显示如下所示的界面：

![浏览器屏幕截图，显示应用程序在 Azure 容器实例中运行](./media/container-instances-application-gateway/aci-app-app-gateway.png)

## <a name="next-steps"></a>后续步骤

* 参阅[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)，了解如何在应用程序网关后面创建一个使用 WordPress 容器实例作为后端服务器的容器组。
* 还可以使用用于 SSL 终止的证书来配置应用程序网关。 请参阅[概述](../application-gateway/ssl-overview.md)和[教程](../application-gateway/create-ssl-portal.md)。
* 根据你的方案，请考虑将其他 Azure 负载均衡解决方案与 Azure 容器实例配合使用。 例如，使用 [Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)在多个容器实例和多个区域之间分配流量。 请参阅此 [博客文章](https://aaronmsft.com/posts/azure-container-instances/)。

[az-network-vnet-create]:  /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-show]: /cli/azure/network/public-ip#az-network-public-ip-show
[az-network-application-gateway-create]: /cli/azure/network/application-gateway#az-network-application-gateway-create
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
