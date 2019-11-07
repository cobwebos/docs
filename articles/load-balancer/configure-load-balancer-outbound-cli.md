---
title: 使用 Azure CLI 配置负载均衡和出站规则
titlesuffix: Azure Load Balancer
description: 本文介绍如何使用 Azure CLI 在标准负载均衡器中配置负载均衡和出站规则。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 503c8f71b7e26cfe6803a6df1d3fec9ef55cd5c3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571136"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>使用 Azure CLI 在标准负载均衡器中配置负载均衡和出站规则

本快速入门介绍如何使用 Azure CLI 在标准负载均衡器中配置出站规则。  

完成后，负载均衡器资源包含两个前端以及与之关联的规则：一个前端用于入站，另一个前端用于出站。  每个前端都会引用一个公共 IP 地址，此方案对于入站和出站使用不同的公共 IP 地址。   负载均衡规则仅提供入站负载均衡，由出站规则控制为 VM 提供的出站 NAT。  本快速入门使用两个独立的后端池（一个用于入站连接，一个用于出站连接）来演示功能，以及如何灵活实施此方案。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.28 版或更高版本。 若要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-resource-group"></a>创建资源组

使用 [az group create](https://docs.microsoft.com/cli/azure/group) 创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

以下示例在“eastus2”位置创建名为“myresourcegroupoutbound”的资源组：

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>创建虚拟网络
使用 *az network vnet create* 在 *myresourcegroupoutbound* 中创建名为 *myvnetoutbound* 的虚拟网络，该虚拟网络包含名为 [mysubnetoutbound](https://docs.microsoft.com/cli/azure/network/vnet) 的子网。

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>创建入站公共 IP 地址 

若要通过 Internet 访问 Web 应用，需要负载均衡器有一个公共 IP 地址。 标准负载均衡器仅支持标准公共 IP 地址。 使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 在 *myresourcegroupoutbound* 中创建名为 *mypublicipinbound* 的标准公共 IP 地址。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>创建出站公共 IP 地址 

使用 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) 为负载均衡器的前端出站配置创建标准 IP 地址。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>创建 Azure 负载均衡器

本部分详细介绍如何创建和配置负载均衡器的以下组件：
  - 前端 IP，用于在负载均衡器上接收传入网络流量。
  - 后端池，前端 IP 将负载均衡的网络流量发送到此处。
  - 用于出站连接的后端池。 
  - 运行状况探测，用于确定后端 VM 实例的运行状况。
  - 负载均衡器入站规则，用于定义如何将流量分配给 VM。
  - 负载均衡器出站规则，用于定义如何从 VM 分配流量。

### <a name="create-load-balancer"></a>创建负载均衡器

使用 [az network lb create ](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 创建名为 *lb* 的入站 IP 地址负载均衡器，该负载均衡器包括入站前端 IP 配置和后端池 *bepoolinbound*（与在前一步创建的公共 IP 地址 *mypublicipinbound* 相关联）。

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>创建出站池

使用 [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 创建名为 *bepooloutbound* 的另一个后端地址池，用于定义 VM 池的出站连接。  创建独立的出站池可以提供最大的灵活性，但你也可以忽略此步骤，仅使用入站池 *bepoolinbound*。

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>创建出站前端 IP
使用 [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) 为负载均衡器创建出站前端 IP 配置，该负载均衡器包括名为 *myfrontendoutbound* 的出站前端 IP 配置（关联到公共 IP 地址 *mypublicipoutbound*）

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>创建运行状况探测

运行状况探测器会检查所有虚拟机实例，以确保它们可以发送网络流量。 探测器检查失败的虚拟机实例将从负载均衡器中删除，直到它恢复联机状态并且探测器检查确定它运行正常。 使用 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) 创建运行状况探测，以监视虚拟机的运行状况。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>创建负载均衡规则

负载均衡器规则定义传入流量的前端 IP 配置和后端池以接收流量，同时定义所需源和目标端口。 使用 *az network lb rule create* 创建负载均衡器规则 [myinboundlbrule](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)，以便侦听前端池 *myfrontendinbound* 中的端口 80，并且将经过负载均衡的网络流量发送到也使用端口 80 的后端地址池 *bepool*。 

>[!NOTE]
>此负载均衡规则通过其 --disable-outbound-snat 参数禁用自动出站 (S)NAT。 出站 NAT 仅通过出站规则提供。

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepoolinbound \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>创建出站规则

出站规则定义前端公共 IP，该 IP 由前端 *myfrontendoutbound*（用于此规则适用的所有出站 NAT 流量和后端池）表示。  创建出站规则 *myoutboundrule*，以便对 *bepool* 后端池中的所有虚拟机（NIC IP 配置）进行出站网络转换。  下面的命令还将出站空闲超时从 4 分钟更改为 15 分钟，并分配 10000 SNAT 端口而不是 1024 端口。  有关详细信息，请参阅[出站规则](https://aka.ms/lboutboundrules)。

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepooloutbound
```

如果你不想要使用独立的出站池，可以更改上述命令中的地址池参数，以指定 *bepoolinbound*。  我们建议使用独立的池，以提高灵活性，并方便阅读最终的配置。

此时，可以使用 *az network nic ip-config address-pool add*，通过更新相应 NIC 资源的 IP 配置来继续将 VM 添加到后端池 __bepoolinbound__ *和* [bepooloutbound](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和所有相关的资源，可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令将其删除。

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>后续步骤
在本文中，你创建了标准负载均衡器、配置了两个入站负载均衡器规则，并为后端池中的 VM 配置了运行状况探测。 若要了解有关 Azure 负载均衡器的详细信息，请继续学习 Azure 负载均衡器教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
