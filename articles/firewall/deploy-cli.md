---
title: 部署和配置 Azure 防火墙使用 Azure CLI
description: 在本文中，您将了解如何部署和配置 Azure 防火墙使用 Azure CLI。
services: firewall
author: vhorne
ms.service: firewall
ms.date: 06/11/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: b40ac789fbc331e779e85462724e5c8a8e9bce47
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083348"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>部署和配置 Azure 防火墙使用 Azure CLI

控制出站网络访问是整个网络安全计划的重要组成部分。 例如，你可能想要限制对网站的访问， 或者限制可以访问的出站 IP 地址和端口。

可以控制 Azure 子网的出站网络访问的一种方法是使用 Azure 防火墙。 使用 Azure 防火墙，可以配置：

* 应用程序规则，用于定义可从子网访问的完全限定域名 (FQDN)。
* 网络规则，用于定义源地址、协议、目标端口和目标地址。

将网络流量路由到用作子网默认网关的防火墙时，网络流量受到配置的防火墙规则的控制。

在本文中，创建具有三个子网，以便于部署的简化单个 VNet。 对于生产部署，[中心辐射型模型](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)建议。 防火墙是在其自身的 VNet 中。 工作负荷服务器在包含一个或多个子网的同一区域中的对等 VNet 内。

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

如果您愿意，可以完成此过程使用[Azure 门户](tutorial-firewall-deploy-portal.md)或[Azure PowerShell](deploy-ps.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必备组件

### <a name="azure-cli"></a>Azure CLI

如果选择在本地安装并使用 CLI，请运行 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 **az --version**。 有关安装或升级的信息，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="set-up-the-network"></a>设置网络

首先，创建一个资源组用于包含部署防火墙所需的资源。 然后创建 VNet、子网和测试服务器。

### <a name="create-a-resource-group"></a>创建资源组

资源组包含部署的所有资源。

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>创建 VNet

此虚拟网络具有三个子网。

> [!NOTE]
> AzureFirewallSubnet 子网的最小大小为 /26。

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/24
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
出现提示时，键入虚拟机的密码。

创建 Srv 跳转虚拟机。

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



创建一个 NIC 用于 Srv 工作具有特定的 DNS 服务器 IP 地址和没有公共 IP 地址可供测试。

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

现在，创建工作负荷的虚拟机。
出现提示时，键入虚拟机的密码。

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

现在部署到虚拟网络中防火墙。

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

创建表，请禁用 BGP 路由传播

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

将关联到子网的路由表

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>配置应用程序规则

应用程序规则允许对 www.google.com 出站访问。

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

网络规则允许在端口 53 (DNS) 的两个 IP 地址对出站访问。

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

1. 请注意的专用 IP 地址**Srv 工作**虚拟机：

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. 将远程桌面连接到 **Srv-Jump** 虚拟机，然后登录。 在此处，打开远程桌面连接到**Srv 工作**专用 IP 地址并登录。

3. 上**SRV 工作**，打开 PowerShell 窗口并运行以下命令：

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   这两个命令应返回显示 DNS 查询将获取通过防火墙的答案。

1. 运行以下命令：

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Www.google.com 请求应该会成功，而且 www.microsoft.com 请求应当会失败。 此示例演示按预期运行你的防火墙规则。

现已验证防火墙规则可正常工作：

* 可以使用配置的外部 DNS 服务器解析 DNS 名称。
* 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

## <a name="clean-up-resources"></a>清理资源

可以将你的防火墙资源保留为下一步的教程中，或如果不再需要删除**测试转发 RG**要删除防火墙相关的所有资源的资源组：

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>后续步骤

* [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)
