---
title: 快速入门：使用资源管理器模板定向 Web 流量
titleSuffix: Azure Application Gateway
description: 了解如何使用资源管理器模板创建 Azure 应用程序网关，以便将 Web 流量定向到后端池中的虚拟机。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 963f728db55f587f7ae72aec702a099882cf401a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86249353"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---arm-template"></a>快速入门：使用 Azure 应用程序网关定向 Web 流量 - ARM 模板

在本快速入门中，使用 Azure 资源管理器模板（ARM 模板）创建 Azure 应用程序网关。 然后，我们对应用程序网关进行测试，确保其正常运行。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 完成本快速入门。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

为简单起见，此模板创建了一项简单设置，其中包含一个公共的前端 IP、一个在应用程序网关上托管单个站点的基本侦听器、一项基本的请求路由规则，以及两个位于后端池的虚拟机。

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/ag-docs-qs/)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)：一个用于应用程序网关，两个用于虚拟机。
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：两个虚拟机
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：两个用于虚拟机
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions)：用于配置 IIS 和网页

## <a name="deploy-the-template"></a>部署模板

将 ARM 模板部署到 Azure：

1. 选择“部署到 Azure”，登录到 Azure 并打开模板。 该模板在运行 IIS 的后端池中创建应用程序网关、网络基础结构和两个虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. 选择或创建资源组，键入虚拟机管理员用户名和密码。
3. 选择“查看 + 创建”，然后选择“创建”。 

   部署可能需要 20 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

虽然不需 IIS 即可创建应用程序网关，但安装它可以验证 Azure 是否已成功创建应用程序网关。 使用 IIS 测试应用程序网关：

1. 在“概述”页面上查找应用程序网关的公共 IP 地址![记录应用程序网关公共 IP 地址](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)或者，可以选择“所有资源”，在搜索框中输入“myAGPublicIPAddress”，然后在搜索结果中选择该地址 。 Azure 会在“概览”页上显示公共 IP 地址。
2. 复制公共 IP 地址，然后将其粘贴到浏览器的地址栏中，以便浏览该 IP 地址。
3. 检查响应。 有效响应验证应用程序网关是否已成功创建，以及是否能够成功连接后端。

   ![测试应用程序网关](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   多次刷新浏览器就会看到与 myVM1 和 myVM2 的连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要通过应用程序网关创建的资源，请删除资源组。 这样会删除应用程序网关和所有相关的资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [通过 Azure CLI 使用应用程序网关管理 Web 流量](./tutorial-manage-web-traffic-cli.md)
