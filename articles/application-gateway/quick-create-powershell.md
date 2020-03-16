---
title: 快速入门：使用 PowerShell 定向 Web 流量
titleSuffix: Azure Application Gateway
description: 了解如何使用 Azure PowerShell 创建 Azure 应用程序网关，以便将 Web 流量重定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: abb38dfc342c8ff692ed1a3a05376b5dcefe8a3d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399555"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>快速入门：通过 Azure PowerShell 使用 Azure 应用程序网关定向 Web 流量

在本快速入门中，你将使用 Azure PowerShell 创建一个应用程序网关。 然后对其进行测试以确保其正常运行。 

该应用程序网关将应用程序 Web 流量定向到后端池中的特定资源。 你将向端口分配侦听器，创建规则，并向后端池中添加资源。 为简单起见，本文使用了带有公共前端 IP 的简单设置、一个在应用程序网关上托管单个站点的基本侦听器、一个基本的请求路由规则，以及后端池中的两台虚拟机。

还可以使用 [Azure CLI](quick-create-cli.md) 或 [Azure 门户](quick-create-portal.md)完成本快速入门。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Azure PowerShell 1.0.0 或更高版本](/powershell/azure/install-az-ps)（如果在本地运行 Azure PowerShell）。

## <a name="connect-to-azure"></a>连接到 Azure

若要连接到 Azure，请运行 `Connect-AzAccount`。

## <a name="create-a-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 可以使用现有资源组，也可以创建新组。

若要创建新的资源组，请使用 `New-AzResourceGroup` cmdlet： 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>创建网络资源

Azure 需要一个虚拟网络才能在创建的资源之间通信。  应用程序网关子网只能包含应用程序网关。 不允许其他资源。  可以创建应用程序网关的新子网，也可以使用现有的子网。 在本示例中创建两个子网：一个用于应用程序网关，另一个用于后端服务器。 可以根据用例将应用程序网关的前端 IP 配置为公共或专用。 本示例将选择公共前端 IP。

1. 使用 `New-AzVirtualNetworkSubnetConfig` 创建子网配置。
2. 使用 `New-AzVirtualNetwork` 创建使用这些子网配置的虚拟网络。 
3. 使用 `New-AzPublicIpAddress` 创建公共 IP 地址。 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>创建应用程序网关

### <a name="create-the-ip-configurations-and-frontend-port"></a>创建 IP 配置和前端端口

1. 使用 `New-AzApplicationGatewayIPConfiguration` 创建配置，用以将创建的子网与应用程序网关相关联。 
2. 使用 `New-AzApplicationGatewayFrontendIPConfig` 创建配置，用以将前面创建的公共 IP 地址分配给应用程序网关。 
3. 使用 `New-AzApplicationGatewayFrontendPort` 分配端口 80 以访问应用程序网关。

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>创建后端池

1. 使用 `New-AzApplicationGatewayBackendAddressPool` 创建应用程序网关的后端池。 后端池暂时将为空，在下一部分中创建后端服务器 NIC 时，会将它们添加到后端池。
2. 使用 `New-AzApplicationGatewayBackendHttpSetting` 配置后端池的设置。

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>创建侦听器并添加规则

Azure 需要一个侦听器才能使应用程序网关以适当方式将流量路由到后端池。 Azure 还需要一项规则，使侦听器了解将哪个后端池用于传入流量。 

1. 使用 `New-AzApplicationGatewayHttpListener` 以及前面创建的前端配置和前端端口创建侦听器。 
2. 使用 `New-AzApplicationGatewayRequestRoutingRule` 创建名为 *rule1* 的规则。 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>创建应用程序网关

创建所需的支持资源以后，即可创建应用程序网关：

1. 使用 `New-AzApplicationGatewaySku` 指定应用程序网关的参数。
2. 使用 `New-AzApplicationGateway` 创建应用程序网关。

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>后端服务器

现在已创建了应用程序网关，接下来创建将托管网站的后端虚拟机。 后端可以包含 NIC、虚拟机规模集、公共 IP、内部 IP、完全限定的域名 (FQDN) 和多租户后端（例如 Azure 应用服务）。 在此示例中，将创建两个虚拟机，供 Azure 用作应用程序网关的后端服务器。 还可以在虚拟机上安装 IIS，以验证 Azure 是否已成功创建应用程序网关。

#### <a name="create-two-virtual-machines"></a>创建两个虚拟机

1. 使用 `Get-AzApplicationGatewayBackendAddressPool` 获取最近创建的应用程序网关后端池配置。
2. 使用 `New-AzNetworkInterface` 创建网络接口。
3. 使用 `New-AzVMConfig` 创建虚拟机配置。
4. 使用 `New-AzVM` 创建虚拟机。

运行以下代码示例来创建虚拟机时，Azure 会提示你输入凭据。 输入 *azureuser* 作为用户名并输入一个密码：
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>测试应用程序网关

虽然不需 IIS 即可创建应用程序网关，但本快速入门中安装了它，用来验证 Azure 是否已成功创建应用程序网关。 使用 IIS 测试应用程序网关：

1. 运行 `Get-AzPublicIPAddress` 获取应用程序网关的公共 IP 地址。 
2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 刷新浏览器时，应该会看到虚拟机的名称。 有效响应验证应用程序网关是否已成功创建，以及是否能够成功连接后端。

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![测试应用程序网关](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 删除资源组时，也会删除应用程序网关和及其所有的相关资源。 

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure PowerShell 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-powershell.md)

