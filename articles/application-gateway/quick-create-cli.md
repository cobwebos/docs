---
title: 快速入门 - 使用 Azure 应用程序网关定向 Web 流量 - Azure CLI | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Azure 应用程序网关，用以将 Web 流量重定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d14b8c9c752c9d41a42f092662c5f3aa88840dc5
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157711"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure CLI

本快速入门展示了如何使用 Azure CLI 来快速创建后端池中有两台虚拟机的应用程序网关。 然后，对它进行测试以确保它正常工作。 使用 Azure 应用程序网关，可以为端口分配侦听器、创建规则以及向后端池添加资源，以便将应用程序 Web 流量定向到特定资源。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，请运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 **az --version**。 有关安装或升级的信息，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

创建虚拟网络后，应用程序网关即可与其他资源通信。 可以在创建应用程序网关的同时创建虚拟网络。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于虚拟机。 应用程序网关子网只能包含应用程序网关。 不允许其他资源。

若要创建虚拟网络和子网，请使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)。 运行 [az network public-ip create](/cli/azure/network/public-ip#az-public-ip-create) 即可创建公共 IP 地址。

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

在此示例中，你创建两台虚拟机，供 Azure 用作应用程序网关的后端服务器。 

### <a name="create-two-virtual-machines"></a>创建两个虚拟机

在虚拟机上安装 [NGINX Web 服务器](https://docs.nginx.com/nginx/)，验证是否已成功创建应用程序网关。 可使用 cloud-init 配置文件在 Linux 虚拟机上安装 NGINX 并运行“Hello World”Node.js 应用。 有关 cloud-init 的详细信息，请参阅 [cloud-init 对 Azure 中虚拟机的支持](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)。

在 Azure Cloud Shell 中，将以下配置复制并粘贴到名为 *cloud-init.txt* 的文件中。 输入 *editor cloud-init.txt* 即可创建该文件。

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

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建网络接口。 若要创建虚拟机，请使用 [az vm create](/cli/azure/vm#az-vm-create)。

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

使用 [az network application-gateway create](/cli/azure/network/application-gateway#az-application-gateway-create) 创建应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、SKU 和 HTTP 设置。 然后，Azure 将添加网络接口的专用 IP 地址作为应用程序网关后端池中的服务器。

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

Azure 可能需要长达 30 分钟的时间来创建应用程序网关。 创建该网关以后，即可在“应用程序网关”页的“设置”部分查看以下设置：

- **appGatewayBackendPool**：位于“后端池”页。 它指定所需的后端池。
- **appGatewayBackendHttpSettings**：位于“HTTP设置”页。 它指定应用程序网关使用端口 80 和 HTTP 协议进行通信。
- **appGatewayHttpListener**：位于“侦听器”页。 它指定与 **appGatewayBackendPool** 关联的默认侦听器。
- **appGatewayFrontendIP**：位于“前端 IP 配置”页。 它将 *myAGPublicIPAddress* 分配到 **appGatewayHttpListener**。
- **rule1**：位于“规则”页。 它指定与 **appGatewayHttpListener** 关联的默认路由规则。

## <a name="test-the-application-gateway"></a>测试应用程序网关

虽然 Azure 不需 NGINX Web 服务器即可创建应用程序网关，但本快速入门中安装了它，用来验证 Azure 是否已成功创建应用程序网关。 若要获取新应用程序网关的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。
    
![测试应用程序网关](./media/quick-create-cli/application-gateway-nginxtest.png)

刷新浏览器时，会看到另一 VM 的名称。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请使用 [az group delete](/cli/azure/group#az-group-delete) 命令删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)

