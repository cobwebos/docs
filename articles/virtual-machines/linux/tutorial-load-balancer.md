---
title: "如何在 Azure 中均衡 Linux 虚拟机负载 | Microsoft Docs"
description: "了解如何使用 Azure 负载均衡器在 3 个 Linux VM 之间创建高可用性和安全性的应用程序"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/11/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 25e2538e220327a078a6527e667dfcd6cb838b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>如何在 Azure 中均衡 Linux 虚拟机负载以创建高可用性应用程序
负载均衡通过将传入请求分布到多个虚拟机来提供更高级别的可用性。 本教程介绍 Azure 负载均衡器的不同组件，这些组件用于分发流量和提供高可用性。 学习如何：

> [!div class="checklist"]
> * 创建 Azure 负载均衡器
> * 创建负载均衡器运行状况探测
> * 创建负载均衡器流量规则
> * 使用 cloud-init 创建基本的 Node.js 应用
> * 创建虚拟机并将其附加到负载均衡器
> * 查看运行中的负载均衡器
> * 从负载均衡器中添加和删除 VM


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="azure-load-balancer-overview"></a>Azure 负载均衡器概述
Azure 负载均衡器是位于第 4 层（TCP、UDP）的负载均衡器，通过在正常运行的 VM 之间分发传入流量提供高可用性。 负载均衡器的运行状况探测监视每个 VM 上的给定端口，并仅将流量分发给可操作的 VM。

定义包含一个或多个公共 IP 地址的前端 IP 配置。 利用此前端 IP 配置，可通过 Internet 访问负载均衡器和应用程序。 

虚拟机使用其虚拟网络接口卡 (NIC) 连接到负载均衡器。 若要向 VM 分发流量，后端地址池需包含连接到负载均衡器的虚拟 NIC 的 IP 地址。

若要控制流量流，需为映射到 VM 的特定端口和协议定义负载均衡器规则。

如果遵循了前面的教程来[创建虚拟机规模集](tutorial-create-vmss.md)，则已创建负载均衡器。 所有这些组件都已配置为规模集的一部分。


