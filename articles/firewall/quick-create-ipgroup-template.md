---
title: 快速入门：创建 Azure 防火墙和 IP 组 - 资源管理器模板
description: 了解如何使用资源管理器模板创建 Azure 防火墙和 IP 组。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 403aaafebcae680f337aeff551b81a80a9549252
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680553"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>快速入门：创建 Azure 防火墙和 IP 组 - 资源管理器模板

在本快速入门中，我们使用资源管理器模板部署一个 Azure 防火墙，其中包含在网络规则和应用程序规则中使用的示例 IP 组。 IP 组是顶级资源，可用于定义 IP 地址、范围与子网并将其组合到单个对象中。 在管理 Azure 防火墙规则中的 IP 地址时，IP 组非常有用。 可以手动输入 IP 地址，或者从文件导入。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-firewall-and-ip-groups"></a>创建 Azure 防火墙和 IP 组

此模板将创建 Azure 防火墙和 IP 组，以及为 Azure 防火墙提供支持所需的资源。

### <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox)。

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
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

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. 在门户中的“创建使用 IpGroups 的 Azure 防火墙”页上，键入或选择以下值：
   - 订阅：从现有订阅中选择 
   - 资源组：从现有资源组中选择，或者选择“新建”，然后选择“确定”。 
   - 位置：选择一个位置
   - 虚拟网络名称：键入新虚拟网络 (VNet) 的名称 
   - IP 组名称 1：键入 IP 组 1 的名称 
   - IP 组名称 2：键入 IP 组 2 的名称 
   - 管理员用户名：键入管理员用户帐户的用户名。 
   - 身份验证：选择“sshPublicKey”或“密码” 
   - 管理员密码：键入管理员密码或密钥

3. 选择“我同意上述条款和条件”，然后选择“购买” 。 部署可能需要 10 分钟或更长时间才能完成。

## <a name="review-deployed-resources"></a>查看已部署的资源

在 Azure 门户中，查看已部署的资源，尤其是使用 IP 组的防火墙规则。

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP 组。":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="网络规则。":::

若要了解模板中防火墙的 JSON 语法和属性，请参阅 [Microsoft.Network azureFirewalls template reference](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)（Microsoft.Network azureFirewalls 模板参考）。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为防火墙创建的资源，请删除资源组。 这会删除该防火墙和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-portal.md)