---
title: 创建内部的基本负载均衡器 - Azure CLI 2.0 | Microsoft Docs
description: 了解如何使用 Azure CLI 2.0 创建内部负载均衡器
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/27/2018
ms.author: kumud
ms.openlocfilehash: bd4dda835279a21509f77814f4d5f9e30e8a42c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439192"
---
# <a name="create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli-20"></a>使用 Azure CLI 2.0 创建内部负载均衡器以对 VM 进行负载均衡

本文介绍如何创建内部负载均衡器，以便对 VM 进行负载均衡。 为了测试负载均衡器，请部署两个运行 Ubuntu 服务器的虚拟机 (VM)，以便对 Web 应用进行负载均衡。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.28 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group#create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 eastus 位置创建名为 myResourceGroupILB 的资源组：

```azurecli-interactive
  az group create \
    --name myResourceGroupILB \
    --location eastus
```
## <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) 在 *myResourceGroup* 中创建名为 *myVnet* 的虚拟网络，该虚拟网络包含名为 *mySubnet* 的子网。

```azurecli-interactive
  az network vnet create \
    --name myVnet \
    --resource-group myResourceGroupILB \
    --location eastus \
    --subnet-name mySubnet
```
## <a name="create-basic-load-balancer"></a>创建基本负载均衡器

本部分详细介绍如何创建和配置负载均衡器的以下组件：
  - 前端 IP 配置，用于在负载均衡器上接收传入网络流量。
  - 后端 IP 池，前端池将负载均衡的网络流量发送到此处。
  - 运行状况探测，用于确定后端 VM 实例的运行状况。
  - 负载均衡器规则，用于定义如何将流量分配给 VM。

### <a name="create-the-load-balancer"></a>创建负载均衡器

使用 [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) 创建名为 **myLoadBalancer** 的公共基本负载均衡器，该负载均衡器包括名为 **myFrontEnd** 的前端 IP 配置，以及名为 **myBackEndPool** 的后端池（与专用 IP 地址 **10.0.0.7 相关联）。

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupILB \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEnd \
    --private-ip-address 10.0.0.7 \
    --backend-pool-name myBackEndPool \
    --vnet-name myVnet \
    --subnet mySubnet      
  ```
### <a name="create-the-health-probe"></a>创建运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以接收网络流量。 探测器检查失败的虚拟机实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) 创建运行状况探测，以监视虚拟机的运行状况。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义传入流量的前端 IP 配置和用于接收流量的后端 IP 池，以及所需源和目标端口。 使用 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) 创建负载均衡器规则 *myLoadBalancerRuleWeb*，以便侦听前端池 *myFrontEndPool* 中的端口 80，并且将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *myBackEndPool*。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupILB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="create-servers-for-the-backend-address-pool"></a>为后端地址池创建服务器

需要先创建支持的虚拟网络资源，然后才能部署一些 VM 并测试负载均衡器。

### <a name="create-nics"></a>创建 NIC

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建两个网络接口，并将它们与专用 IP 地址关联。 

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupILB \
    --name myNic$i \
    --vnet-name myVnet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
done
```

## <a name="create-backend-servers"></a>创建后端服务器

本示例将创建两个要用作负载均衡器后端服务器的虚拟机。 若要验证负载均衡器是否已成功创建，还需要在虚拟机上安装 NGINX。

### <a name="create-an-availability-set"></a>创建可用性集

使用 [az vm availabilityset create](/cli/azure/network/nic#az-network-availabilityset-create) 创建可用性集

 ```azurecli-interactive
  az vm availability-set create \
    --resource-group myResourceGroupILB \
    --name myAvailabilitySet
```

### <a name="create-two-virtual-machines"></a>创建两个虚拟机

可使用 cloud-init 配置文件在 Linux 虚拟机上安装 NGINX 并运行“Hello World”Node.js 应用。 在当前 shell 中创建名为“cloud-init.txt”的文件，并将以下配置复制粘贴到 shell。 请确保正确复制整个 cloud-init 文件，尤其是第一行：

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
 
使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机。

 ```azurecli-interactive
for i in `seq 1 2`; do
  az vm create \
    --resource-group myResourceGroupILB \
    --name myVM$i \
    --availability-set myAvailabilitySet \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
    done
```
VM 可能需要几分钟才能部署好。

### <a name="create-a-vm-for-testing-the-load-balancer"></a>创建用于测试负载均衡器的 VM

若要测试负载均衡器，请创建虚拟机 *myVMTest* 并将其关联到 *myNic3*。

```azurecli-interactive
 az vm create \
    --resource-group myResourceGroupILB \
    --name myVMTest \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword123456!
```

## <a name="test-the-internal-load-balancer"></a>测试内部负载均衡器

若要测试负载均衡器，必须先获得负载均衡器的专用 IP 地址。 接下来，登录到虚拟机 myVMTest，然后在 Web 浏览器的地址栏中键入专用 IP 地址。

若要获取负载均衡器的专用 IP 地址，请使用 [az network lb show](/cli/azure/network/public-ip##az-network-lb-show)。 复制该专用 IP 地址，然后将其粘贴到虚拟机 *myVMTest* 的 Web 浏览器的地址栏中。

```azurecli-interactive
  az network lb show \
    --name myLoadBalancer \
    --resource-group myResourceGroupILB
``` 
![测试负载均衡器](./media/load-balancer-get-started-ilb-arm-cli/load-balancer-test.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive 
  az group delete --name myResourceGroupILB
```


## <a name="next-steps"></a>后续步骤
本文介绍了如何创建内部基本负载平衡器，向其附加 VM，配置负载均衡器流量规则、运行状况探测，然后测试负载均衡器。 若要详细了解负载均衡器及其关联的资源，请继续阅读操作方法文章。