## <a name="create-azure-load-balancer"></a>创建 Azure 负载均衡器
本部分详细介绍如何创建和配置负载均衡器的每个组件。 创建负载均衡器之前，需使用 [az group create](/cli/azure/group#create) 创建资源组。 以下示例在“eastus”位置创建名为“myResourceGroupLoadBalancer”的资源组：

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址
若要通过 Internet 访问应用，需要负载均衡器的一个公共 IP 地址。 使用 [az network public-ip create](/cli/azure/network/public-ip#create) 创建公共 IP 地址。 以下示例在“myResourceGroupLoadBalancer”资源组中创建名为“myPublicIP”的公共 IP 地址：

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>创建负载均衡器
使用 [az network lb create](/cli/azure/network/lb#create) 创建负载均衡器。 以下示例创建名为“myLoadBalancer”的负载均衡器，并将“myPublicIP”地址分配到前端 IP 配置：

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>创建运行状况探测器
若要允许负载均衡器监视应用的状态，可以使用运行状况探测器。 运行状况探测器基于其对运行状况检查的响应，从负载均衡器中动态添加或删除 VM。 默认情况下，在 15 秒时间间隔内发生两次连续的故障后，将从负载均衡器分布中删除 VM。 可以为应用创建基于协议或特定运行状况检查页面的运行状况探测器。 

以下示例创建一个 TCP 探测。 还可创建自定义 HTTP 探测，以便执行更精细的运行状况检查。 使用自定义 HTTP 探测时，必须创建运行状况检查页，例如 healthcheck.js。 探测必须为负载均衡器返回 HTTP 200 OK 响应，以保持主机处于旋转状态。

若要创建 TCP 运行状况探测，请使用 [az network lb probe create](/cli/azure/network/lb/probe#create)。 以下示例创建名为“myHealthProbe”的运行状况探测：

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则
负载均衡器规则用于定义将流量分配给 VM 的方式。 定义传入流量的前端 IP 配置和后端 IP 池以接收流量，同时定义所需源和目标端口。 若要确保仅正常运行的 VM 接收流量，还需定义要使用的运行状况探测。

使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 创建负载均衡器规则。 以下示例创建名为“myLoadBalancerRule”的规则、使用“myHealthProbe”运行状况探测并平衡端口 80 上的流量：

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>配置虚拟网络
需要先创建支持的虚拟网络资源，才能部署某些 VM 和测试均衡器。 有关虚拟网络的详细信息，请参阅[管理 Azure 虚拟网络](tutorial-virtual-network.md)教程。

### <a name="create-network-resources"></a>创建网络资源
使用 [az network vnet create](/cli/azure/network/vnet#create) 创建虚拟网络。 以下示例创建名为“myVnet”的虚拟网络和一个名为“mySubnet”的子网：

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

若要添加网络安全组，请使用 [az network nsg create](/cli/azure/network/nsg#create)。 以下示例创建名为“myNetworkSecurityGroup”的网络安全组：

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#create) 创建网络安全组规则。 以下示例创建名为“myNetworkSecurityGroupRule”的网络安全组规则：

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

使用 [az network nic create](/cli/azure/network/nic#create) 创建虚拟 NIC。 以下示例创建三个虚拟 NIC。 （在以下步骤中为应用创建的每个 VM 各使用一个虚拟 NIC）。 可随时创建其他虚拟 NIC 和 VM，并将其添加到负载均衡器：

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-virtual-machines"></a>创建虚拟机

### <a name="create-cloud-init-config"></a>创建 cloud-init 配置
在有关[如何在首次启动时自定义 Linux 虚拟机](tutorial-automate-vm-deployment.md)的上一个教程中，已了解如何使用 cloud-init 自动执行 VM 自定义。 可使用同一个 cloud-init 配置文件安装 NGINX 并运行简单的“Hello World”Node.js 应用。

在当前 shell 中，创建名为“cloud-init.txt”的文件并粘贴下面的配置。 例如，在不处于本地计算机上的 Cloud Shell 中创建文件。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

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

### <a name="create-virtual-machines"></a>创建虚拟机
要提高应用的高可用性，请将 VM 放置在可用性集中。 有关可用性集的详细信息，请参阅前面的[如何创建高可用性虚拟机](tutorial-availability-sets.md)教程。

使用 [az vm availability-set create](/cli/azure/vm/availability-set#create) 创建可用性集。 以下示例创建名为“myAvailabilitySet”的可用性集：

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

现在，可使用 [az vm create](/cli/azure/vm#create) 创建 VM。 以下示例创建三个 VM，并生成 SSH 密钥（如果它们尚不存在）：

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 `--no-wait` 参数不会等待所有任务完成。 可能还需等待几分钟才能访问应用。 在每个 VM 上运行应用时，负载均衡器运行状况探测器会自动检测。 应用运行后，负载均衡器规则将开始分布流量。


## <a name="test-load-balancer"></a>测试负载均衡器
使用 [az network public-ip show](/cli/azure/network/public-ip#show) 获取负载均衡器的公共 IP 地址。 以下示例获取前面创建的“myPublicIP”的 IP 地址：

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

然后，可将公共 IP 地址输入 web 浏览器中。 请记住 - 在负载均衡器开始向 VM 分发流量之前，VM 需要几分钟才能准备就绪。 随即显示应用，包括负载均衡器将流量分发到的 VM 的主机名，如下例所示：

![运行 Node.js 应用](./media/tutorial-load-balancer/running-nodejs-app.png)

若要查看负载均衡器如何在运行应用的所有 3 个 VM 之间分配流量，可强制刷新 web 浏览器。


## <a name="add-and-remove-vms"></a>添加和删除 VM
建议对运行应用的 VM 执行维护，例如安装 OS 更新。 若要应对应用增加的流量，建议添加更多 VM。 本部分演示了如何在负载均衡器中删除或添加 VM。

### <a name="remove-a-vm-from-the-load-balancer"></a>从负载均衡器中删除 VM
可使用 [az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove) 从后端地址池中删除 VM。 以下示例从“myLoadBalancer”中删除“myVM2”的虚拟 NIC：

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

若要查看负载均衡器如何在运行应用的其余两个 VM 之间分发流量，可强制刷新 web 浏览器。 现在可以对 VM 执行维护，例如安装 OS 更新或执行 VM 重新启动。

### <a name="add-a-vm-to-the-load-balancer"></a>将 VM 添加到负载均衡器
执行 VM 维护后，或者如果需要扩展容量，可使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add) 将 VM 添加到后端地址池。 以下示例将“myVM2”的虚拟 NIC 添加到“myLoadBalancer”：

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>后续步骤
在本教程中，已创建负载均衡器并已将 VM 附加到它。 你已了解如何：

> [!div class="checklist"]
> * 创建 Azure 负载均衡器
> * 创建负载均衡器运行状况探测
> * 创建负载均衡器流量规则
> * 使用 cloud-init 创建基本的 Node.js 应用
> * 创建虚拟机并将其附加到负载均衡器
> * 查看运行中的负载均衡器
> * 从负载均衡器中添加和删除 VM

请转到下一教程，深入了解 Azure 虚拟网络组件。

> [!div class="nextstepaction"]
> [管理 VM 和虚拟网络](tutorial-virtual-network.md)
