---
title: 快速入门：创建具有可用性区域的 Azure 防火墙 - 资源管理器模板
description: 使用模板部署 Azure 防火墙。 该虚拟网络有一个包含三个子网的 VNet。 将部署两个 Windows Server 虚拟机；一个跳转盒和一个服务器。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 9b9b7926caa717c1a02988ac7a927bd9bd39d52a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683704"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>快速入门：部署具有可用性区域的 Azure 防火墙 - 资源管理器模板

在本快速入门中，你将使用资源管理器模板在三个可用性区域中部署 Azure 防火墙。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

此模板创建带防火墙的测试网络环境。 网络具有一个虚拟网络 (VNet)，其中包含三个子网：AzureFirewallSubnet、ServersSubnet 和 JumpboxSubnet。 ServersSubnet 和 JumpboxSubnet 子网均包含一个单个、双核 Windows Server 虚拟机。

防火墙在 AzureFirewallSubnet 子网中，并配置有一个应用程序规则集合，其中包含允许访问 `www.microsoft.com` 的单个规则。

用户定义的一个路由，它引导来自 ServersSubnet 子网的网络流量穿过应用了防火墙规则的防火墙。

有关 Azure 防火墙的详细信息，请参阅[使用 Azure 门户部署和配置 Azure 防火墙](tutorial-firewall-deploy-portal.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-firewall-with-availability-zones"></a>创建具有可用性区域的 Azure 防火墙

此模板创建具有可用性区域的 Azure 防火墙，以及用于支持 Azure 防火墙的必要资源。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox)。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>部署模板

将资源管理器模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 该模板将创建 Azure 防火墙、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. 在门户中的“创建具有区域的 Azure 防火墙的沙盒设置”页上，键入或选择以下值：
   - **资源组**：选择“新建”，键入资源组的名称，然后选择“确定”。 
   - **虚拟网络名称**：键入新 VNet 的名称。 
   - **管理员用户名**：键入管理员用户帐户的用户名。
   - **管理员密码**：键入管理员密码。 

3. 阅读条款和条件，选择“我同意上述条款和条件”，然后选择“购买”。  部署可能需要 10 分钟或更长时间才能完成。

## <a name="review-deployed-resources"></a>查看已部署的资源

浏览使用防火墙创建的资源。

若要了解模板中防火墙的 JSON 语法和属性，请参阅 [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="clean-up-resources"></a>清理资源

当不再需要这些资源时，可以通过运行 PowerShell 命令 `Remove-AzResourceGroup` 来删除资源组、防火墙和所有相关的资源。 若要删除名为 MyResourceGroup 的资源组，请运行： 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

如果计划继续学习防火墙监视教程，请不要删除资源组和防火墙。 

## <a name="next-steps"></a>后续步骤

接下来，可以监视 Azure 防火墙日志。

> [!div class="nextstepaction"]
> [教程：监视 Azure 防火墙日志](tutorial-diagnostics.md)