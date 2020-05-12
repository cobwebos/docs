---
title: 教程 - 在 Azure 中为 VMWare 私有云配置网络
description: 了解如何在 Azure 中创建和配置在部署私有云时所需的网络
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 41043b98a6e270d8d9f4373de7876b3fcae86747
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837611"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>教程：在 Azure 中为 VMWare 私有云配置网络

Azure VMware 解决方案 (AVS) 私有云需要一个虚拟网络。 由于 AVS 在预览版中不支持本地 vCenter，因此需要执行额外的步骤来与本地环境集成。 此外，需要设置 ExpressRoute 线路和虚拟网络网关，本教程将介绍此操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关
> * 查找 vCenter 和 NSX 管理器的 URL

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-virtual-network"></a>创建虚拟网络

导航到在[上一教程](tutorial-create-private-cloud.md)中创建的资源组，然后选择“+ 添加”以定义新资源  。

在“搜索市场”文本框中，键入“虚拟网络”   。 找到“虚拟网络”资源并将其选中。

在“虚拟网络”页上，选择“创建”来为私有云设置虚拟网络  。

在“创建虚拟网络”页上，输入虚拟网络的相关详细信息，下表显示了这些属性的说明  ：

> [!IMPORTANT]
> 使用的地址空间不得与在上一教程中创建私有云时所用的地址空间重叠  。

在“基本信息”选项卡上输入虚拟网络的名称，选择相应的区域，然后选择“下一步:   IP 地址”

在“IP 地址”选项卡上的“IPv4 地址空间”下，输入在上一教程中创建的地址空间   。

选择“+ 添加子网”，然后在“添加子网”页上，指定子网名称和相应的地址范围   。 完成后，选择“添加”。 

选择“查看 + 创建” 

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="创建虚拟网络" border="true":::

验证信息并选择“创建”  。 部署完成后，会在资源组中看到你的虚拟网络。

## <a name="create-a-virtual-network-gateway"></a>创建虚拟网络网关

在上一部分中，你创建了一个虚拟网络，现在你将创建一个虚拟网络网关。

在资源组中，选择“+ 添加”以添加新资源  。

在“搜索市场”文本框中，键入“虚拟网络网关”   。 找到“虚拟网络”资源并将其选中。

在“虚拟网络网关”页上选择“创建”   。

在“创建虚拟网络网关”页的“基本信息”选项卡上，为字段提供值  。 下表显示了字段的说明：

| 字段 | 值 |
| --- | --- |
| **订阅** | 已使用资源组所属的订阅填充此值。 |
| **资源组** | 已为当前资源组填充此值。 此资源组应是在前面的测试中创建的资源组。 |
| **名称** | 输入虚拟网络网关的唯一名称。 |
| **区域** | 选择虚拟网络网关的地理位置。 |
| **网关类型** | 选择“ExpressRoute”  。 |
| **VPN 类型** | 选择“基于路由”。  |
| **SKU** | 保留默认值：“标准”  。 |
| **虚拟网络** | 选择前面创建的虚拟网络。 如果未看到虚拟网络，请确保网关的区域与虚拟网络的区域匹配。 |
| **网关子网地址范围** | 选择虚拟网络时会填充此值。 不要更改默认值。 |
| **公共 IP 地址** | 选择“新建”。  |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="创建网关" border="true":::

选择“审阅 + 创建”，在下一页上验证详细信息是否正确，然后选择“创建”以开始部署虚拟网络网关   。 部署完成后，转到本教程的下一部分，将 ExpressRoute 连接到包含私有云的虚拟网络。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>将 ExpressRoute 连接到虚拟网络网关

本部分引导你在 AVS 私有云与创建的虚拟网络网关之间添加连接。

导航到在上一教程中创建的私有云，在“管理”下选择“连接”，然后选择“ExpressRoute”选项卡    。

复制授权密钥。 如果没有授权密钥，需要创建一个密钥，为此，请选择“+ 请求授权密钥” 

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="请求授权密钥" border="true":::

导航到在上一步中创建的虚拟网络网关，在“设置”下选择“连接”   。 在“连接”页上，选择“+ 添加”   。

在“添加连接”页上为字段提供值  。 下表显示了字段的说明：

| 字段 | 值 |
| --- | --- |
| **名称**  | 输入连接的名称。  |
| **连接类型**  | 选择“ExpressRoute”  。  |
| **兑换授权**  | 确保选中此框。  |
| **虚拟网络网关** | 前面创建的虚拟网络网关  |
| **授权密钥**  | 从资源组的“ExpressRoute”选项卡复制并粘贴授权密钥。 |
| **对等线路 URI**  | 从资源组的“ExpressRoute”选项卡复制并粘贴 ExpressRoute ID。  |

选择“确定”  。 这会在 ExpressRoute 线路与虚拟网络之间创建连接。

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="添加连接" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>查找 vCenter 和 NSX 管理器的 URL

若要登录到 vCenter 和 NSX 管理器，需要提供 vCenter Web 客户端和 NSX-T 管理器站点的 URL。 若要查找 URL：

导航到 AVS 私有云，在“管理”下选择“标识”，然后便可以找到所需的信息   。

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="查找 vCenter URL" border="true":::

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建虚拟网络网关
> * 将 ExpressRoute 线路连接到网关
> * 查找 vCenter 和 NSX 管理器的 URL

继续学习下一教程，了解如何创建用于连接环境的 Jumpbox，以便可以在本地管理私有云。

> [!div class="nextstepaction"]
> [访问私有云](tutorial-access-private-cloud.md)
