---
title: 快速入门：创建具有多个公共 IP 地址的 Azure 防火墙 - 资源管理器模板
description: 了解如何使用资源管理器模板创建具有多个公共 IP 地址的 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: b81362f191e6317aa7ffa974a6d432b0c7514d8f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680536"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>快速入门：创建具有多个公共 IP 地址的 Azure 防火墙 - 资源管理器模板

在本快速入门中，我们使用资源管理器模板部署具有多个公共 IP 地址的 Azure 防火墙。 部署的防火墙具有 NAT 规则收集规则，这些规则允许通过 RDP 连接与两个 Windows Server 2019 虚拟机进行连接。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

若要详细了解具有多个公共 IP 地址的 Azure 防火墙，请参阅[使用 Azure PowerShell 部署具有多个公共 IP 地址的 Azure 防火墙](deploy-multi-public-ip-powershell.md)。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-firewall"></a>创建 Azure 防火墙

此模板创建具有两个公共 IP 地址的 Azure 防火墙，以及用于支持 Azure 防火墙的必要资源。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/fw-docs-qs)。

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>部署模板

将资源管理器模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 该模板将创建 Azure 防火墙、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. 在门户中的“创建具有多个公共 IP 地址的 Azure 防火墙”页上，键入或选择以下值：
   - 订阅：从现有订阅中选择 
   - 资源组：从现有资源组中选择，或者选择“新建”，然后选择“确定”。 
   - 位置：选择一个位置
   - 管理员用户名：键入管理员用户帐户的用户名 
   - 管理员密码：键入管理员密码或密钥

3. 选择“我同意上述条款和条件”，然后选择“购买” 。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

在 Azure 门户中，查看已部署的资源。 记下防火墙公共 IP 地址。  

使用“远程桌面连接”连接到防火墙公共 IP 地址。 成功的连接演示了允许连接到后端服务器的防火墙 NAT 规则。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为防火墙创建的资源，请删除资源组。 这会删除该防火墙和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)