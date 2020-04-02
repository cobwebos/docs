---
title: 在虚拟网络中创建 azure 数据资源管理器群集&数据库
description: 在本文中，您将了解如何在虚拟网络中创建 Azure 数据资源管理器群集。
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548902"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>在虚拟网络中创建 Azure 数据资源管理器群集

Azure 数据资源管理器支持将群集部署到虚拟网络 （VNet） 中的子网。 此功能使您能够从 Azure 虚拟网络或内部部署私下访问群集，访问虚拟网络中的事件集线器和存储等资源，并限制入站和出站流量。

## <a name="prerequisites"></a>先决条件

* 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* 登录 [Azure 门户](https://portal.azure.com/)。

## <a name="create-network-security-group-nsg"></a>创建网络安全组 （NSG）

[网络安全组 （NSG）](/azure/virtual-network/security-overview)提供控制 VNet 内网络访问的能力。 可以使用两个终结点 HTT （443） 和 TDS （1433） 访问 Azure 数据资源管理器。 必须配置以下 NSG 规则，以允许访问这些终结点，以便管理、监视和正确操作群集。

要创建网络安全组，请：

1. 选择门户左上角的 **"创建资源**"按钮。
1. 搜索*网络安全组*。
1. 在**屏幕底部的网络安全组**下，选择 **"创建**"。
1. 在 **"创建网络安全组"** 窗口中，填写以下信息。

   ![创建 NSG 窗体](media/vnet-create-cluster-portal/network-security-group.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 选择要用于群集的 Azure 订阅。|
    | 资源组 | 你的资源组 | 使用现有资源组或创建新资源组。 |
    | 名称 | Azure 数据资源管理器 | 在资源组中选择标识网络安全组 （NSG） 的名称。  |
    | 区域 | *美国西部* | 选择最符合需求的区域。
    | | | |

1. 选择“查看 + 创建”以查看群集的详细信息，选择“创建”以预配群集********。

1. 部署完成后，选择“转到资源”。****

    ![转到资源](media/vnet-create-cluster-portal/notification-nsg.png)

1. 在 **"入站安全规则"** 选项卡中，选择 **"添加**"。
1. 在 **"添加入站安全规则"** 窗口中，填写以下信息。

    ![创建 NSG 入站规则窗体](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **设置** | **建议的值** 
    |---|---|
    | 源 | 服务标签
    | 源服务标记 | Azure 数据资源管理器管理
    | 源端口范围 | *
    | 目标 | VirtualNetwork
    | 目标端口范围 | *
    | 协议 | TCP
    | 操作 | Allow
    | Priority | 100
    | 名称 | 允许 Azure 数据资源管理器管理
    | | |
    
1. 根据[VNet 部署的依赖项](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)，对所有入站和出站依赖项重复前两个步骤。 或者，出站规则可以替换为单个规则，以允许端口 443 和 80 的*Internet。*
    
    入站和出站依赖项的 NSG 规则应如下所示：

    ![NSG 规则](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>创建公共 IP 地址

要创建查询（引擎）公共 IP 地址，请处理：

1. 选择门户左上角的 **"创建资源**"按钮。
1. 搜索*网络安全组*。
1. 在**屏幕底部的公共 IP 地址**下，选择 **"创建**"。
1. 在 **"创建公共 IP 地址**"窗格中，完成以下信息。
   
   ![创建公共 IP 表单](media/vnet-create-cluster-portal/public-ip-blade.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | IP 版本 | IPv4 | 选择 IP 版本。 我们仅支持 IPv4。|
    | SKU | Standard | 我们需要查询（引擎）URI 终结点**的标准**。 |
    | 名称 | 发动机点 | 选择在资源组中标识公共 IP 地址的名称。
    | 订阅 | 订阅 | 选择要用于公共 IP 的 Azure 订阅。|
    | 资源组 | 你的资源组 | 使用现有资源组或创建新资源组。 |
    | 位置 | *美国西部* | 选择最符合需求的区域。
    | | | |

1. 选择 **"创建**"以创建公共 IP 地址。

1. 要创建引入（数据管理）公共 IP 地址，请遵循相同的说明并选择 
    * **SKU**： 基本
    * **IP 地址分配**： 静态

## <a name="create-virtual-network-and-subnet"></a>创建虚拟网络和子网

要创建虚拟网络和子网，

1. 选择门户左上角的 **"创建资源**"按钮。
1. 搜索*虚拟网络*。
1. 在**屏幕底部的虚拟网络**下，选择 **"创建**"。
1. 在 **"创建虚拟网络**"窗口中，完成以下信息。

   ![创建虚拟网络窗体](media/vnet-create-cluster-portal/vnet-blade.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 选择要用于群集的 Azure 订阅。|
    | 资源组 | 你的资源组 | 使用现有资源组或创建新资源组。 |
    | 名称 | Azure 数据资源管理器Vnet | 在资源组中选择标识虚拟网络的名称。
    | 区域 | *美国西部* | 选择最符合需求的区域。
    | | | |

    > [!NOTE]
    > 对于生产工作负载，根据[VNet 中的计划子网大小规划子网大小](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet)

1. 选择“查看 + 创建”以查看群集的详细信息，选择“创建”以预配群集********。

1. 部署完成后，选择“转到资源”。****
1. 转到**子网**边栏选项卡并选择**默认**子网。
    
    ![子网刀片](media/vnet-create-cluster-portal/subnets.png)

1. 在**默认**子网窗口中：
    1. 从下拉菜单中选择**您的网络安全组**。 
    1. 选择网络安全组名称，在这种情况下 **，AzureDataExplorerNsg。** 
    1. **保存**

    ![配置子网](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>创建群集

创建 Azure 数据资源管理器群集，在 Azure 资源组中使用一组定义的计算和存储资源，如[创建群集](create-cluster-database-portal.md#create-a-cluster)中所述。

1. 在完成群集创建之前，在 **"创建 Azure 数据资源管理器"群集**窗口中，选择 **"网络**"选项卡，使用前面选项卡中创建的资源提供虚拟网络详细信息：

   ![创建群集 vnet 窗体](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **设置** | **建议的值** | **字段说明**
    |---|---|---|
    | 订阅 | 订阅 | 选择要用于网络资源的 Azure 订阅。|
    | 虚拟网络 | Azure 数据资源管理器Vnet | 选择在前面的步骤中创建的虚拟网络。
    | 子网 | default | 选择在前面的步骤中创建的子网。
    | 查询公共 IP | 发动机点 | 选择在前面的步骤中创建的查询公共 IP。
    | 数据引入公共 IP | dm-pip | 选择在前面的步骤中创建的引入公共 IP。
    | | | |

1. 选择 **"查看 + 创建"** 以创建群集。
1. 部署完成后，选择“转到资源”。****

要将 Azure 数据资源管理器群集部署到虚拟网络，请使用["将 Azure 数据资源管理器"群集部署到 VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure 资源管理器模板中。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Azure 数据资源管理器部署到虚拟网络](vnet-create-cluster-portal.md)