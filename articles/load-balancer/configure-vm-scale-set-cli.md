---
title: 使用现有 Azure 负载均衡器配置虚拟机规模集 - Azure CLI
description: 了解如何使用现有 Azure 负载均衡器配置虚拟机规模集。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349735"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>使用 Azure CLI 配置具有现有 Azure 负载均衡器的虚拟机规模集

在本文中，您将了解如何使用现有的 Azure 负载均衡器配置虚拟机规模集。 

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 将部署虚拟机规模集的订阅中的现有标准 sKU 负载均衡器。
- 虚拟机缩放集的 Azure 虚拟网络。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

如果选择在本地使用 CLI，则本文要求您安装 Azure CLI 版本 2.0.28 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure-cli"></a>登录 Azure CLI

登录到 Azure。

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>使用现有负载均衡器部署虚拟机规模集

将括号中的值替换为配置中资源的名称。

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

下面的示例部署虚拟机规模集，该集具有：

- 名为**myVMSS 的**虚拟机规模集
- Azure 负载均衡器名为**myLoad 平衡器**
- 负载均衡器后端池名为 **"我的后端池**"
- 名为**myVnet 的**Azure 虚拟网络
- 名为 **"我的子网"的子网**
- 名为 **"我的资源组"的资源组**
- 虚拟机规模集的 Ubuntu 服务器映像

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> 创建比例集后，无法为负载均衡器的运行状况探测使用的负载平衡规则修改后端端口。 要更改端口，可以通过更新 Azure 虚拟机缩放集、更新端口然后再次配置运行状况探测来删除运行状况探测。

## <a name="next-steps"></a>后续步骤

在本文中，您部署了一个虚拟机缩放集与现有的 Azure 负载均衡器。  要了解有关虚拟机缩放集和负载均衡器的更多详细信息，请参阅：

- [什么是 Azure 负载均衡器？](load-balancer-overview.md)
- [什么是虚拟机规模集？](../virtual-machine-scale-sets/overview.md)
                                