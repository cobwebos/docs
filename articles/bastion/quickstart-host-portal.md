---
title: 快速入门：使用专用 IP 地址连接到虚拟机：Azure Bastion
description: 本文介绍如何从虚拟机创建 Azure Bastion 主机并使用专用 IP 地址进行安全连接。
services: bastion
author: charwen
ms.service: bastion
ms.topic: quickstart
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: b0155ae92e3179918273d6a19773aa15b67949ea
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985586"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入门：使用专用 IP 地址和 Azure Bastion 连接到虚拟机

本快速入门文章介绍如何使用专用 IP 地址连接到虚拟机。 在通过 Bastion 连接时，虚拟机无需公共 IP 地址。 本文中的步骤可帮助你在门户中通过虚拟机将 Bastion 部署到虚拟网络。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。

## <a name="prerequisites"></a><a name="prereq"></a>先决条件

* 一个 Azure 虚拟网络。
* 位于虚拟网络中的 Azure 虚拟机，已打开端口 3389。

### <a name="example-values"></a>示例值

|**名称** | **值** |
| --- | --- |
| 名称 |  VNet1Bastion |
| 区域 | eastus |
| 虚拟网络 |  VNet1 |
| + 子网名称 | AzureBastionSubnet |
| AzureBastionSubnet 地址 |  10.1.254.0/27 |
| 公共 IP 地址 |  新建 |
| 公共 IP 地址名称 | VNet1BastionPIP  |
| 公用 IP 地址 SKU |  Standard  |
| 分配  | 静态 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>创建 Bastion 主机

使用现有虚拟机在门户中创建 Bastion 主机时，各种设置将自动默认为与虚拟机和/或虚拟网络相对应。

1. 打开 [Azure 门户](https://portal.azure.com)。 转到虚拟机，然后单击“连接”。

   ![虚拟机设置](./media/quickstart-host-portal/vm-settings.png)
1. 在下拉列表中，选择“Bastion”。
1. 在“连接”页上，选择“使用 Bastion”。

   ![选择“Bastion”](./media/quickstart-host-portal/select-bastion.png)

1. 在“Bastion”页上，填写以下设置字段：

   * 名称：为 Bastion 主机命名
   * **子网**：虚拟网络中的子网，将在其中部署 Bastion 资源。 必须使用名称 AzureBastionSubnet 创建子网。 此名称告知 Azure 要将 Bastion 资源部署到哪个子网。 这不同于网关子网。 使用至少为 /27 或更大（/27、/26、/25 等）的子网。
   
      * 选择“管理子网配置”，然后选择“+ 子网” 。
      * 在“添加子网”页上，键入 AzureBastionSubnet。
      * 指定以 CIDR 表示法表示的地址范围。 例如 10.1.254.0/27。
      * 选择“确定”以创建子网。 在页面顶部，导航回 Bastion 以完成其余设置。

         ![导航到 Bastion 设置](./media/quickstart-host-portal/navigate-bastion.png)
   * **公共 IP 地址**：这是要在其上通过端口 443 访问 RDP/SSH 的 Bastion 资源的公共 IP。 新建公共 IP，或使用现有公共 IP。 公共 IP 地址必须与要创建的 Bastion 资源位于同一区域。
   * **公共 IP 地址名称**：公共 IP 地址资源的名称。
1. 在“验证”屏幕上，单击“创建”。 请等待大约 5 分钟，以创建和部署 Bastion 资源。

   ![创建 Bastion 主机](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>连接

在将 Bastion 部署到虚拟网络后，屏幕切换到连接页面。

1. 键入虚拟机的用户名和密码。 然后，选择“连接”。

   ![屏幕截图显示“使用 Azure Bastion 进行连接”对话框，该对话框提示你输入用户名和密码。](./media/quickstart-host-portal/connect.png)
1. 通过 Bastion 连接到此虚拟机的 RDP 将使用端口 443 和 Bastion 服务在 Azure 门户中（通过 HTML5）直接打开。

   ![RDP 连接](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清理资源

使用完虚拟网络和虚拟机之后，请删除资源组和其包含的所有资源：

1. 在门户顶部的“搜索”框中输入“TestRG1”，并从搜索结果中选择“TestRG1” 。

2. 选择“删除资源组”。

3. 对于“键入资源组名称”，输入“TestRG1”，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你为虚拟网络创建了一个 Bastion 主机，然后通过该 Bastion 主机安全连接到了虚拟机。

* 若要了解有关 Azure Bastion 的详细信息，请参阅 [Bastion 概述](bastion-overview.md)和 [Bastion FAQ](bastion-faq.md)。
* 若要在 Azure Bastion 子网中使用网络安全组，请参阅[使用 NSG](bastion-nsg.md)。
* 有关包含 Azure Bastion 主机设置解释的说明，请参阅[教程](bastion-create-host-portal.md)。
* 若要连接到虚拟机规模集，请参阅[使用 Azure Bastion 连接到虚拟机规模集](bastion-connect-vm-scale-set.md)。