---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301926"
---
可以通过以下步骤使用资源管理器部署模型和 Azure 门户创建一个 VNet。 有关虚拟网络的详细信息，请参阅[虚拟网络概述](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>将虚拟网络用作跨界体系结构的一部分时，请确保与本地网络管理员协调以雕刻可用于此虚拟网络的 IP 地址范围。 如果 VPN 连接的两端存在重复的地址范围，则会以意外方式路由流量。 此外，如果要将此虚拟网络连接到另一个虚拟网络，地址空间不能与其他虚拟网络重叠。 相应地规划网络配置。
>
>

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 在**搜索资源、服务和文档 （G+/） 中**，键入*虚拟网络*。

   ![查找虚拟网络资源页面](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "查找“虚拟网络”资源页")
1. 从**应用商店**结果中选择**虚拟网络**。

   ![选择虚拟网络](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "查找“虚拟网络”资源页")
1. 在**虚拟网络**页上，选择 **"创建**"。

   ![虚拟网络页面](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "选择“创建”")
1. 选择 **"创建**"后，将打开 **"创建虚拟网络**"页。
1. 在 **"基本"** 选项卡上，配置**项目详细信息**和**实例详细信息**VNet 设置。

   ![基础知识选项卡](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "“基本信息”选项卡")填写字段时，在验证字段中输入的字符时，会看到绿色复选标记。 某些值是自动填写的，你可以将其替换为自己的值：

   - “订阅”****：确认列出的是正确的订阅。 可以使用下拉列表更改订阅。
   - **资源组**：选择现有资源组，或单击"**创建新**资源组"以创建新资源组。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/management/overview.md#resource-groups)。
   - **名称**：输入虚拟网络的名称。
   - **区域**：选择 VNet 的位置。 该位置确定要部署到此 VNet 的资源将位于哪里。

1. 在 **"IP 地址"** 选项卡上，配置值。 以下示例中显示的值用于演示目的。 根据所需的设置调整这些值。

   ![IP 地址选项卡](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP 地址选项卡")  
   - **IPv4 地址空间**：默认情况下，将自动创建地址空间。 您可以单击地址空间来调整它以反映您自己的值。 您还可以添加其他地址空间。
   - **IPv6**：如果您的配置需要 IPv6 地址空间，请选择 **"添加 IPv6 地址空间**"框以输入该信息。
   - **子网**：如果使用默认地址空间，将自动创建默认子网。 如果更改地址空间，则需要添加子网。 选择 **= 添加子网**以打开 **"添加子网"** 窗口。 配置以下设置，然后选择 **"添加"** 以添加值：
      - **子网名称**：在此示例中，我们命名子网"FrontEnd"。
      - **子网地址范围**：此子网的地址范围。

1. 此时，在 **"安全"** 选项卡上，保留默认值：

   - **DDos 保护**： 基本
   - **防火墙**： 已禁用
1. 选择 **"查看 + 创建**"以验证虚拟网络设置。
1. 验证设置后，选择 **"创建**"。
