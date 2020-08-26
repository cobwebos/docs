---
title: 在 Azure 专用链接中创建专用终结点
description: 在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用终结点。
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 9fde76b86b290e1271f408cb7810e549dd9502a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071495"
---
# <a name="quickstart-create-a-private-endpoint-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建专用终结点

在本快速入门中，将使用 Azure 资源管理器模板（ARM 模板）创建专用终结点。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

还可以使用 [Azure 门户](create-private-endpoint-portal.md)、[Azure PowerShell](create-private-endpoint-powershell.md) 或 [Azure CLI](create-private-endpoint-cli.md) 完成本快速入门。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

需要一个具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

该模板为 Azure SQL 数据库的实例创建专用终结点。

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/)。

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

模板中定义了多个 Azure 资源：

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)：带有示例数据库的 SQL 数据库实例。
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)：示例数据库。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)：部署专用终结点的虚拟网络。
- [**Microsoft.Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints)：用于访问 SQL 数据库实例的专用终结点。
- [**Microsoft.Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones)：用于解析专用终结点 IP 地址的区域。
- [**Microsoft.Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft.Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups)：用于将专用终结点与专用 DNS 区域相关联的区域组。
- [**Microsoft.Network/publicIpAddresses**](/azure/templates/microsoft.network/publicIpAddresses)：用于访问虚拟机的公共 IP 地址。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)：虚拟机的网络接口。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)：用于测试专用终结点与 SQL 数据库实例的专用连接的虚拟机。

## <a name="deploy-the-template"></a>部署模板

下面介绍如何将 ARM 模板部署到 Azure：

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”。 该模板创建专用终结点、SQL 数据库实例、网络基础结构和要验证的虚拟机。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. 选择或创建资源组。
3. 键入 SQL 管理员登录名和密码。
4. 键入虚拟机管理员用户名和密码。
5. 阅读条款和条件声明。 如果你同意，请选择“我同意上述条款和条件” > “购买” 。 部署可能需要 20 分钟或更长时间才能完成。

## <a name="validate-the-deployment"></a>验证部署

> [!NOTE]
> ARM 模板为虚拟机 myVm<b>{uniqueid}</b> 资源和 SQL 数据库 sqlserver<b>{uniqueid}</b> 资源生成唯一名称。 用生成的值替换 {uniqueid}。

### <a name="connect-to-a-vm-from-the-internet"></a>从 Internet 连接到 VM

从 Internet 连接到 VM myVm{uniqueid}，如下所示：

1. 在门户的搜索栏中，输入 myVm{uniqueid}。

2. 选择“连接”。 “连接到虚拟机”随即打开。

3. 选择“下载 RDP 文件”。 Azure 会创建远程桌面协议 ( _.rdp_) 文件，并将其下载到计算机。

4. 打开下载的 .rdp 文件。

   a. 出现提示时，选择“连接”。

   b. 输入创建 VM 时指定的用户名和密码。

      > [!NOTE]
      > 可能需要选择“更多选择” > “使用其他帐户”，以指定在创建 VM 时输入的凭据。

5. 选择“确定”。

6. 你可能会在登录过程中收到证书警告。 如果收到证书警告，请选择“确定”或“继续” 。

7. VM 桌面出现后，将其最小化以返回到本地桌面。

### <a name="access-the-sql-database-server-privately-from-the-vm"></a>以私密方式从 VM 访问 SQL 数据库服务器

下面介绍了如何使用专用终结点从 VM 连接到 SQL 数据库服务器。

1.  在 myVM{uniqueid} 的远程桌面中，打开 PowerShell。
2.  输入以下内容：nslookup sqlserver{uniqueid}.database.windows.net。 
    将收到类似于下面的消息：

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  安装 SQL Server Management Studio。
4.  在“连接服务器”中，输入或选择以下信息 **** ：
    - **服务器类型**：选择“数据库引擎”。
    - **服务器名称**：选择“sqlserver{uniqueid}.database.windows.net”。
    - **用户名**：输入在创建过程中提供的用户名。
    - 密码：输入在创建过程中提供的密码。
    - **记住密码**：选择“是”。 ****

5.  选择“连接”。
6.  从左侧菜单中转到“数据库”。
7.  （可选）创建或查询 sample-db 中的信息。
8.  关闭与 myVm{uniqueid} 的远程桌面连接。

## <a name="clean-up-resources"></a>清理资源

如果不再需要为专用终结点创建的资源，请删除资源组。 这会删除该专用终结点和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 专用链接](private-link-overview.md)。
