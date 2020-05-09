---
title: 教程：使用 CLI 实现基于 URL 路径的重定向
titleSuffix: Azure Application Gateway
description: 本教程介绍如何通过 Azure CLI 使用基于 URL 路径的重定向流量创建应用程序网关。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 87f6febaf89f82c2c81b397c94d744229b3f4b34
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80239494"
---
# <a name="tutorial-create-an-application-gateway-with-url-path-based-redirection-using-the-azure-cli"></a>教程：通过 Azure CLI 使用基于 URL 路径的重定向创建应用程序网关

创建[应用程序网关](application-gateway-introduction.md)时可以使用 Azure CLI 配置[基于 URL 路径的路由规则](tutorial-url-route-cli.md)。 在本教程中，使用[虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)创建后端池。 然后创建 URL 路由规则，以确保 Web 流量重定向到相应的后端池。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置网络
> * 创建应用程序网关
> * 添加侦听器和路由规则
> * 为后端池创建虚拟机规模集

下面的示例演示来自端口 8080 和 8081 并定向到相同后端池的站点流量：

![URL 路由示例](./media/tutorial-url-redirect-cli/scenario.png)

如果需要，可以使用 [Azure PowerShell](tutorial-url-redirect-powershell.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group) 创建资源组。

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组   。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

使用 [az network vnet create](/cli/azure/network/vnet) 创建名为 *myVNet* 的虚拟网络和名为 *myAGSubnet* 的子网。 然后，可以使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet) 添加后端服务器所需的名为 *myBackendSubnet* 的子网。 使用 [az network public-ip create](/cli/azure/network/public-ip) 创建名为 *myAGPublicIPAddress* 的公共 IP 地址。

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

使用 [az network application-gateway create](/cli/azure/network/application-gateway) 创建名为 myAppGateway 的应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将应用程序网关分配给之前创建的 *myAGSubnet* 和 *myPublicIPAddress*。

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到以下新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。


### <a name="add-backend-pools-and-ports"></a>添加后端池和端口

可以使用 [az network application-gateway address-pool create](/cli/azure/network/application-gateway/address-pool) 将名为 *imagesBackendPool* 和 *videoBackendPool* 的后端地址池添加到应用程序网关。 使用 [az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) 为池添加前端端口。 

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
  --name bport

az network application-gateway frontend-port create \
  --port 8081 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name rport
```

## <a name="add-listeners-and-rules"></a>添加侦听器和规则

### <a name="add-listeners"></a>添加侦听器

使用 [az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener) 添加路由流量所需的名为 *backendListener* 和 *redirectedListener* 的后端侦听器。


```azurecli-interactive
az network application-gateway http-listener create \
  --name backendListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port bport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway

az network application-gateway http-listener create \
  --name redirectedListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port rport \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-default-url-path-map"></a>添加默认 URL 路径映射

URL 路径映射可确保将特定的 URL 路由到特定的后端池。 可以分别使用 [az network application-gateway url-path-map create](/cli/azure/network/application-gateway/url-path-map) 和 [az network application-gateway url-path-map rule create](/cli/azure/network/application-gateway/url-path-map/rule) 创建名为 *imagePathRule* 和 *videoPathRule* 的 URL 路径映射

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name urlpathmap \
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
  --path-map-name urlpathmap \
  --paths /video/* \
  --address-pool videoBackendPool
```

### <a name="add-redirection-configuration"></a>添加重定向配置

可以使用 [az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) 为侦听器配置重定向。

```azurecli-interactive
az network application-gateway redirect-config create \
  --gateway-name myAppGateway \
  --name redirectConfig \
  --resource-group myResourceGroupAG \
  --type Found \
  --include-path true \
  --include-query-string true \
  --target-listener backendListener
```

### <a name="add-the-redirection-url-path-map"></a>添加重定向 URL 路径映射

```azurecli-interactive
az network application-gateway url-path-map create \
  --gateway-name myAppGateway \
  --name redirectpathmap \
  --paths /images/* \
  --resource-group myResourceGroupAG \
  --redirect-config redirectConfig \
  --rule-name redirectPathRule
```

### <a name="add-routing-rules"></a>添加路由规则

路由规则可将 URL 路径映射与所创建的侦听器相关联。 可以使用 [az network application-gateway rule create](/cli/azure/network/application-gateway/rule) 添加名为 *defaultRule* 和 *redirectedRule* 的规则。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name defaultRule \
  --resource-group myResourceGroupAG \
  --http-listener backendListener \
  --rule-type PathBasedRouting \
  --url-path-map urlpathmap \
  --address-pool appGatewayBackendPool

az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name redirectedRule \
  --resource-group myResourceGroupAG \
  --http-listener redirectedListener \
  --rule-type PathBasedRouting \
  --url-path-map redirectpathmap \
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

若要获取应用程序网关的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 例如，`http://40.121.222.19`、`http://40.121.222.19:8080/images/test.htm`、`http://40.121.222.19:8080/video/test.htm` 或 `http://40.121.222.19:8081/images/test.htm`。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![在应用程序网关中测试基 URL](./media/tutorial-url-redirect-cli/application-gateway-nginx.png)

将 URL 更改为 http://&lt;ip-address&gt;:8080/images/test.html（请将 &lt;ip-address&gt; 替换为自己的 IP 地址），应会看到如以下示例所示的内容：

![在应用程序网关中测试映像 URL](./media/tutorial-url-redirect-cli/application-gateway-nginx-images.png)

将 URL 更改为 http://&lt;ip-address&gt;:8080/video/test.html（请将 &lt;ip-address&gt; 替换为自己的 IP 地址），应会看到如以下示例所示的内容：

![在应用程序网关中测试视频 URL](./media/tutorial-url-redirect-cli/application-gateway-nginx-video.png)

现在，将 URL 更改为 http://&lt;ip-address&gt;:8081/images/test.htm（请将 &lt;ip-address&gt; 替换为自己的 IP 地址），应会在 http://&lt;ip-address&gt;:8080/images 中看到重定向回映像后端池的流量。

## <a name="clean-up-resources"></a>清理资源

当不再需要资源组、应用程序网关以及所有相关资源时，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroupAG
```
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解应用程序网关的作用](application-gateway-introduction.md)
