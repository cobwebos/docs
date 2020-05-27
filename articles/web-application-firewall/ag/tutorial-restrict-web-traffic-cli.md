---
title: 启用 Web 应用程序防火墙 - Azure CLI
description: 了解如何通过 Azure CLI 在应用程序网关上使用 Web 应用程序防火墙限制 Web 流量。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 05/21/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: a429d1a0f426002595f347794bc1cb97a649d6f6
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770187"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>使用 Azure CLI 启用 Web 应用程序防火墙

你可以使用 [Web 应用程序防火墙](ag-overview.md) (WAF) 限制应用程序网关上的流量。 WAF 使用 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 规则保护应用程序。 这些规则包括针对各种攻击（例如 SQL 注入、跨站点脚本攻击和会话劫持）的保护。

在本文中，学习如何：

> [!div class="checklist"]
> * 设置网络
> * 创建启用 WAF 的应用程序网关
> * 创建虚拟机规模集
> * 创建存储帐户和配置诊断

![Web 应用程序防火墙示例](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

如果需要，可以使用 [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) 完成此过程。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

根据本文的要求，如果选择在本地安装并使用 CLI，则需要运行 Azure CLI 2.0.4 或更高版本。 要查找版本，请运行 `az --version`。 如果需要进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [az group create](/cli/azure/group#az-group-create) 创建名为 *myResourceGroupAG* 的 Azure 资源组。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>创建网络资源

虚拟网络和子网用于提供与应用程序网关及其关联资源的网络连接。 创建名为 myVNet 的虚拟网络和名为 myAGSubnet 的子网。 创建名为 myAGPublicIPAddress 的公共 IP 地址。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>创建具有 WAF 的应用程序网关

可以使用 [az network application-gateway create](/cli/azure/network/application-gateway) 创建名为 *myAppGateway* 的应用程序网关。 使用 Azure CLI 创建应用程序网关时，请指定配置信息，例如容量、sku 和 HTTP 设置。 将应用程序网关分配给 myAGSubnet 和 myAGPublicIPAddress。

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

创建应用程序网关可能需要几分钟时间。 创建应用程序网关后，可以看到它的这些新功能：

- *appGatewayBackendPool* - 应用程序网关必须至少具有一个后端地址池。
- *appGatewayBackendHttpSettings* - 指定将端口 80 和 HTTP 协议用于通信。
- *appGatewayHttpListener* - 与 *appGatewayBackendPool* 关联的默认侦听器。
- *appGatewayFrontendIP* - 将 *myAGPublicIPAddress* 分配给 *appGatewayHttpListener*。
- *rule1* - 与 *appGatewayHttpListener* 关联的默认路由规则。

## <a name="create-a-virtual-machine-scale-set"></a>创建虚拟机规模集

在此示例中，将创建虚拟机规模集，以便为应用程序网关的后端池提供两个服务器。 规模集中的虚拟机与 *myBackendSubnet* 子网相关联。 若要创建规模集，可以使用 [az vmss create](/cli/azure/vmss#az-vmss-create)。

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>安装 NGINX

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>创建存储帐户和配置诊断

在本文中，应用程序网关使用存储帐户来存储用于检测和防范目的的数据。 也可以使用 Azure Monitor 日志或事件中心来记录数据。 

### <a name="create-a-storage-account"></a>创建存储帐户

使用 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 创建名为 *myagstore1* 的存储帐户。

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>配置诊断

配置诊断以将数据记录到 ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog 和 ApplicationGatewayFirewallLog 日志中。 将 `<subscriptionId>` 替换为你的订阅标识符，然后使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 配置诊断。

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

若要获取应用程序网关的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![在应用程序网关中测试基 URL](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>清理资源

当不再需要资源组、应用程序网关以及所有相关资源时，请将其删除。

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>后续步骤

[自定义 Web 应用程序防火墙规则](application-gateway-customize-waf-rules-portal.md)
