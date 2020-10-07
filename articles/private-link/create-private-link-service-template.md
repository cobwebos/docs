---
title: 在 Azure 专用链接中创建专用链接服务
description: 在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用链接服务。
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/29/2020
ms.author: allensu
ms.openlocfilehash: dce80d70af7cd711cf852a60b98ad65b6d21117f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88705242"
---
# <a name="quickstart-create-a-private-link-service-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建专用链接服务

在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用链接服务。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](create-private-link-service-portal.md)、[Azure PowerShell](create-private-link-service-powershell.md) 或 [Azure CLI](create-private-link-service-cli.md) 完成本快速入门。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

此模板创建专用链接服务。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-privatelink-service/)。

:::code language="json" source="~/quickstart-templates/101-privatelink-service/azuredeploy.json":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)：每个虚拟机都有一个虚拟网络。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers)：公开托管服务的虚拟机的负载均衡器。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：有两个网络接口，每个虚拟机对应一个接口。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：有两台虚拟机，一台用于托管服务，另一台用于测试到专用终结点的连接。
- [**Microsoft.Compute/virtualMachines/extensions**](/azure/templates/Microsoft.Compute/virtualMachines/extensions)：安装 Web 服务器的扩展。
- [**Microsoft.Network/privateLinkServices**](/azure/templates/microsoft.network/privateLinkServices)：用于公开服务的专用链接服务。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses)：有两个公共 IP 地址，每个虚拟机对应一个地址。
- [**Microsoft.Network/privateendpoints**](/azure/templates/microsoft.network/privateendpoints)：用于访问服务的专用终结点。

## <a name="deploy-the-template"></a>部署模板

下面介绍如何将 ARM 模板部署到 Azure：

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”。 此模板创建虚拟机、标准负载均衡器、专用链接服务、专用终结点、网络和要验证的虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-privatelink-service%2Fazuredeploy.json)

2. 选择或创建资源组。
3. 键入虚拟机管理员用户名和密码。
4. 阅读条款和条件声明。 如果你同意，请选择“我同意上述条款和条件” > “购买” 。

## <a name="validate-the-deployment"></a>验证部署

> [!NOTE]
> ARM 模板为虚拟机 myConsumerVm<b>{uniqueid}</b> 资源生成唯一名称。 用生成的值替换 {uniqueid}。

### <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM myConsumerVm{uniqueid}，如下所示：

1.  在门户的搜索栏中，输入 myConsumerVm{uniqueid}。

2.  选择“连接”。 “连接到虚拟机”随即打开。

3.  选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( _.rdp_) 文件，并将其下载到计算机。

4.  打开下载的 .rdp 文件。

    a. 出现提示时，选择“连接”。

    b. 输入创建 VM 时指定的用户名和密码。
    
    > [!NOTE]
    > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

5.  选择“确定”。

6.  你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

7.  VM 桌面出现后，将其最小化以返回到本地桌面。

### <a name="access-the-http-service-privately-from-the-vm"></a>以非公开方式从 VM 访问 http 服务

下面介绍了如何使用专用终结点从 VM 连接到 http 服务。

1.  转到 myConsumerVm{uniqueid} 的远程桌面。
2.  打开浏览器并输入专用终结点地址： `http://10.0.0.5/` 。
3.  默认 IIS 页随即出现。

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用专用链接服务创建的资源，请删除资源组。 这会删除专用链接服务和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用链接](private-link-overview.md)。
