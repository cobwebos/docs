---
title: 教程：将 SQL 数据库托管实例添加到故障转移组
description: 了解如何为 Azure SQL 数据库托管实例配置故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: 5bf2ee68ff2cfde2846dff2d94f7478920a18760
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69037107"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教程：将 SQL 数据库托管实例添加到故障转移组

将 SQL 数据库托管实例添加到故障转移组。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

  > [!NOTE]
  > 创建托管实例可能需要很长时间。 因此, 此教程可能需要几个小时才能完成。 有关预配时间的详细信息, 请参阅[托管实例管理操作](sql-database-managed-instance.md#managed-instance-management-operations)。 将故障转移组用于托管实例目前处于预览阶段。 

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备： 

- 如果还没有 Azure 订阅, 请[创建一个免费帐户](https://azure.microsoft.com/free/)。 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1-创建资源组和主托管实例
在此步骤中, 将使用 Azure 门户为故障转移组创建资源组和主托管实例。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 选择在 Azure 门户的左上角**创建资源**。 
1. 在`managed instance`搜索框中键入, 并选择 "Azure SQL 托管实例" 选项。 
1. 选择 "**创建**" 以启动 " **SQL 托管实例**创建" 页。 
1. 在 "**创建 Azure SQL 数据库托管实例**" 页上的 "**基本**信息" 选项卡
    1. 在 "**项目详细信息**" 下, 从下拉菜单中选择你的**订阅**, 然后选择 "**新建**资源组"。 键入资源组的名称, 例如`myResourceGroup`。 
    1. 在 "**托管实例详细信息**" 下, 提供托管实例的名称以及要将托管实例部署到的区域。 请确保选择具有[配对区域](/azure/best-practices-availability-paired-regions)的区域。 将 "**计算 + 存储**" 保留为默认值。 
    1. 在 "**管理员帐户**" 下, 提供管理员登录名`azureuser`(例如) 和复杂的管理员密码。 

    ![创建主 MI](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 将其余设置保留为默认值, 然后选择 "**查看 + 创建**" 以查看托管实例设置。 
1. 选择 "**创建**" 以创建主托管实例。 


## <a name="2---create-a-virtual-network"></a>2-创建虚拟网络
在此步骤中, 你将为辅助托管实例创建虚拟网络。 此步骤是必需的, 因为主和辅助托管实例的子网具有不重叠的地址范围。 

若要验证主虚拟网络的子网范围, 请执行以下步骤:
1. 在[Azure 门户](https://portal.azure.com)中, 导航到资源组, 然后选择主实例的虚拟网络。 
1. 选择 "**设置**" 下的**子网**, 并记下**地址范围**。 辅助托管实例的虚拟网络的子网地址范围不能与此重叠。 


   ![主要子网](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

若要创建虚拟网络, 请执行以下步骤:

1. 在[Azure 门户](https://portal.azure.com)中, 选择 "**创建资源**" 并搜索 "*虚拟网络*"。 
1. 选择 Microsoft 发布的 "**虚拟网络**" 选项, 然后在下一页上选择 "**创建**"。 
1. 填写必填字段以配置辅助托管实例的虚拟网络, 然后选择 "**创建**"。 

   下表显示了辅助虚拟网络所需的值:

    | **字段** | ReplTest1 |
    | --- | --- |
    | **名称** |  辅助托管实例使用的虚拟网络的名称, 例如`vnet-sql-mi-secondary`。 |
    | **地址空间** | 虚拟网络的地址空间, 例如`10.128.0.0/16`。 | 
    | **订阅** | 主托管实例和资源组所在的订阅。 |
    | **区域** | 你将在其中部署辅助托管实例的位置;这应该位于与主托管实例[配对的区域](/azure/best-practices-availability-paired-regions)中。  |
    | **子网** | 子网的名称。 `default`默认情况下, 为您提供。 |
    | **地址范围**| 子网的地址范围。 这必须不同于主托管实例的虚拟网络使用的子网地址范围, 例如`10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![辅助虚拟网络值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3-创建辅助托管实例
在此步骤中, 您将在 Azure 门户中创建一个辅助托管实例, 该实例还将在两个托管实例之间配置网络。 

第二个托管实例必须:
- 为空。 
- 位于[配对区域](/azure/best-practices-availability-paired-regions)内, 其主托管实例对应。 
- 具有不同于主托管实例的子网和 IP 范围。 

若要创建辅助托管实例, 请执行以下步骤: 

1. 在[Azure 门户](http://portal.azure.com)中, 选择 "**创建资源**", 然后搜索 " *Azure SQL 托管实例*。 
1. 选择 Microsoft 发布的**AZURE SQL 托管实例**选项, 然后在下一页上选择 "**创建**"。
1. 在 "**创建 Azure SQL 数据库托管实例**" 页的 "**基本**信息" 选项卡上, 填写必填字段以配置辅助托管实例。 

   下表显示了辅助托管实例所需的值:
 
    | **字段** | ReplTest1 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **资源组**| 主托管实例所在的资源组。 |
    | **托管实例名称** | 新的辅助托管实例的名称, 例如`sql-mi-secondary`  | 
    | **区域**| 辅助托管实例的[配对区域](/azure/best-practices-availability-paired-regions)位置。  |
    | **托管实例管理员登录名** | 要用于新的辅助托管实例的登录名, 如`azureuser`。 |
    | **密码** | 管理登录名将用于新的辅助托管实例的复杂密码。  |
    | &nbsp; | &nbsp; |

1. 在 "**网络**" 选项卡下, 对于 "**虚拟网络**", 从下拉选项中选择为辅助托管实例创建的虚拟网络。

   ![辅助 MI 网络](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在 "**其他设置**" 选项卡下, 对于 "**异地复制**", 选择 **"是"** 以_用作故障转移辅助数据库_。 从下拉项中选择主托管实例。 
    1. 请确保排序规则和时区与主托管实例的排序规则和时区匹配。 在本教程中创建的主托管实例使用`SQL_Latin1_General_CP1_CI_AS`排序规则`(UTC) Coordinated Universal Time`和时区的默认值。 

   ![辅助 MI 网络](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 选择 "查看" 和 "**创建**" 以查看辅助托管实例的设置。 
1. 选择 "**创建**" 以创建辅助托管实例。 


## <a name="4---create-primary-virtual-network-gateway"></a>4-创建主虚拟网络网关 

对于两个托管实例以参与故障转移组, 必须在两个托管实例的虚拟网络之间配置一个网关, 以允许网络通信。 您可以使用 Azure 门户创建主托管实例的网关:

1. 在[Azure 门户](https://portal.azure.com)中, 请前往资源组, 并选择主托管实例的**虚拟网络**资源。 
1. 选择 "**设置**" 下的**子**网, 然后选择 "添加新的**网关子网**"。 保留默认值。 

   ![为主托管实例添加网关](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 创建子网网关后, 从左侧导航窗格中选择 "**创建资源**", 然后`Virtual network gateway`在 "搜索" 框中键入。 选择**Microsoft**发布的**虚拟网络网关**资源。 

   ![创建新的虚拟网络网关](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填写必填字段, 以配置主托管实例的网关。 

   下表显示了主托管实例的网关所需的值:
 
    | **字段** | ReplTest1 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **名称** | 虚拟网络网关的名称, 如`primary-mi-gateway`。 | 
    | **区域** | 辅助托管实例所在的区域。 |
    | **网关类型** | 选择“VPN”。 |
    | **VPN 类型** | 选择 "**基于路由**" |
    | **SKU**| 保留默认值`VpnGw1`。 |
    | **Location**| 辅助托管实例和辅助虚拟网络所在的位置。   |
    | **虚拟网络**| 选择在第2部分中创建的虚拟网络, 例如`vnet-sql-mi-primary`。 |
    | **公共 IP 地址**| 选择“新建”。 |
    | **公共 IP 地址名称**| 输入 IP 地址的名称, 如`primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |
1. 将其他值保留为默认值, 然后选择 "**查看 + 创建**" 以查看虚拟网络网关的设置。

   ![主网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 选择 "**创建**" 以创建新的虚拟网络网关。 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5-配置辅助虚拟网络网关 

重复上一部分中的步骤, 为辅助托管实例创建虚拟网络子网和网关。 填写必填字段以配置辅助托管实例的网关。 

   下表显示了辅助托管实例的网关所需的值:

   | **字段** | ReplTest1 |
   | --- | --- |
   | **订阅** |  辅助托管实例所在的订阅。 |
   | **名称** | 虚拟网络网关的名称, 如`secondary-mi-gateway`。 | 
   | **区域** | 辅助托管实例所在的区域。 |
   | **网关类型** | 选择“VPN”。 |
   | **VPN 类型** | 选择 "**基于路由**" |
   | **SKU**| 保留默认值`VpnGw1`。 |
   | **Location**| 辅助托管实例和辅助虚拟网络所在的位置。   |
   | **虚拟网络**| 选择在第2部分中创建的虚拟网络, 例如`vnet-sql-mi-secondary`。 |
   | **公共 IP 地址**| 选择“新建”。 |
   | **公共 IP 地址名称**| 输入 IP 地址的名称, 如`secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![辅助网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6-连接网关
在此步骤中, 在网关之间创建连接。 必须从主网关建立连接, 然后才能在辅助网关之间建立单独的连接。 在两个网关之间配置连接时, 请务必使用相同的**共享密钥**。 

若要配置连接, 请执行以下步骤:

1. 导航到[Azure 门户](https://portal.azure.com)中的资源组, 并选择在步骤4中创建的主网关。 
1. 选择 "**设置**" 下的 "**连接**", 然后选择 "**添加**" 创建新连接。 

   ![将连接添加到主网关](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. 输入连接的名称, 例如`Primary-connection`, 并键入**共享** `mi1mi2psk`密钥的值, 例如。 
1. 选择**第二个虚拟网络网关**, 然后选择辅助托管实例的网关, 例如`secondary-mi-gateway`。 

   ![创建主数据库到辅助连接](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. 选择 **"确定"** 以添加新的主要网关连接。
1. 重复这些步骤, 创建从辅助托管实例的网关到主托管实例的网关的连接。 

   ![创建辅助主机连接](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7-创建故障转移组
在此步骤中, 将创建故障转移组, 并向其中添加两个托管实例。 

1. 在[Azure 门户](https://portal.azure.com)中, 切换到 "**所有服务**" `managed instance` , 并在搜索框中键入。 
1. 可有可无选择 " **SQL 托管实例**" 旁边的星号, 将托管实例添加为左侧导航栏的快捷方式。 
1. 选择 " **SQL 托管实例**" 并选择主托管实例, 如`sql-mi-primary`。 
1. 在 "**设置**" 下, 导航到 "**实例故障转移组**", 然后选择 "**添加组**" 以打开 "**实例故障转移组**" 页。 

   ![添加故障转移组](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在 "**实例故障转移组**" 页上, 键入故障转移组的名称 ( `failovergrouptutorial`例如), 然后选择辅助托管`sql-mi-secondary`实例, 如下拉。 选择 "**创建**" 以创建故障转移组。 

   ![创建故障转移组](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 故障转移组部署完成后, 你将返回到**故障转移组**页。 

## <a name="8---test-failover"></a>8-测试故障转移
在此步骤中, 你将故障转移组故障转移到辅助服务器, 然后使用 Azure 门户故障回复。 

1. 导航到[Azure 门户](https://portal.azure.com)中的托管实例, 并选择 "设置" 下的 "**实例故障转移组**"。 
1. 查看哪个托管实例是主实例, 哪个托管实例是辅助数据库。 
1. 选择 "**故障转移**", 然后在有关要断开的 TDS 会话的警告上选择 **"是"** 。 

   ![故障转移组故障转移](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪个托管实例是主实例, 哪个实例是辅助实例。 如果已成功进行故障转移, 则这两个实例应该已切换角色。 

   ![托管实例在故障转移后切换了角色](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 再次选择 "**故障转移**", 将主实例故障回复到主角色。 


## <a name="clean-up-resources"></a>清理资源
通过先删除托管实例, 然后依次删除虚拟群集、任何剩余资源和资源组来清理资源。 

1. 导航到[Azure 门户](https://portal.azure.com)中的资源组。 
1. 选择托管实例, 然后选择 "**删除**"。 在`yes`文本框中键入以确认是否要删除该资源, 然后选择 "**删除**"。 此过程可能需要一段时间才能在后台完成, 在完成之前, 你将无法删除*虚拟群集*或任何其他从属资源。 监视 "活动" 选项卡中的 "删除", 确认已删除托管实例。 
1. 删除托管实例后, 通过在资源组中选择虚拟群集, 然后选择 "**删除**" 来删除*虚拟群集*。 在`yes`文本框中键入以确认是否要删除该资源, 然后选择 "**删除**"。 
1. 删除任何剩余资源。 在`yes`文本框中键入以确认是否要删除该资源, 然后选择 "**删除**"。 
1. 通过选择 "**删除资源组**", 键入资源组`myResourceGroup`的名称, 然后选择 "**删除**" 来删除资源组。 

## <a name="next-steps"></a>后续步骤

在本教程中, 你在两个托管实例之间配置了一个故障转移组。 你已了解如何：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

转到下一个快速入门, 了解如何连接到托管实例, 以及如何将数据库还原到托管实例: 

> [!div class="nextstepaction"]
> [连接到托管实例](sql-database-managed-instance-configure-vm.md)
> 将[数据库还原到托管实例](sql-database-managed-instance-get-started-restore.md)


