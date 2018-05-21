---
title: 快速入门 - 使用 Azure 应用程序网关定向 Web 流量 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Azure 应用程序网关，用以将 Web 流量重定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: quickstart
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 99c3975c6ab2c7a20dfbab519dae575a2a61465f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure CLI

使用 Azure 应用程序网关，可以通过为端口分配侦听器、创建规则以及向后端池添加资源，来将应用程序 Web 流量定向到特定资源。

本快速入门展示了如何使用 Azure CLI 来快速创建后端池中有两台虚拟机的应用程序网关。 然后，对它进行测试以确保它正常工作。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求运行 Azure CLI 2.0.4 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

需要始终在资源组中创建资源。 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

需要为应用程序网关创建虚拟网络才能与其他资源进行通信。 可以在创建应用程序网关的同时创建虚拟网络。 在本示例中创建了两个子网：一个用于应用程序网关，另一个用于虚拟机。 

使用 [az network vnet create](/cli/azure/vnet#az_vnet_create) 创建虚拟网络和子网。 使用 [az network public-ip create](/cli/azure/public-ip#az_public_ip_create) 创建公用 IP 地址。

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
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>创建后端服务器

在此示例中，将创建两个虚拟机以用作应用程序网关的后端服务器。 

### <a name="create-two-virtual-machines"></a>创建两个虚拟机

还可以在虚拟机上安装 NGINX，以验证是否已成功创建应用程序网关。 可使用 cloud-init 配置文件在 Linux 虚拟机上安装 NGINX 并运行“Hello World”Node.js 应用。 

在当前 shell 中创建名为“cloud-init.txt”的文件，并将以下配置复制粘贴到 shell。 请确保正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建网络接口。 使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>创建应用程序网关

使用 [az network application-gateway create](/cli/azure/application-gateway#az_application_gateway_create) 创建应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将添加网络接口的专用 IP 地址作为应用程序网关后端池中的服务器。

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

创建应用程序网关可能需要多达 30 分钟。 创建应用程序网关后，可以看到它的这些功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。

## <a name="test-the-application-gateway"></a>测试应用程序网关

创建应用程序网关不需要安装 NGINX，但本快速入门中安装了它，用来验证应用程序网关是否已成功创建。 若要获取应用程序网关的公用 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)。 复制该公用 IP 地址，并将其粘贴到浏览器的地址栏。

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

![测试应用程序网关](./media/quick-create-cli/application-gateway-nginxtest.png)

刷新浏览器时，应该会看到显示了另一 VM 的名称。

## <a name="clean-up-resources"></a>清理资源

首先，探究随应用程序网关创建的资源，当不再需要这些资源时，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令来删除资源组、应用程序网关和所有相关资源。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)

