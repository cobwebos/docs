---
title: 快速入门：使用专用 IP 地址连接到虚拟机： Azure 堡垒
description: 本文介绍如何从虚拟机创建 Azure 堡垒主机并使用专用 IP 地址进行安全连接。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 0b4f0a1fa4f9cce05aa544bb24e045209a6d24ef
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137452"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入门：使用专用 IP 地址和 Azure 堡垒连接到虚拟机

本快速入门文章介绍了如何使用专用 IP 地址连接到虚拟机。 通过堡垒连接时，虚拟机不需要公共 IP 地址。 本文中的步骤可帮助你通过门户中的虚拟机将堡垒部署到虚拟网络。 预配服务后，RDP/SSH 体验即可用于同一虚拟网络中的所有虚拟机。

## <a name="prereq"></a>先决条件

* 一个 Azure 虚拟网络。
* 虚拟网络中的 Azure 虚拟机已打开端口3389。

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
| 公共 IP 地址 SKU |  Standard  |
| 分配  | 静态 |

## <a name="createvmset"></a>创建堡垒主机

当你使用现有虚拟机在门户中创建堡垒主机时，会自动将不同的设置默认为对应于你的虚拟机和/或虚拟网络。

1. 打开 [Azure 门户](https://portal.azure.com)。 中转到你的虚拟机，并单击 "**连接**"。

   ![虚拟机设置](./media/quickstart-host-portal/vm-settings.png)
1. 从下拉列表中，选择 "**堡垒**"。
1. 在 "连接" 页上，选择 "**使用堡垒**"。

   ![选择堡垒](./media/quickstart-host-portal/select-bastion.png)

1. 在 "堡垒" 页上，填写以下设置字段：

   * **名称**：为堡垒主机命名
   * **子网**：要将堡垒资源部署到的虚拟网络中的子网。 必须用名称**AzureBastionSubnet**创建子网。 该名称允许 Azure 知道要将堡垒资源部署到哪个子网。 这不同于网关子网。 使用至少为/27 或更大（/27、/26、/25 等）的子网。
   
      * 选择 "**管理子网配置**"，然后选择 " **+ 子网**"。
      * 在 "添加子网" 页中，键入**AzureBastionSubnet**。
      * 以 CIDR 表示法指定地址范围。 例如，10.1.254.0/27。
      * 选择 **"确定"** 以创建子网。 在页面顶部，导航回 "堡垒" 以完成其余设置。

         ![导航到堡垒设置](./media/quickstart-host-portal/navigate-bastion.png)
   * **公共 ip 地址**：这是要在其上访问 RDP/SSH 的堡垒资源（通过端口443）的公共 ip。 创建新的公共 IP，或使用现有的公共 IP。 公共 IP 地址必须与要创建的堡垒资源位于同一区域。
   * **公共 ip 地址名称**：公共 ip 地址资源的名称。
1. 在验证屏幕上，单击 "**创建**"。 等待大约5分钟等待堡垒资源的创建和部署。

   ![创建堡垒主机](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a>与

在将堡垒部署到虚拟网络后，屏幕将更改为 "连接" 页。

1. 键入虚拟机的用户名和密码。 然后，选择“连接”。

   ![连接](./media/quickstart-host-portal/connect.png)
1. 通过堡垒连接到此虚拟机的 RDP 将直接在 Azure 门户（通过 HTML5）中使用端口443和堡垒服务打开。

   ![RDP 连接](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清理资源

使用完虚拟网络和虚拟机后，请删除资源组及其包含的所有资源：

1. 在门户顶部的**搜索**框中输入 " *TestRG1* "，然后从搜索结果中选择 " **TestRG1** "。

2. 选择“删除资源组”。

3. 输入*TestRG1*作为 **"资源组名称"** ，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你为虚拟网络创建了一个堡垒主机，然后通过堡垒主机安全连接到该虚拟机。

* 若要了解有关 Azure 堡垒的详细信息，请阅读[堡垒概述](bastion-overview.md)和[堡垒常见问题解答](bastion-faq.md)。
* 若要在 Azure 堡垒子网中使用网络安全组，请参阅[使用 nsg](bastion-nsg.md)。
* 有关包含 Azure 堡垒主机设置说明的说明，请参阅[教程](bastion-create-host-portal.md)。
* 若要连接到虚拟机规模集，请参阅[使用 Azure 堡垒连接到虚拟机规模集](bastion-connect-vm-scale-set.md)。