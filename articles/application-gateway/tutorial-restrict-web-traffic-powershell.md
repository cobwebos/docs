---
title: 使用 Web 应用程序防火墙限制 Web 流量 - Azure PowerShell
description: 了解如何通过 Azure PowerShell 在应用程序网关上使用 Web 应用程序防火墙限制 Web 流量。
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e962d76bc82edabf750af52c50ec45ed9ed76e17
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596834"
---
# <a name="enable-web-application-firewall-using-azure-powershell"></a>使用 Azure PowerShell 启用 Web 应用程序防火墙

> [!div class="op_single_selector"]
>
> - [Azure 门户](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

在[应用程序网关](overview.md)上使用 [Web 应用程序防火墙](waf-overview.md) (WAF) 限制流量。 WAF 使用 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 规则保护应用程序。 这些规则包括针对各种攻击（例如 SQL 注入、跨站点脚本攻击和会话劫持）的保护。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置网络
> * 创建启用 WAF 的应用程序网关
> * 创建虚拟机规模集
> * 创建存储帐户和配置诊断

![Web 应用程序防火墙示例](./media/tutorial-restrict-web-traffic-powershell/scenario-waf.png)

如果需要，也可以使用 [Azure CLI](tutorial-restrict-web-traffic-cli.md) 完成本教程中的步骤。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块版本 1.0.0 或更高版本。 运行 `Get-Module -ListAvailable Az` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>创建网络资源 

使用 [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建名为 *myBackendSubnet* 和 *myAGSubnet* 的子网配置。 使用 [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) 和子网配置创建名为 myVNet 的虚拟网络  。 最后使用 [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) 创建名为 myAGPublicIPAddress 的公共 IP 地址  。 这些资源用于提供与应用程序网关及其关联资源的网络连接。

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>创建应用程序网关

本部分将创建支持应用程序网关的资源，然后最终创建应用程序网关和 WAF。 创建的资源包括：

- *IP 配置和前端端口* - 将先前创建的子网关联到应用程序网关，并分配一个端口以用于访问它。
- *默认池* - 所有应用程序网关必须至少具有一个后端服务器池。
- *默认侦听器和规则* - 默认侦听器侦听已分配的端口上的流量，默认规则将流量发送到默认池。

### <a name="create-the-ip-configurations-and-frontend-port"></a>创建 IP 配置和前端端口

使用 [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) 将前面创建的 myAGSubnet 关联到应用程序网关  。 使用 [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) 将 *myAGPublicIPAddress* 分配给应用程序网关。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[1]

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-backend-pool-and-settings"></a>创建后端池和设置

使用 [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) 为应用程序网关创建名为 *appGatewayBackendPool* 的后端池。 使用 [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) 配置后端地址池的设置。

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>创建默认侦听器和规则

应用程序网关需要侦听器才能适当地将流量路由到后端地址池。 在此示例中，将一个创建基本侦听器以侦听根 URL 上的流量。 

使用 [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) 以及前面创建的前端配置和前端端口创建名为 mydefaultListener 的侦听器  。 侦听器需要使用规则来了解哪个后端池使用传入流量。 使用 [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) 创建一个名为 rule1 的基本规则  。

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name mydefaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway-with-the-waf"></a>创建具有 WAF 的应用程序网关

现在已创建所需的支持资源，请使用 [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) 为应用程序网关指定参数。 使用 [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) 指定 WAF 配置。 然后，使用 [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) 创建名为 *myAppGateway* 的应用程序网关。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name WAF_Medium `
  -Tier WAF `
  -Capacity 2

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration `
  -Enabled $true `
  -FirewallMode "Detection"

$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -WebApplicationFirewallConfig $wafConfig
```

## <a name="create-a-virtual-machine-scale-set"></a>创建虚拟机规模集

在此示例中，将创建虚拟机规模集，以便为应用程序网关的后端池提供服务器。 配置 IP 设置时将规模集分配给后端池。

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id

$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic

Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage

Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss

Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig

New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>安装 IIS

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss

Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>创建存储帐户和配置诊断

在本教程中，应用程序网关使用存储帐户来存储用于检测和防范目的的数据。 也可以使用 Azure Monitor 日志或事件中心来记录数据。

### <a name="create-the-storage-account"></a>创建存储帐户

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建名为 *myagstore1* 的存储帐户。

```azurepowershell-interactive
$storageAccount = New-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1 `
  -Location eastus `
  -SkuName "Standard_LRS"
```

### <a name="configure-diagnostics"></a>配置诊断

使用 [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) 配置诊断以将数据记录到 ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog 和 ApplicationGatewayFirewallLog 日志中。

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$store = Get-AzStorageAccount `
  -ResourceGroupName myResourceGroupAG `
  -Name myagstore1

Set-AzDiagnosticSetting `
  -ResourceId $appgw.Id `
  -StorageAccountId $store.Id `
  -Categories ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog, ApplicationGatewayFirewallLog `
  -Enabled $true `
  -RetentionEnabled $true `
  -RetentionInDays 30
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

可以使用 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) 获取应用程序网关的公共 IP 地址。 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![在应用程序网关中测试基 URL](./media/tutorial-restrict-web-traffic-powershell/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、应用程序网关和所有相关资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 设置网络
> * 创建启用 WAF 的应用程序网关
> * 创建虚拟机规模集
> * 创建存储帐户和配置诊断

> [!div class="nextstepaction"]
> [使用 SSL 终端创建应用程序网关](./tutorial-ssl-powershell.md)
