---
title: 使用 Azure CLI 对区域冗余的 VM 进行负载均衡 | Microsoft Docs
description: 了解如何使用 Azure CLI 通过区域冗余的前端创建公共负载均衡器标准
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2018
ms.author: kumud
ms.openlocfilehash: 29dcfaad840b5498dd859082ce11655a4f1fe8af
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
#  <a name="load-balance-vms-across-all-availability-zones-using-azure-cli"></a>使用 Azure CLI 跨所有可用性区域对 VM 进行负载均衡

本文逐步讲解如何创建一个包含区域冗余前端的公共[标准负载均衡器](https://aka.ms/azureloadbalancerstandard)，以实现区域冗余，且不依赖于多个 DNS 记录。 单个前端 IP 地址已自动实现区域冗余。  如果对负载均衡器使用区域冗余前端，现在只需通过单个 IP 地址即可访问某个区域内的虚拟网络中跨所有可用性区域的任何 VM。 使用可用性区域可以在整个数据中心发生故障或服务中断（这种情况很少见）时保护应用和数据。

有关对标准负载均衡器使用可用性区域的详细信息，请参阅[标准负载均衡器和可用性区域](load-balancer-standard-availability-zones.md)。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.17 或更高版本。  若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

> [!NOTE]
> 选择 Azure 资源、区域和 VM 大小系列时可使用可用性区域支持。 有关如何开始使用以及可以尝试将可用性区域用于哪些 Azure 资源、区域和 VM 大小系列的详细信息，请参阅[可用性区域概述](https://docs.microsoft.com/azure/availability-zones/az-overview)。 若需支持，可以在 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) 上寻求帮助或者 [open an Azure support ticket](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（创建 Azure 支持票证）。  

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在 *westeurope* 位置创建名为 *myResourceGroupSLB* 的资源组。

```azurecli-interactive
az group create \
--name myResourceGroupSLB \
--location westeurope
```

## <a name="create-a-zone-redundant-public-ip-standard"></a>创建区域冗余的公共 IP 标准
若要通过 Internet 访问应用，需要负载均衡器的一个公共 IP 地址。 区域冗余的前端由区域中的所有可用性区域同时提供服务。 使用 [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) 创建区域冗余的公共 IP 地址。 创建标准公共 IP 地址时，该地址默认是区域冗余的。

以下示例在 *myResourceGroupLoadBalancer* 资源组中创建名为 *myPublicIP* 的区域冗余公共 IP 地址。

```azurecli-interactive
az network public-ip create \
--resource-group myResourceGroupSLB \
--name myPublicIP \
--sku Standard
```

## <a name="create-azure-load-balancer-standard"></a>创建标准 Azure 负载均衡器
本部分详细介绍如何创建和配置负载均衡器的以下组件：
- 前端 IP 池，用于在负载均衡器上接收传入网络流量。
- 后端 IP 池，前端池将负载均衡的网络流量发送到此处。
- 运行状况探测，用于确定后端 VM 实例的运行状况。
- 负载均衡器规则，用于定义如何将流量分配给 VM。

### <a name="create-the-load-balancer"></a>创建负载均衡器
使用 [az network lb create](/cli/azure/network/lb#az_network_lb_create) 创建标准负载均衡器。 以下示例创建名为 *myLoadBalancer* 的负载均衡器，并将 *myPublicIP* 地址分配到前端 IP 配置。

```azurecli-interactive
az network lb create \
--resource-group myResourceGroupSLB \
--name myLoadBalancer \
--public-ip-address myPublicIP \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--sku Standard
```

## <a name="create-health-probe-on-port-80"></a>在端口 80 上创建运行状况探测

运行状况探测器会检查所有虚拟机实例，以确保它们可以发送网络流量。 探测器检查失败的虚拟机实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 使用 az network lb probe create 创建运行状况探测，以监视虚拟机的运行状况。 若要创建 TCP 运行状况探测，请使用 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create)。 以下示例创建名为“myHealthProbe”的运行状况探测：

```azurecli-interactive
az network lb probe create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myHealthProbe \
--protocol tcp \
--port 80
```

## <a name="create-load-balancer-rule-for-port-80"></a>针对端口 80 创建负载均衡器规则
负载均衡器规则定义传入流量的前端 IP 配置和用于接收流量的后端 IP 池，以及所需源和目标端口。 使用 [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) 创建负载均衡器规则 *myLoadBalancerRuleWeb*，以便侦听前端池 *myFrontEndPool* 中的端口 80，并且将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *myBackEndPool*。

```azurecli-interactive
az network lb rule create \
--resource-group myResourceGroupSLB \
--lb-name myLoadBalancer \
--name myLoadBalancerRuleWeb \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--frontend-ip-name myFrontEnd \
--backend-pool-name myBackEndPool \
--probe-name myHealthProbe
```

## <a name="configure-virtual-network"></a>配置虚拟网络
需要先创建支持的虚拟网络资源，然后才能部署一些 VM 并测试负载均衡器。

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 在 myResourceGroup 中创建名为 *myVnet* 的虚拟网络，该虚拟网络包含名为 *mySubnet* 的子网。


```azurecli-interactive
az network vnet create \
--resource-group myResourceGroupSLB \
--location westeurope \
--name myVnet \
--subnet-name mySubnet
```

### <a name="create-a-network-security-group"></a>创建网络安全组

使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建名为 *myNetworkSecurityGroup* 的网络安全组，以定义虚拟网络的入站连接。

```azurecli-interactive
az network nsg create \
--resource-group myResourceGroupSLB \
--name myNetworkSecurityGroup
```

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 针对端口 80 创建名为 *myNetworkSecurityGroupRule* 的网络安全组规则。

```azurecli-interactive
az network nsg rule create \
--resource-group myResourceGroupSLB \
--nsg-name myNetworkSecurityGroup \
--name myNetworkSecurityGroupRule \
--protocol tcp \
--direction inbound \
--source-address-prefix '*' \
--source-port-range '*' \
--destination-address-prefix '*' \
--destination-port-range 80 \
--access allow \
--priority 200
```
### <a name="create-nics"></a>创建 NIC
使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建三个虚拟 NIC，并将它们与公共 IP 地址和网络安全组关联。 以下示例创建六个虚拟 NIC。 （在以下步骤中为应用创建的每个 VM 各使用一个虚拟 NIC）。 可随时创建其他虚拟 NIC 和 VM，并将其添加到负载均衡器：

```azurecli-interactive
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupSLB \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
## <a name="create-backend-servers"></a>创建后端服务器
本示例将在区域 1、区域 2 和区域 3 中创建三个用作负载均衡器后端服务器的虚拟机。 还需要在虚拟机上安装 NGINX，以验证负载均衡器是否已成功创建。

### <a name="create-cloud-init-config"></a>创建 cloud-init 配置

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

### <a name="create-the-zonal-virtual-machines"></a>创建区域虚拟机
使用 [az vm create](/cli/azure/vm#az_vm_create) 在区域 1、区域 2 和区域 3 中创建 VM。 以下示例在每个区域中创建一个 VM，并生成 SSH 密钥（如果不存在）：

在区域 1 中创建 VM

```azurecli-interactive
 az vm create \
--resource-group myResourceGroupSLB \
--name myVM$i \
--nics myNic$i \
--image UbuntuLTS \
--generate-ssh-keys \
--zone $i \
--custom-data cloud-init.txt
```
## <a name="test-the-load-balancer"></a>测试负载均衡器

使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 获取负载均衡器的公共 IP 地址。 

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
然后，可将公共 IP 地址输入 web 浏览器中。 请记住 - 在负载均衡器开始向 VM 分发流量之前，VM 需要几分钟才能准备就绪。 随即显示应用，包括负载均衡器将流量分发到的 VM 的主机名，如下例所示：

![运行 Node.js 应用](./media/load-balancer-standard-public-zone-redundant-cli/running-nodejs-app.png)

若要查看负载均衡器如何在所有三个可用性区域中运行应用的 VM 之间分配流量，可以停止特定区域中的 VM，并刷新浏览器。

## <a name="next-steps"></a>后续步骤
- 详细了解[标准负载均衡器](./load-balancer-standard-overview.md)



