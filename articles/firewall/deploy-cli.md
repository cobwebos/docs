---
title: 使用 Azure CLI 部署和配置 Azure 防火墙
description: 本文介绍如何使用 Azure CLI 部署和配置 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 94db17405457be91795d1588bee68a0deea68246
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114815"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>使用 Azure CLI 部署和配置 Azure 防火墙

控制出站网络访问是整个网络安全计划的重要组成部分。 例如，你可能想要限制对网站的访问， 或者限制可以访问的出站 IP 地址和端口。

可以控制 Azure 子网的出站网络访问的一种方法是使用 Azure 防火墙。 使用 Azure 防火墙，可以配置：

* 应用程序规则，用于定义可从子网访问的完全限定域名 (FQDN)。 FQDN 还可以[包括 SQL 实例](sql-fqdn-filtering.md)。
* 网络规则，用于定义源地址、协议、目标端口和目标地址。

将网络流量路由到用作子网默认网关的防火墙时，网络流量受到配置的防火墙规则的控制。

在本文中，你将创建一个包含三个子网的简化 VNet，以便于部署。 对于生产部署，建议使用[中心辐射模型](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)。 防火墙位于其自身的 VNet 中。 工作负荷服务器在包含一个或多个子网的同一区域中的对等 VNet 内。

* **AzureFirewallSubnet** - 防火墙在此子网中。
* **Workload-SN** - 工作负荷服务器在此子网中。 此子网的网络流量通过防火墙。
* **Jump-SN** -“跳转”服务器在此子网中。 可以使用远程桌面连接到跳转服务器中的公共 IP 地址。 然后，可在跳转服务器中（使用另一个远程桌面）连接到工作负荷服务器。

![教程网络基础结构](media/tutorial-firewall-rules-portal/Tutorial_network.png)

在本文中，学习如何：

> [!div class="checklist"]
> * 设置测试网络环境
> * 部署防火墙
> * 创建默认路由
> * 配置一个应用程序规则以允许访问 www.google.com
> * 配置网络规则，以允许访问外部 DNS 服务器
> * 测试防火墙

如果你愿意，可以使用 [Azure 门户](tutorial-firewall-deploy-portal.md)或 [Azure PowerShell](deploy-ps.md) 完成此过程。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>先决条件

### <a name="azure-cli"></a>Azure CLI

如果选择在本地安装并使用 CLI，请运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 **az --version**。 有关安装或升级的信息，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

安装 Azure 防火墙扩展：

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>设置网络

首先，创建一个资源组用于包含部署防火墙所需的资源。 然后创建 VNet、子网和测试服务器。

### <a name="create-a-resource-group"></a>创建资源组

资源组包含用于部署的所有资源。

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>创建 VNet

此虚拟网络有三个子网。

> [!NOTE]
> AzureFirewallSubnet 子网的大小为/26。 有关子网大小的详细信息, 请参阅[Azure 防火墙常见问题解答](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>创建虚拟机

现在，创建跳转虚拟机和工作负荷虚拟机，并将其放入相应的子网。
出现提示时，请键入虚拟机的密码。

创建 Srv-Jump 虚拟机。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



使用特定的 DNS 服务器 IP 地址为 Srv-Work 创建 NIC，并且不使用公共 IP 地址进行测试。

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

现在，创建工作负荷虚拟机。
出现提示时，请键入虚拟机的密码。

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>部署防火墙

现在，将防火墙部署到虚拟网络。

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

记下专用 IP 地址。 稍后在创建默认路由时需要用到此地址。

## <a name="create-a-default-route"></a>创建默认路由

创建一个禁用 BGP 路由传播的表

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

创建路由。

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

将路由表关联到子网

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>配置应用程序规则

此应用程序规则允许对 www.google.com 进行出站访问。

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure 防火墙包含默认情况下允许的基础结构 FQDN 的内置规则集合。 这些 FQDN 特定于平台，不能用于其他目的。 有关详细信息，请参阅[基础结构 FQDN](infrastructure-fqdns.md)。

## <a name="configure-a-network-rule"></a>配置网络规则

此网络规则允许对端口 53 (DNS) 上的两个 IP 地址进行出站访问。

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>测试防火墙

现在测试防火墙，以确认它是否按预期方式工作。

1. 记下 **Srv-Work** 虚拟机的专用 IP 地址：

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. 将远程桌面连接到 **Srv-Jump** 虚拟机，然后登录。 在该虚拟机中，与 **Srv-Work** 专用 IP 地址建立远程桌面连接并登录。

3. 在 **SRV-Work** 上，打开 PowerShell 窗口并运行以下命令：

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   这两个命令都应返回应答，表明 DNS 查询正在通过防火墙。

1. 运行以下命令：

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   [www.google.com](www.google.com) 请求应成功，而 www.microsoft.com 请求应失败。 这表明防火墙规则按预期运行。

现已验证防火墙规则可正常工作：

* 可以使用配置的外部 DNS 服务器解析 DNS 名称。
* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

## <a name="clean-up-resources"></a>清理资源

可以将防火墙资源保留到下一教程使用。不再需要时，请删除 **Test-FW-RG** 资源组，以删除与防火墙相关的所有资源：

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>后续步骤

* [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
