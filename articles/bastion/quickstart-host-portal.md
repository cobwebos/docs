---
title: 快速入门：使用专用 IP 地址连接到虚拟机：Azure 堡垒
description: 在本文中，了解如何从虚拟机创建 Azure Bastion 主机，以及如何使用专用 IP 地址安全地连接。
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619255"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>快速入门：使用专用 IP 地址和 Azure 堡垒连接到虚拟机

此快速入门文章介绍如何使用专用 IP 地址连接到虚拟机。 通过 Bastion 进行连接时，虚拟机不需要公共 IP 地址。 本文中的步骤可帮助您通过门户中的虚拟机将堡垒部署到虚拟网络。 预配服务后，RDP/SSH 体验将提供给同一虚拟网络中的所有虚拟机。

## <a name="prerequisites"></a><a name="prereq"></a>先决条件

* 一个 Azure 虚拟网络。
* 位于打开端口 3389 的虚拟网络中的 Azure 虚拟机。

### <a name="example-values"></a>示例值

|**名称** | **价值** |
| --- | --- |
| “属性” |  VNet1 堡垒 |
| 区域 | eastus |
| 虚拟网络 |  VNet1 |
| • 子网名称 | Azure 堡垒子网 |
| AzureBastion Subnet 地址 |  10.1.254.0/27 |
| 公共 IP 地址 |  新建 |
| 公共 IP 地址名称 | VNet1BastionPIP  |
| 公共 IP 地址 SKU |  Standard  |
| 分配  | Static |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>创建 Bastion 主机

当您使用现有虚拟机在门户中创建堡垒主机时，各种设置将自动默认以对应于虚拟机和/或虚拟网络。

1. 打开[Azure 门户](https://portal.azure.com)。 转到虚拟机，然后单击"**连接**"。

   ![虚拟机设置](./media/quickstart-host-portal/vm-settings.png)
1. 从下拉列表中，选择 **"堡垒**"。
1. 在"连接"页上，选择 **"使用堡垒**"。

   ![选择堡垒](./media/quickstart-host-portal/select-bastion.png)

1. 在"堡垒"页上，填写以下设置字段：

   * **名称**： 命名堡垒主机
   * **子网**：将部署堡垒资源的虚拟网络中的子网。 子网必须使用**名称 AzureBastion Subnet 创建**。 该名称使 Azure 知道要将堡垒资源部署到哪个子网。 这与网关子网不同。 使用至少 /27 或更大的子网（/27、/26、/25 等）。
   
      * 选择 **"管理子网配置**"，然后选择 **" 子网**"。
      * 在"添加子网"页上，键入**AzureBastion 子网**。
      * 在 CIDR 表示法中指定地址范围。 例如，10.1.254.0/27。
      * 选择 **"确定"** 以创建子网。 在页面顶部，导航回堡垒以完成其余设置。

         ![导航到堡垒设置](./media/quickstart-host-portal/navigate-bastion.png)
   * **公共 IP 地址**：这是将访问 RDP/SSH 的堡垒资源的公共 IP（通过端口 443）。 创建新的公共 IP 或使用现有 IP。 公共 IP 地址必须与您正在创建的堡垒资源位于同一区域。
   * **公共 IP 地址名称**：公共 IP 地址资源的名称。
1. 在验证屏幕上，单击"**创建**"。 等待大约 5 分钟，以便创建和部署堡垒资源。

   ![创建堡垒主机](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>连接

将堡垒部署到虚拟网络后，屏幕将更改为连接页。

1. 键入虚拟机的用户名和密码。 然后，选择 **"连接**"。

   ![连接](./media/quickstart-host-portal/connect.png)
1. 通过 Bastion 连接到此虚拟机的 RDP 连接将直接在 Azure 门户（通过 HTML5）中使用端口 443 和堡垒服务打开。

   ![RDP 连接](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>清理资源

使用虚拟网络和虚拟机完成后，请删除资源组及其包含的所有资源：

1. 在门户顶部的 **"搜索**"框中输入*TestRG1，* 然后从搜索结果中选择**TestRG1。**

2. 选择“删除资源组”****。

3. 输入*TESTRG1*以**键入资源组名称**，然后选择 **"删除**"。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您为虚拟网络创建了一个堡垒主机，然后通过堡垒主机安全地连接到虚拟机。

* 要了解有关 Azure 堡垒的详细信息，请阅读[堡垒概述](bastion-overview.md)和[堡垒常见问题 。](bastion-faq.md)
* 要将网络安全组与 Azure 堡垒子网一起使用，请参阅[使用 NSG。](bastion-nsg.md)
* 有关包含 Azure 堡垒主机设置说明的说明，请参阅[教程](bastion-create-host-portal.md)。
* 要连接到虚拟机缩放集，请参阅[使用 Azure Bastion 连接到虚拟机规模集](bastion-connect-vm-scale-set.md)。