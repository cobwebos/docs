---
title: "在 Azure 中为 Linux 创建虚拟机规模集 | Microsoft Docs"
description: "使用虚拟机规模集在 Linux VM 上创建和部署高度可用的应用程序"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/18/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 73167924f95c8cea0ac3cb4651cb3571fb24cc01
ms.lasthandoff: 04/21/2017

---

# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>在 Linux 上创建虚拟机规模集和部署高度可用的应用
本教程介绍如何使用 Azure 中的虚拟机规模集快速缩放运行应用的虚拟机 (VM) 数目。 利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 可以手动缩放规模集中的 VM 数，也可以定义规则，以便根据 CPU 使用率、内存需求或网络流量进行自动缩放。 若要查看虚拟机规模集的运作方式，可以生成一个跨多个 Linux VM 运行的 Node.js 应用。

可以使用最新的 [Azure CLI 2.0](/cli/azure/install-azure-cli) 来完成本教程中的步骤。


## <a name="scale-set-overview"></a>规模集概述
利用虚拟机规模集，可以部署和管理一组相同的、自动缩放的虚拟机。 规模集使用的组件与前一教程[创建高度可用的 VM](tutorial-availability-sets.md) 中介绍的相同。 规模集中的 VM 在可用性集中创建，分布在逻辑容错域和更新域之间。

可以根据需要在规模集中创建 VM。 可以定义自动缩放规则，控制如何以及何时在规模集中添加或删除 VM。 这些根据 CPU 负载、内存用量或网络流量等指标触发这些规则。

使用 Azure 平台映像时，规模集最多支持 1,000 个 VM。 对于生产工作负荷，可能需要[创建自定义 VM 映像](tutorial-custom-images.md)。 使用自定义映像时，在规模集中最多可以创建 100 个 VM。


## <a name="create-an-app-to-scale"></a>创建用于缩放的应用
对于生产用途，可能需要[创建自定义 VM 映像](tutorial-custom-images.md)，其中包含已安装和配置的应用程序。 在本教程中，我们将在首次启动时自定义 VM，以便快速了解规模集的运作方式。

上一篇教程已介绍[如何使用 cloud-init 在首次启动时自定义 Linux 虚拟机](tutorial-automate-vm-deployment.md)。 可使用同一个 cloud-init 配置文件安装 NGINX 并运行简单的“Hello World”Node.js 应用。 创建名为 `cloud-init.txt` 的文件并粘贴下面的配置：

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


## <a name="create-a-scale-set"></a>创建规模集
使用 [az group create](/cli/azure/group#create) 创建资源组，然后才能创建规模集。 以下示例在 `westus` 位置创建名为 `myResourceGroupScaleSet` 的资源组：

```azurecli
az group create --name myResourceGroupScaleSet --location westus
```

现在，使用 [az vmss create](/cli/azure/vmss#create) 创建虚拟机规模集。 以下示例创建名为 `myScaleSet` 的规模集，使用cloud-int 文件自定义 VM，然后生成 SSH 密钥（如果不存在）：

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。


## <a name="allow-web-traffic"></a>允许 Web 流量
已自动创建一个负载均衡器，作为虚拟机规模集的一部分。 负载均衡器使用负载均衡器规则将流量分配到一组定义的 VM。 可以在下一篇教程[如何对 Azure 中的虚拟机进行负载均衡](tutorial-load-balancer.md)中详细了解负载均衡器的概念和配置。

若要允许通信流到达 Web 应用，请使用 [az network lb rule create](/cli/azure/network/lb/rule#create) 创建一个规则。 以下示例创建一个名为 `myLoadBalancerRuleWeb` 的规则：

```azurecli
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>测试应用
若要在 Web 上查看 Node.js 应用，请使用 [az network public-ip show](/cli/azure/network/public-ip#show) 获取负载均衡器的公共 IP 地址。 以下示例获取创建为规模集一部分的 `myScaleSetLBPublicIP` 的 IP 地址：

```azurecli
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

将公共 IP 地址输入到 Web 浏览器中。 将显示应用，包括负载均衡器将流量分发到的 VM 的主机名：

![运行 Node.js 应用](./media/tutorial-create-vmss/running-nodejs-app.png)

若要查看规模集的运作方式，可以强制刷新 Web 浏览器，以查看负载均衡器如何在运行应用的所有 VM 之间分配流量。


## <a name="management-tasks"></a>管理任务
在规模集的整个生命周期内，可能需要运行一个或多个管理任务。 此外，可能还需要创建自动执行各种生命周期任务的脚本。 Azure CLI 2.0 提供一种用于执行这些任务的快速方法。 以下是一些常见任务。

### <a name="view-vms-in-a-scale-set"></a>查看规模集中的 VM
若要查看规模集中运行的 VM 列表，请使用 [az vmss list-instances](/cli/azure/vmss#list-instances)，如下所示：

```azurecli
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

输出类似于以下示例：

```azurecli
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  westus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  westus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>增加或减少 VM 实例
若要查看规模集中当前包含的实例数，请使用 [az vmss show](/cli/azure/vmss#show) 并查询 `sku.capacity`：

```azurecli
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

然后，可以使用 [az vmss scale](/cli/azure/vmss#scale) 手动增加或减少规模集中虚拟机的数目。 以下示例将规模集中 VM 的数目设置为 `5`：

```azurecli
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```

利用自动缩放规则，可以定义如何根据网络流量或 CPU 使用率等需求，增加或减少规模集中 VM 的数目。 目前，不能在 Azure CLI 2.0 中设置这些规则。 使用 [Azure 门户](https://portal.azure.com)配置自动缩放。

### <a name="get-connection-info"></a>获取连接信息
若要获取有关规模集中 VM 的连接信息，请使用 [az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info)。 此命令为每个允许采用 SSH 进行连接的 VM 输出公共 IP 地址和端口：

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupScaleSet --name myScaleSet
```


## <a name="next-steps"></a>后续步骤
在本教程中，你已学习了如何创建虚拟机规模集。 请继续学习下一篇教程，详细了解虚拟机的负载均衡概念。

[均衡虚拟机的负载](tutorial-load-balancer.md)

