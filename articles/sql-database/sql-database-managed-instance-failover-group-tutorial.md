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
ms.openlocfilehash: 04802b8b25ca21cc0099874e5a9ea69748868f6e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103208"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>教程：将 SQL 数据库托管实例添加到故障转移组

将 SQL 数据库托管实例添加到故障转移组。 本文介绍如何执行以下操作：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

  > [!NOTE]
  > - 在学习本教程时，请确保使用[为托管实例设置故障转移组的先决条件](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)来配置资源。 
  > - 创建托管实例可能需要花费很长时间。 因此，本教程可能需要几个小时才能完成。 有关预配时间的详细信息，请参阅[托管实例管理操作](sql-database-managed-instance.md#managed-instance-management-operations)。 


## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备： 

- 如果还没有 Azure 订阅，请[创建一个免费帐户](https://azure.microsoft.com/free/)。 


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-创建资源组和主托管实例
此步骤将使用 Azure 门户为故障转移组创建资源组和主托管实例。 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 通过选择 "数据库" 磁贴上的 "显示详细信息"，可以查看有关不同数据库的其他信息。
1. 在 " **SQL 托管实例**" 磁贴上选择 "**创建**"。 

    ![选择托管实例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 数据库托管实例”页的“基本信息”选项卡上
    1. 在“项目详细信息”下，从下拉列表中选择你的订阅，然后选择“创建新资源组”。 键入资源组的名称，例如 `myResourceGroup`。 
    1. 在“托管实例详细信息”下，提供托管实例的名称，以及要将托管实例部署到的区域。 将“计算 + 存储”保留默认值。 
    1. 在“管理员帐户”下，提供管理员登录名（例如 `azureuser`）和复杂的管理员密码。 

    ![创建主托管实例](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 将剩余设置保留默认值，然后选择“查看 + 创建”检查托管实例设置。 
1. 选择“创建”以创建主托管实例。 


## <a name="2---create-a-virtual-network"></a>2 - 创建虚拟网络
此步骤为辅助托管实例创建虚拟网络。 必须执行此步骤，因为主托管实例和辅助托管实例的子网不得使用重叠的地址范围。 

若要验证主虚拟网络的子网范围，请执行以下步骤：
1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组，并选择主实例的虚拟网络。 
1. 在“设置”下选择“子网”，并记下“地址范围”。 辅助托管实例的虚拟网络的子网地址范围不能与此重叠。 


   ![主子网](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

若要创建虚拟网络，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“创建资源”并搜索“虚拟网络”。 
1. 选择 Microsoft 发布的“虚拟网络”选项，然后在下一页选择“创建”。 
1. 填写必填字段以配置辅助托管实例的虚拟网络，然后选择“创建”。 

   下表显示了辅助虚拟网络所需的值：

    | **字段** | ReplTest1 |
    | --- | --- |
    | **名称** |  辅助托管实例要使用的虚拟网络的名称，例如 `vnet-sql-mi-secondary`。 |
    | **地址空间** | 虚拟网络的地址空间，例如 `10.128.0.0/16`。 | 
    | **订阅** | 主托管实例和资源组所在的订阅。 |
    | **区域** | 你将在其中部署辅助托管实例的位置。 |
    | **子网** | 子网的名称。 默认会提供 `default`。 |
    | **地址范围**| 子网的地址范围。 此范围必须不同于主托管实例的虚拟网络使用的子网地址范围，例如 `10.128.0.0/24`。  |
    | &nbsp; | &nbsp; |

    ![辅助虚拟网络值](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 - 创建辅助托管实例
此步骤将在 Azure 门户中创建一个辅助托管实例，同时在两个托管实例之间配置网络。 

第二个托管实例必须：
- 是空的。 
- 与主托管实例的子网和 IP 范围不同。 

若要创建辅助托管实例，请执行以下步骤： 

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 通过选择 "数据库" 磁贴上的 "显示详细信息"，可以查看有关不同数据库的其他信息。
1. 在 " **SQL 托管实例**" 磁贴上选择 "**创建**"。 

    ![选择托管实例](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. 在“创建 Azure SQL 数据库托管实例”页的“基本信息”选项卡上，填写必填字段以配置辅助托管实例。 

   下表显示了辅助托管实例所需的值：
 
    | **字段** | ReplTest1 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **资源组**| 主托管实例所在的资源组。 |
    | **托管实例名称** | 新辅助托管实例的名称，例如 `sql-mi-secondary`  | 
    | **区域**| 辅助托管实例的位置。  |
    | **托管实例管理员登录名** | 要用于新辅助托管实例的登录名，例如 `azureuser`。 |
    | **密码** | 新辅助托管实例的管理员登录名使用的复杂密码。  |
    | &nbsp; | &nbsp; |

1. 在“网络”选项卡下，对于“虚拟网络”，请从下拉列表中选择为辅助托管实例创建的虚拟网络。

   ![辅助托管实例网络](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. 在“其他设置”选项卡下，对于“异地复制”，请选择“用作故障转移辅助实例”旁边的“是”。 从下拉列表中选择主托管实例。 
    1. 确保排序规则和时区与主托管实例匹配。 在本教程中创建的主托管实例使用了默认的 `SQL_Latin1_General_CP1_CI_AS` 排序规则和 `(UTC) Coordinated Universal Time` 时区。 

   ![辅助托管实例网络](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. 选择“查看 + 创建”以检查辅助托管实例的设置。 
1. 选择“创建”以创建辅助托管实例。 


## <a name="4---create-primary-virtual-network-gateway"></a>4 - 创建主虚拟网络网关 

对于要参与故障转移组的两个托管实例，必须在这两个托管实例的虚拟网络之间配置一个网关，以便能够进行网络通信。 可以使用 Azure 门户为主托管实例创建网关：

1. 在 [Azure 门户](https://portal.azure.com)中转到你的资源组，并选择主托管实例的“虚拟网络”资源。 
1. 在“设置”下选择“子网”，然后选择添加新的“网关子网”。 保留默认值。 

   ![为主托管实例添加网关](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 创建子网网关后，在左侧导航窗格中选择“创建资源”，然后在搜索框中键入 `Virtual network gateway`。 选择**Microsoft**发布的**虚拟网络网关**资源。 

   ![创建新的虚拟网络网关](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 填写必填字段，为主托管实例配置网关。 

   下表显示了主托管实例的网关所需的值：
 
    | **字段** | ReplTest1 |
    | --- | --- |
    | **订阅** |  主托管实例所在的订阅。 |
    | **名称** | 虚拟网络网关的名称，例如 `primary-mi-gateway`。 | 
    | **区域** | 辅助托管实例所在的区域。 |
    | **网关类型** | 选择“VPN”。 |
    | **VPN 类型** | 选择“基于路由” |
    | **SKU**| 保留默认值 `VpnGw1`。 |
    | **Location**| 主托管实例和主虚拟网络所在的位置。   |
    | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-primary`。 |
    | **公共 IP 地址**| 选择“新建”。 |
    | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `primary-gateway-IP`。 |
    | &nbsp; | &nbsp; |
1. 将其他值保留为默认值，然后选择“查看 + 创建”以检查虚拟网络网关的设置。

   ![主网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 选择“创建”以创建新的虚拟网络网关。 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 - 配置辅助虚拟网络网关 

重复上一部分中的步骤，为辅助托管实例创建虚拟网络子网和网关。 填写必填字段，为辅助托管实例配置网关。 

   下表显示了辅助托管实例的网关所需的值：

   | **字段** | ReplTest1 |
   | --- | --- |
   | **订阅** |  辅助托管实例所在的订阅。 |
   | **名称** | 虚拟网络网关的名称，例如 `secondary-mi-gateway`。 | 
   | **区域** | 辅助托管实例所在的区域。 |
   | **网关类型** | 选择“VPN”。 |
   | **VPN 类型** | 选择“基于路由” |
   | **SKU**| 保留默认值 `VpnGw1`。 |
   | **Location**| 辅助托管实例和辅助虚拟网络所在的位置。   |
   | **虚拟网络**| 选择在第 2 部分创建的虚拟网络，例如 `vnet-sql-mi-secondary`。 |
   | **公共 IP 地址**| 选择“新建”。 |
   | **公共 IP 地址名称**| 输入 IP 地址的名称，例如 `secondary-gateway-IP`。 |
   | &nbsp; | &nbsp; |

   ![辅助网关设置](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 - 连接网关
此步骤在网关之间创建连接。 必须建立从主网关到辅助网关的连接，然后单独建立从辅助网关到主网关的连接。 在两个网关之间配置连接时，请务必使用相同的**共享密钥**。 

若要配置连接，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组，并选择在步骤 4 中创建的主网关。 
1. 在“设置”下选择“连接”，然后选择“添加”以创建新连接。 

   ![将连接添加到主网关](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. 输入连接的名称，例如 `Primary-connection`，并键入“共享密钥”的值，例如 `mi1mi2psk`。 
1. 选择“第二个虚拟网络网关”，然后选择辅助托管实例的网关，例如 `secondary-mi-gateway`。 

   ![创建从主网关到辅助网关的连接](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. 选择“确定”以添加新的从主网关到辅助网关的连接。
1. 重复上述步骤，创建从辅助托管实例网关到主托管实例网关的连接。 

   ![创建从辅助网关到主网关的连接](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7 - 创建故障转移组
此步骤将创建故障转移组，并将两个托管实例添加到其中。 

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择在第一部分中创建的主托管实例，如`sql-mi-primary`。 
1. 在“设置”下，导航到“实例故障转移组”，然后选择“添加组”打开“实例故障转移组”页。 

   ![添加故障转移组](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. 在“实例故障转移组”页上键入故障转移组的名称（例如 `failovergrouptutorial`），然后从下拉列表中选择辅助托管实例（例如 `sql-mi-secondary`）。 选择“创建”以创建故障转移组。 

   ![创建故障转移组](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 故障转移组部署完成后，你将返回到“故障转移组”页。 

## <a name="8---test-failover"></a>8 - 测试故障转移
此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的托管实例，然后在“设置”下选择“实例故障转移组”。 
1. 查看哪个托管实例是主实例，哪个托管实例是辅助实例。 
1. 选择“故障转移”，然后在有关正在断开 TDS 会话的警告中选择“是”。 

   ![将故障转移组故障转移](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 查看哪个托管实例是主实例，哪个实例是辅助实例。 如果故障转移成功，这两个实例的角色应会交换。 

   ![故障转移后托管实例的角色已交换](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 再次选择“故障转移”，将主实例故障回复为主角色。 


## <a name="clean-up-resources"></a>清理资源
若要清理资源，请依次删除托管实例、虚拟群集、所有剩余资源和资源组。 

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。 
1. 选择托管实例，然后选择“删除”。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 此过程将在后台完成，这可能需要一段时间，在此之前，无法删除虚拟群集或任何其他相关资源。 在“活动”选项卡中监视删除过程，以确认托管实例已删除。 
1. 删除托管实例后，请删除虚拟群集，方法是在资源组中将其选中，然后选择“删除”。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除任何剩余资源。 在文本框中键入 `yes` 以确认你要删除该资源，然后选择“删除”。 
1. 删除资源组：选择“删除资源组”，键入资源组的名称 `myResourceGroup`，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已在两个托管实例之间配置了一个故障转移组。 你已了解如何：

> [!div class="checklist"]
> - 创建主托管实例
> - 创建辅助托管实例作为[故障转移组](sql-database-auto-failover-group.md)的一部分。 
> - 测试故障转移

继续学习下一篇快速入门，了解如何连接到托管实例，以及如何将数据库还原到托管实例： 

> [!div class="nextstepaction"]
> [连接到托管实例](sql-database-managed-instance-configure-vm.md)
> [将数据库还原到托管实例](sql-database-managed-instance-get-started-restore.md)


