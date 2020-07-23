---
title: 快速入门：使用 CLI 定向 Web 流量
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure CLI 创建 Azure 应用程序网关，用以将 Web 流量定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: f60b26756c0affffbd45c8596fdf73d11ffa8e81
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80239513"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure CLI

在本快速入门中，你将使用 Azure CLI 创建一个应用程序网关。 然后对其进行测试以确保其正常运行。 

该应用程序网关将应用程序 Web 流量定向到后端池中的特定资源。 你将向端口分配侦听器，创建规则，并向后端池中添加资源。 为简单起见，本文使用了带有公共前端 IP 的简单设置、一个在应用程序网关上托管单个站点的基本侦听器、一个基本的请求路由规则，以及后端池中的两台虚拟机。

还可以使用 [Azure PowerShell](quick-create-powershell.md) 或 [Azure 门户](quick-create-portal.md)完成本快速入门。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure CLI 2.0.4 或更高版本](/cli/azure/install-azure-cli)（如果在本地运行 Azure CLI）。

## <a name="create-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 使用 `az group create` 创建一个资源组。 

以下示例在 eastus 位置创建名为 myResourceGroupAG 的资源组   。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

Azure 需要一个虚拟网络才能在创建的资源之间通信。  应用程序网关子网只能包含应用程序网关。 不允许其他资源。  可以创建应用程序网关的新子网，也可以使用现有的子网。 在此示例中，你将创建两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以根据用例将应用程序网关的前端 IP 配置为公共或专用。 在此示例中，你将选择公共前端 IP 地址。

若要创建虚拟网络和子网，请使用 `az network vnet create`。 运行 `az network public-ip create` 来创建公共 IP 地址。

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
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-the-backend-servers"></a>创建后端服务器

后端可以具有 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 在此示例中，你将创建用作应用程序网关的后端服务器的两个虚拟机。 还会在虚拟机上安装 IIS 以测试应用程序网关。

#### <a name="create-two-virtual-machines"></a>创建两个虚拟机

在虚拟机上安装 NGINX Web 服务器，验证是否已成功创建应用程序网关。 可使用 cloud-init 配置文件在 Linux 虚拟机上安装 NGINX 并运行“Hello World”Node.js 应用。 有关 cloud-init 的详细信息，请参阅 [cloud-init 对 Azure 中虚拟机的支持](../virtual-machines/linux/using-cloud-init.md)。

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

使用 `az network nic create` 创建网络接口。 若要创建虚拟机，请使用 `az vm create`。

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

使用 `az network application-gateway create` 创建应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、SKU 和 HTTP 设置。 然后，Azure 将添加网络接口的专用 IP 地址作为应用程序网关后端池中的服务器。

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_v2 \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure 可能需要长达 30 分钟的时间来创建应用程序网关。 创建该网关以后，即可在“应用程序网关”页的“设置”部分查看以下设置：  

- **appGatewayBackendPool**：位于“后端池”页。  它指定所需的后端池。
- **appGatewayBackendHttpSettings**：位于“HTTP设置”页。  它指定应用程序网关使用端口 80 和 HTTP 协议进行通信。
- **appGatewayHttpListener**：位于“侦听器”页。  它指定与 **appGatewayBackendPool** 关联的默认侦听器。
- **appGatewayFrontendIP**：位于“前端 IP 配置”页。  它将 *myAGPublicIPAddress* 分配到 **appGatewayHttpListener**。
- **rule1**：位于“规则”页。  它指定与 **appGatewayHttpListener** 关联的默认路由规则。

## <a name="test-the-application-gateway"></a>测试应用程序网关

虽然 Azure 不需 NGINX Web 服务器即可创建应用程序网关，但本快速入门中安装了它，用来验证 Azure 是否已成功创建应用程序网关。 若要获取新应用程序网关的公共 IP 地址，请使用 `az network public-ip show`。 

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。
    
![测试应用程序网关](./media/quick-create-cli/application-gateway-nginxtest.png)

刷新浏览器时，会看到另一 VM 的名称。 这表示应用程序网关创建成功，可以与后端连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请使用 `az group delete` 命令删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)

