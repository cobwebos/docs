---
title: 配置 Azure SSIS 集成运行时以加入虚拟网络的教程
description: 了解如何将 Azure SSIS 集成运行时加入 Azure 虚拟网络。
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
ms.openlocfilehash: 593ceb884e751ca3115b08baf0c9c7e802057f54
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864981"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>配置 Azure SQL Server Integration Services （SSIS）集成运行时（IR）以加入虚拟网络

本教程提供了使用 Azure 门户配置 Azure SQL Server Integration Services （SSIS）集成运行时（IR）以加入虚拟网络的基本步骤。

步骤包括：

- 配置虚拟网络。
- 通过 Azure 数据工厂门户将 Azure-SSIS IR 加入虚拟网络。

## <a name="prerequisites"></a>必备组件

- **Azure-SSIS 集成运行时**。 如果你没有 Azure SSIS 集成运行时，请[在 Azure 数据工厂中预配 AZURE ssis 集成运行时](tutorial-deploy-ssis-packages-azure.md)，然后再开始。

- **用户权限**。 创建 Azure-SSIS IR 的用户必须至少具有以下选项之一的 Azure 数据工厂资源上的[角色分配](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope)：

    - 使用内置的 "网络参与者" 角色。 此角色具有 _Microsoft.Network/\*_ 权限，具有比所需作用域更大的作用域。
    - 创建仅包括必需的 _Microsoft.Network/virtualNetworks/\*/join/action_ 权限的一个自定义角色。 如果还想要为 Azure-SSIS IR 提供自己的公共 IP 地址，同时将它加入 Azure 资源管理器虚拟网络，请在角色中加入_publicIPAddresses/*/join/action_权限。

- 虚拟网络。

    - 如果没有虚拟网络，请[使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。

    - 请确保虚拟网络的资源组可以创建和删除某些 Azure 网络资源。
    
        Azure-SSIS IR 需要在与虚拟网络相同的资源组下创建某些网络资源。 这些资源包括：
        - Azure 负载均衡器，其名称 *\<Guid >-azurebatch-cloudserviceloadbalancer*
        - 网络工作安全组，名称为 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup
        - 使用名称 azurebatch-cloudservicepublicip 的 Azure 公共 IP 地址
    
        Azure-SSIS IR 启动时，将创建这些资源。 当你的 Azure-SSIS IR 停止时，它们将被删除。 若要避免阻止 Azure-SSIS IR 停止，请不要在其他资源中重复使用这些网络资源。

    - 请确保虚拟网络所属的资源组/订阅上没有[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)。 如果配置了只读/删除锁定，则启动和停止 Azure-SSIS IR 将失败，否则将停止响应。

    - 请确保没有 Azure 策略阻止在虚拟网络所属的资源组/订阅下创建以下资源：
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 本教程未涵盖以下**网络配置**方案：
    - 如果为 Azure-SSIS IR 提供了自己的公共 IP 地址。
    - 如果你使用自己的域名系统（DNS）服务器。
    - 如果在子网上使用网络安全组（NSG）。
    - 如果使用 Azure ExpressRoute 或用户定义的路由（UDR）。
    - 如果使用自定义 Azure-SSIS IR。
    
    有关详细信息，请检查[虚拟网络配置](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)。

## <a name="configure-a-virtual-network"></a>配置虚拟网络

在尝试将 Azure-SSIS IR 加入到虚拟网络之前，请使用该 Azure 门户。

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“更多服务”。 筛选并选择“虚拟网络”。

1. 在列表中筛选并选择自己的虚拟网络。

1. 在“虚拟网络”页中选择“属性”。

1. 选择“资源 ID”对应的复制按钮，将虚拟网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。

1. 在左侧菜单中，选择 "**子网**"。

    - 确保所选的子网具有足够的可用地址空间，以便 Azure-SSIS IR 使用。 将可用 IP 地址保留为 IR 节点编号的至少两倍。 Azure 会保留每个子网中的某些 IP 地址。 不能使用这些地址。 子网的第一个和最后一个 IP 地址是为协议一致性而保留的，其他三个地址用于 Azure 服务。 有关详细信息，请参阅[使用这些子网中的 IP 地址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - 不要选择 GatewaySubnet 来部署 Azure-SSIS IR。 它专用于虚拟网络网关。
    - 请勿使用由其他 Azure 服务独占使用的子网（例如，SQL 数据库托管实例、应用服务等）。

1. 验证是否已将 Azure Batch 提供程序注册到包含虚拟网络的 Azure 订阅中。 或注册 Azure Batch 提供程序。 如果订阅中已有 Azure Batch 帐户，则会为 Azure Batch 注册订阅。 （如果在数据工厂门户中创建 Azure-SSIS IR，将自动注册 Azure Batch 提供程序。）

   1. 在 Azure 门户的左侧菜单中，选择 "**订阅**"。

   1. 选择订阅。

   1. 在左侧，选择 "**资源提供程序**"，并确认 " **Microsoft Batch** " 为已注册的提供程序。

   ![确认“已注册”状态](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   如果列表中未出现 **Microsoft.Batch**，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>将 Azure-SSIS IR 加入虚拟网络

配置 Azure 资源管理器虚拟网络或经典虚拟网络后，可以将 Azure-SSIS IR 加入到虚拟网络：

1. 启动 Microsoft Edge 或 Google Chrome。 目前，只有这些 web 浏览器支持数据工厂 UI。

1. 在[Azure 门户](https://portal.azure.com)的左侧菜单中，选择 "**数据工厂**"。 如果菜单中未显示 "**数据工厂**"，请选择 "**更多服务**"，然后在 "**智能 + 分析**" 部分选择 "**数据工厂**"。

   ![数据工厂列表](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 在列表中选择包含 Azure-SSIS IR 的数据工厂。 随后会显示该数据工厂的主页。 选择 "**创作 & 监视器**" 磁贴。 单独的选项卡中会显示数据工厂 UI。

   ![数据工厂主页](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 在数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡。

   ![“集成运行时”选项卡](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. 如果 Azure-SSIS IR 正在运行，请在 " **Integration** runtime" 列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**停止**" 按钮。 你无法编辑 Azure-SSIS IR，直到你将其停止。

   ![停止 IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 在**集成运行时**列表的 "**操作**" 列中，选择 Azure-SSIS IR 的 "**编辑**" 按钮。

   ![编辑集成运行时](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 在集成运行时设置面板上，通过选择 "**下一步**" 按钮前进到 "**常规设置**" 和 " **SQL 设置**" 部分。

1. 在 "**高级设置**" 部分中：
   1. 选择 "为**要加入的 Azure-SSIS Integration Runtime 选择 VNet"，允许 ADF 创建特定的网络资源，并选择 "自带静态公共 IP 地址**" 复选框。

   1. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

   1. 对于“位置”，系统已选择集成运行时所在的位置。

   1. 对于 "**类型**"，请选择虚拟网络的类型：经典或 Azure 资源管理器。 建议选择 Azure 资源管理器虚拟网络，因为即将弃用经典虚拟网络。

   1. 对于“VNet 名称”，请选择虚拟网络的名称。 它应与包含虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例一起用于托管 SSISDB。 或者，它应与连接到本地网络的计算机相同。 否则，可以通过任何虚拟网络为 Azure-SSIS IR 提供自己的静态公共 IP 地址。

   1. 对于“子网名称”，请选择虚拟网络的子网名称。 它应该与用于具有虚拟网络服务终结点的 Azure SQL 数据库服务器托管 SSISDB 的服务器相同。 它应与用于托管实例的托管实例的子网不同，后者用于托管 SSISDB。 否则，它可以是任何子网，为 Azure-SSIS IR 提供自己的静态公共 IP 地址。

   1. 选择 " **VNet 验证**"。 如果验证成功，请选择 "**继续**"。

   ![使用虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. 在 "**摘要**" 部分中，查看 Azure-SSIS IR 的所有设置。 然后选择 "**更新**"。

1. 通过选择 Azure-SSIS IR 的 "**操作**" 列中的 "**启动**" 按钮来启动 Azure-SSIS IR。 启动加入虚拟网络的 Azure-SSIS IR 需要大约20到30分钟的时间。

## <a name="next-steps"></a>后续步骤

详细了解[如何将 Azure-SSIS IR 加入到虚拟网络](join-azure-ssis-integration-runtime-virtual-network.md)。
