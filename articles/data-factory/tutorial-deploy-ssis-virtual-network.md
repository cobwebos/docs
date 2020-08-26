---
title: 有关如何配置 Azure-SSIS 集成运行时以加入虚拟网络的教程
description: 了解如何将 Azure-SSIS Integration Runtime 加入 Azure 虚拟网络。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 0b83049e154afc48334cc6deb576c700ed71d844
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84118143"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>配置 Azure SQL Server Integration Services (SSIS) 集成运行时 (IR) 以加入虚拟网络

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本教程提供使用 Azure 门户配置 Azure-SQL Server Integration Services (SSIS) 集成运行时 (IR) 以加入虚拟网络的基本步骤。

步骤包括：

- 配置虚拟网络
- 通过 Azure 数据工厂门户将 Azure-SSIS IR 加入虚拟网络。

## <a name="prerequisites"></a>先决条件

- **Azure-SSIS 集成运行时**。 如果没有 Azure SSIS 集成运行时，请在开始之前[在 Azure 数据工厂中预配 Azure SSIS 集成运行时](tutorial-deploy-ssis-packages-azure.md)。

- **用户权限**。 创建 Azure-SSIS IR 的用户必须至少使用以下选项之一在 Azure 数据工厂资源上进行[角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)：

    - 使用内置的“网络参与者”角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。
    - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 如果你还想要使用自己的 Azure-SSIS IR 公共 IP 地址，同时将其加入 Azure 资源管理器虚拟网络，请在角色中包含 _Microsoft.Network/publicIPAddresses/*/join/action_ 权限。

- 虚拟网络。

    - 如果没有虚拟网络，请[使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。

    - 确保虚拟网络的资源组可以创建和删除特定的 Azure 网络资源。
    
        Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
        - Azure 负载均衡器，名为 \<Guid>-azurebatch-cloudserviceloadbalancer
        - 网络安全组，名为 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Azure 公共 IP 地址，名为 -azurebatch-cloudservicepublicip
    
        这些资源在 Azure-SSIS IR 启动时创建， 并在 Azure-SSIS IR 停止时删除。 为了避免阻止 Azure-SSIS IR 停止，请不要在其他资源中重用这些网络资源。

    - 确保虚拟网络所属的资源组/订阅中没有任何[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果配置只读/删除锁，则启动和停止 Azure-SSIS IR 将会失败，或者它会停止响应。

    - 确保没有 Azure Policy 分配阻止在虚拟网络所属的资源组/订阅下创建以下资源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 下面的**网络配置**方案在本教程中不介绍：
    - 如果使用自己的 Azure-SSIS IR 公共 IP 地址。
    - 如果使用自己的域名系统 (DNS) 服务器。
    - 如果在子网中使用网络安全组 (NSG)。
    - 如果使用 Azure ExpressRoute 或用户定义的路由 (UDR)。
    - 如果使用自定义的 Azure-SSIS IR。
    
    有关详细信息，请查看[虚拟网络配置](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)。

## <a name="configure-a-virtual-network"></a>配置虚拟网络

使用 Azure 门户配置一个虚拟网络，然后尝试将 Azure-SSIS IR 加入其中。

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“更多服务”。 筛选并选择“虚拟网络”。

1. 在列表中筛选并选择自己的虚拟网络。

1. 在“虚拟网络”页中选择“属性”。 

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。

1. 在左侧菜单中选择“子网”。

    - 确保选择的子网具有足够的可用地址空间以供 Azure-SSIS IR 使用。 将可用 IP 地址数保持为 IR 节点数的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址仅为协议一致性而保留，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - 不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 GatewaySubnet 专用于虚拟网络网关。
    - 不要使用其他 Azure 服务（例如，SQL 数据库 SQL 托管实例、应用服务等）以独占方式占用的子网。

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或者注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。）

   1. 在 Azure 门户的左侧菜单中选择“订阅”。

   1. 选择订阅。

   1. 在左侧选择“资源提供程序”，确认 **Microsoft.Batch** 是注册的提供程序。

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可将 Azure-SSIS IR 加入该虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前只有这些 Web 浏览器支持数据工厂 UI。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“数据工厂”。 如果菜单中未显示“数据工厂”，请选择“更多服务”，然后在“智能 + 分析”部分选择“数据工厂”。   

   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择“创作和监视”磁贴。 单独的选项卡中会显示数据工厂 UI。

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。  

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure SSIS IR 正在运行，请在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“停止”按钮。   只有在停止 Azure-SSIS IR 之后才能对其进行编辑。

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在“集成运行时”列表中的“操作”列内，选择 Azure SSIS IR 对应的“编辑”按钮。  

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在集成运行时设置面板上，选择“下一步”按钮进入“常规设置”和“SQL 设置”部分。  

1. 在“高级设置”部分：
   1. 选中“选择 Azure-SSIS Integration Runtime 要加入的 VNet，允许 ADF 创建特定网络资源，或者选择引入自己的静态公共 IP 地址”复选框。

   1. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

   1. 对于“位置”，系统已选择集成运行时所在的位置。

   1. 对于“类型”，请选择虚拟网络的类型：“经典”或“Azure 资源管理器”。 我们建议选择 Azure 资源管理器虚拟网络，因为经典虚拟网络在不久后将被弃用。

   1. 对于“VNet 名称”，请选择虚拟网络的名称。 它应与用于具有虚拟网络服务终结点的 SQL 数据库或带有专用终结点的 SQL 托管实例一起用于托管 SSISDB。 或者，它应该是连接到本地网络的同一个虚拟网络。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何虚拟网络。

   1. 对于“子网名称”，请选择虚拟网络的子网名称。 它应与用于 SQL 数据库的虚拟网络服务终结点相同，以便托管 SSISDB。 或者，它应与用于 SQL 托管实例的子网不同，专用终结点用于托管 SSISDB。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何子网。

   1. 选择“VNet 验证”。 如果验证成功，请选择“继续”。

   ![使用虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在“摘要”部分，复查 Azure-SSIS IR 的所有设置。 然后选择“更新”。

1. 在 Azure-SSIS IR 的“操作”列中选择“启动”按钮以启动 Azure-SSIS IR。  启动要加入虚拟网络的 Azure-SSIS IR 需要花费大约 20 到 30 分钟。

## <a name="next-steps"></a>后续步骤

详细了解如何[将 Azure-SSIS IR 加入虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。
