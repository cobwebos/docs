---
title: 快速入门：使用 Azure 防火墙管理器保护虚拟中心安全 - 资源管理器模板
description: 了解如何使用 Azure 防火墙管理器保护虚拟中心安全。
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: bfa957d8fb0eeb8020472026e18c41dc6e65fdc3
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705752"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>快速入门：使用 Azure 防火墙管理器保护虚拟中心安全 - ARM 模板

在本快速入门中，使用 Azure 资源管理器模板（ARM 模板），通过 Azure 防火墙管理器来保护虚拟中心的安全。 部署的防火墙具有允许连接到 `www.microsoft.com` 的应用程序规则。 部署了两个 Windows Server 2019 虚拟机以测试防火墙。 一个跳转服务器用于连接到工作负载服务器。 从工作负载服务器，只能连接到 `www.microsoft.com`。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

有关 Azure 防火墙管理器的详细信息，请参阅[什么是 Azure 防火墙管理器？](overview.md)。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

此模板使用 Azure 防火墙管理器以及支持该场景所需的资源创建了一个安全虚拟中心。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/fwm-docs-qs/)。

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>部署模板

将 ARM 模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 此模板会创建 Azure 防火墙、虚拟 WAN 和虚拟中心、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. 在门户中的“安全虚拟中心”页上，键入或选择以下值：
   - 订阅：从现有订阅中选择 
   - 资源组：从现有资源组中选择，或者选择“新建”，然后选择“确定”。 
   - 位置：选择一个位置
   - 管理员用户名：键入管理员用户帐户的用户名 
   - 管理员密码：键入管理员密码或密钥

3. 选择“查看 + 创建”，然后选择“创建” 。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

现在，测试防火墙以确认它可按预期工作。

1. 在 Azure 门户中，查看“Workload-Srv”虚拟机的网络设置并记下专用 IP 地址。
2. 将远程桌面连接到“Jump-Srv”虚拟机，然后登录。 在这里，打开与“Workload-Srv”专用 IP 地址建立的远程桌面连接。

3. 打开 Internet Explorer 并浏览到 `www.microsoft.com`。
4. 出现 Internet Explorer 安全警报时，请选择“确定” > “关闭”。 

   应会看到 Microsoft 主页。

5. 浏览到 `www.google.com`。

   防火墙应会阻止你访问。

现已验证防火墙规则可正常工作：

- 可以浏览到一个允许的 FQDN，但不能浏览到其他任何 FQDN。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为防火墙创建的资源，请删除资源组。 这会删除该防火墙和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解安全合作伙伴提供程序](trusted-security-partners.md)
