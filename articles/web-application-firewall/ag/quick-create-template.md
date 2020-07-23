---
title: 快速入门：在应用程序网关上创建 Azure WAF v2 - 资源管理器模板
titleSuffix: Azure Application Gateway
description: 了解如何使用资源管理器模板在 Azure 应用程序网关上创建 Web 应用程序防火墙 v2。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 6759071e73adfd3af4ac780da6db3a0e6e967ea1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81617981"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>快速入门：在应用程序网关上创建 Azure WAF v2 - 资源管理器模板

在本快速入门中，你将使用资源管理器模板在应用程序网关上创建 Azure Web 应用程序防火墙 v2。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-web-application-firewall"></a>创建 Web 应用程序防火墙

此模板在 Azure 应用程序网关上创建简单的 Web 应用程序防火墙 v2。 这包括公共 IP 前端 IP 地址、HTTP 设置、包含端口 80 上的基本侦听器的规则，以及后端池。 将创建一个包含自定义规则的 WAF 策略，以根据 IP 地址匹配类型阻止发往后端池的流量。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)：一个用于应用程序网关，两个用于虚拟机。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：两个虚拟机
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：两个用于虚拟机
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions)：用于配置 IIS 和网页

### <a name="deploy-the-template"></a>部署模板

将资源管理器模板部署到 Azure：

1. 选择“部署到 Azure”  ，登录到 Azure 并打开模板。 该模板在运行 IIS 的后端池中创建应用程序网关、网络基础结构和两个虚拟机。

   [![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. 选择或创建资源组。
3. 选择“我同意上述条款和条件”，然后选择“购买”   。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

虽然无需 IIS 即可创建应用程序网关，但在后端服务器上安装 IIS 可以验证 Azure 是否已在应用程序网关上成功创建 WAF v2。 

使用 IIS 测试应用程序网关：

1. 在“概述”页面上查找应用程序网关的公共 IP 地址![记录应用程序网关公共 IP 地址](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)或者，可以选择“所有资源”，在搜索框中输入“myAGPublicIPAddress”，然后在搜索结果中选择该地址    。 Azure 会在“概览”页上显示公共 IP 地址。 
2. 复制公共 IP 地址，然后将其粘贴到浏览器的地址栏中，以便浏览该 IP 地址。
3. 检查响应。 “403 禁止”响应表示 WAF 已成功创建，并正在阻止与后端池建立连接。 
4. 将自定义规则更改为“允许流量”。 
  运行以下 Azure PowerShell 脚本（请替换资源组名称）：
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   多次刷新浏览器就会看到与 myVM1 和 myVM2 的连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 这样会删除应用程序网关和所有相关的资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure 门户创建具有 Web 应用程序防火墙的应用程序网关](application-gateway-web-application-firewall-portal.md)