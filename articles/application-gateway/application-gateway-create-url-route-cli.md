---
title: 使用基于 URL 路径的路由规则创建应用程序网关 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 为应用程序网关和虚拟机规模集创建基于 URL 路径的路由规则。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: e811527b8bc0b816f09f055f776fe8bd65bed07d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069397"
---
# <a name="create-an-application-gateway-with-url-path-based-routing-rules-using-the-azure-cli"></a>通过 Azure CLI 使用基于 URL 路径的路由规则创建应用程序网关

创建[应用程序网关](application-gateway-introduction.md)时可以使用 Azure CLI 配置[基于 URL 路径的路由规则](application-gateway-url-route-overview.md)。 在本教程中，使用[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)创建后端池。 然后创建路由规则，以确保 Web 流量到达池中的相应服务器。

在本文中，学习如何：

> [!div class="checklist"]
> * 设置网络
> * 使用 URL 映射创建应用程序网关
> * 使用后端池创建虚拟机规模集

![URL 路由示例](./media/application-gateway-create-url-route-cli/scenario.png)

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group#create) 创建资源组。

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

使用 [az network vnet create](/cli/azure/network/vnet#az_net) 创建名为 *myVNet* 的虚拟网络和名为 *myAGSubnet* 的子网。 然后，可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 添加后端服务器所需的名为 *myBackendSubnet* 的子网。 使用 [az network public-ip create](/cli/azure/public-ip#az_network_public_ip_create) 创建名为 *myAGPublicIPAddress* 的公共 IP 地址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway-with-url-map"></a>使用 URL 映射创建应用程序网关

可以使用 [az network application-gateway create](/cli/azure/application-gateway#create) 创建名为 *myAppGateway* 的应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将应用程序网关分配给之前创建的 *myAGSubnet* 和 *myAGPublicIPAddress*。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到它的这些新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。


### <a name="add-image-and-video-backend-pools-and-port"></a>添加映像及视频后端池和端口

可以使用 [az network application-gateway address-pool create](/cli/azure/application-gateway#az_network_application_gateway_address-pool_create) 向应用程序网关添加名为 *imagesBackendPool* 和 *videoBackendPool* 的后端池。 使用 [az network application-gateway frontend-port create](/cli/azure/application-gateway#az_network_application_gateway_frontend_port_create) 为池添加前端端口。 

```azurecli-interactive
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name imagesBackendPool
az network application-gateway address-pool create \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name videoBackendPool
az network application-gateway frontend-port create \
  --port 8080 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name port8080
```

### <a name="add-backend-listener"></a>添加后端侦听器

使用 [az network application-gateway http-listener create](/cli/azure/application-gateway#az_network_application_gateway_http_listener_create) 添加路由流量所需的名为 *backendListener* 的后端侦听器。


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port port8080 \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-url-path-map"></a>添加 URL 路径映射

URL 路径映射可确保将特定的 URL 路由到特定的后端池。 可以分别使用 [az network application-gateway url-path-map create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_create) 和 [az network application-gateway url-path-map rule create](/cli/azure/application-gateway#az_network_application_gateway_url_path_map_rule_create) 创建名为 *imagePathRule* 和 *videoPathRule* 的 URL 路径映射

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name myPathMap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --address-pool imagesBackendPool \
  --default-address-pool appGatewayBackendPool \
  --default-http-settings appGatewayBackendHttpSettings \
  --http-settings appGatewayBackendHttpSettings \
  --rule-name imagePathRule
az network application-gateway url-path-map rule create \
  --gateway-name myAppGateway \
  --name videoPathRule \
  --resource-group myResourceGroupAG \
  --path-map-name myPathMap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-routing-rule"></a>添加路由规则

路由规则可将 URL 映射与所创建的侦听器相关联。 可以使用 [az network application-gateway rule create](/cli/azure/application-gateway#az_network_application_gateway_rule_create) 添加名为 *rule2* 的规则。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map myPathMap \
  --address-pool appGatewayBackendPool
```

## <a name="create-virtual-machine-scale-sets"></a>创建虚拟机规模集

在此示例中，将创建三个虚拟机规模集以支持所创建的三个后端池。 创建的规模集分别名为 *myvmss1*、*myvmss2* 和 *myvmss3*。 每个规模集包含两个在其上安装了 NGINX 的虚拟机实例。

```azurecli-interactive
for i in `seq 1 3`; do
  if [ $i -eq 1 ]
  then
    poolName="appGatewayBackendPool" 
  fi
  if [ $i -eq 2 ]
  then
    poolName="imagesBackendPool"
  fi
  if [ $i -eq 3 ]
  then
    poolName="videoBackendPool"
  fi
  az vmss create \
    --name myvmss$i \
    --resource-group myResourceGroupAG \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password Azure123456! \
    --instance-count 2 \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --vm-sku Standard_DS2 \
    --upgrade-policy-mode Automatic \
    --app-gateway myAppGateway \
    --backend-pool-name $poolName
done
```

### <a name="install-nginx"></a>安装 NGINX

```azurecli-interactive
for i in `seq 1 3`; do
  az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroupAG \
    --vmss-name myvmss$i \
    --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
done
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

若要获取应用程序网关的公共 IP 地址，可以使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如，*http://40.121.222.19*, *http://40.121.222.19:8080/images/test.htm*, 或 *http://40.121.222.19:8080/video/test.htm*。

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![在应用程序网关中测试基 URL](./media/application-gateway-create-url-route-cli/application-gateway-nginx.png)

将 http://<ip-address>:8080/video/test.html 的 URL 更改到基 URL 的末尾，应看到类似下例所示的内容：

![应用程序网关中的测试映像 URL](./media/application-gateway-create-url-route-cli/application-gateway-nginx-images.png)

更改 http://<ip-address>:8080/video/test.html 的 URL，应看到类似下例所示的内容。

![在应用程序网关中测试视频 URL](./media/application-gateway-create-url-route-cli/application-gateway-nginx-video.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 设置网络
> * 使用 URL 映射创建应用程序网关
> * 使用后端池创建虚拟机规模集

若要了解有关应用程序网关及其关联资源的详细信息，请继续阅读操作指南文章。
