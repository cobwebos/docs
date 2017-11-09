---
title: "将 Azure-SSIS 集成运行时加入 VNET | Microsoft Docs"
description: "了解如何将 Azure-SSIS 集成运行时加入 Azure 虚拟网络。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: 8a58f55bd627594145661e1c8d5c1da360cd1e30
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>将 Azure-SSIS 集成运行时加入虚拟网络
如果存在以下情况之一，则必须将 Azure-SSIS 集成运行时 (IR) 加入 Azure 虚拟网络 (VNet)： 

- 在属于 VNet 的 SQL Server 托管实例（个人预览版）上承载 SSIS 目录数据库。
- 想要从 Azure-SSIS 集成运行时中运行的 SSIS 包连接到本地数据存储。

 使用 Azure 数据工厂版本 2（预览版）可将 Azure-SSIS 集成运行时加入经典 VNet。 目前尚不支持 Azure 资源管理器 VNet。 但是，可根据以下部分中所示解决此问题。 

 > [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用正式版 (GA) 1 版本的数据工厂服务，请参阅 [数据工厂版本 1 文档](v1/data-factory-introduction.md)。

如果 SSIS 包仅访问公有云数据存储，则不需要将 Azure-SSIS IR 加入 VNet。 如果 SSIS 包访问本地数据存储，则必须将 Azure-SSIS IR 加入已连接到本地网络的 VNet。 如果 SSIS 目录承载于不在 VNet 上的 Azure SQL 数据库中，则需要打开相应的端口。 如果 SSIS 目录承载在经典 VNet 上的 Azure SQL 托管实例中，则可将 Azure-SSIS IR 加入到相同的经典 VNet，或加入到已建立经典到经典 VNet 连接、与 Azure SQL 托管实例所在的 VNet 不同的经典 VNet 中。 以下部分提供了更多详细信息。  

## <a name="access-on-premises-data-stores"></a>访问本地数据存储
如果 SSIS 包访问本地数据存储，请将 Azure-SSIS 集成运行时加入已连接到本地网络的 VNet 中。 下面是几个要点： 

- 如果没有任何现有 VNet 连接到本地网络，请先创建 Azure-SSIS 集成运行时要加入到的[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置从该 VNet 到本地网络的站点到站点 [VPN 网关连接](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 连接。
- 如果已有现有的经典 VNet 连接到 Azure-SSIS 集成运行时所在同一位置中的本地网络，则可将 Azure-SSIS 集成运行时加入该 VNet。
- 如果已有现有的经典 VNet 连接到与 Azure-SSIS 集成运行时所在位置不同的位置中的本地网络，可以先创建 Azure-SSIS 集成运行时要加入到的[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到经典 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 连接。
- 如果已有现有的 Azure 资源管理器 VNet 连接到本地网络，请先创建 Azure-SSIS 集成运行时要加入到的[经典 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然后，配置[经典到 Azure 资源管理器 VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 连接。

## <a name="domain-name-services-server"></a>域名服务服务器 
如果需要在 Azure-SSIS 集成运行时加入的 VNet 中使用自己的域名服务 (DNS) 服务器，请遵循指导以[确保 VNet 中 Azure-SSIS 集成运行时的节点可以解析 Azure 终结点](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>网络安全组
如果需要在 Azure-SSIS 集成运行时加入的 VNet 中实现网络安全组 (NSG)，请允许通过以下端口传送入站/出站流量：

| 端口 | 方向 | 传输协议 | 目的 | 入站源/出站目标 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | 入站 | TCP | Azure 服务使用这些端口来与 VNet 中 Azure-SSIS 集成运行时的节点通信。 | Internet | 
| 443 | 出站 | TCP | VNet 中 Azure-SSIS 集成运行时的节点使用此端口来访问 Azure 服务，例如 Azure 存储、事件中心等。 | INTERNET | 
| 1433<br/>11000-11999<br/>14000-14999  | 出站 | TCP | VNet 中 Azure-SSIS 集成运行时的节点使用这些端口来访问 Azure SQL 数据库服务器承载的 SSISDB（不适用于 Azure SQL 托管实例承载的 SSISDB）。 | Internet | 

## <a name="script-to-configure-vnet"></a>用于配置 VNet 的脚本 
可以使用[此文](create-azure-ssis-integration-runtime.md)中的引导式 PowerShell 脚本在 VNet 中预配 Azure-SSIS 集成运行时。 该脚本会自动配置 VNet 权限和设置，以便将 Azure-SSIS 集成运行时加入 VNet。  


## <a name="use-portal-to-configure-vnet"></a>使用门户配置 VNet
运行脚本是配置 VNet 的最简单方法。 如果没有配置该 VNet 所需的访问权限或自动配置失败，该 VNet 的所有者或者你可以尝试通过以下步骤手动进行配置：

### <a name="find-the-resource-id-for-your-azure-vnet"></a>找到 Azure VNet 的资源 ID。
 
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“更多服务”。 筛选并选择“虚拟网络(经典)”。
3. 在列表中筛选并选择自己的**虚拟网络**。 
4. 在“虚拟网络(经典)”页中选择“属性”。 

    ![经典 VNet 资源 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 单击“资源 ID”对应的复制按钮，将经典网络的资源 ID 复制到剪贴板。 将剪贴板中的 ID 保存到 OneNote 或某个文件中。
6. 在左侧菜单中单击“子网”，确保**可用地址**的数目大于 Azure-SSIS 集成运行时中的节点数。

    ![VNet 中的可用地址数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 将 **MicrosoftAzureBatch** 加入 VNet 的“经典虚拟机参与者”角色。 
    1. 在左侧菜单中单击“访问控制(IAM)”，并在工具栏中单击“添加”。
    
        ![“访问控制”->“添加”](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. 在“添加权限”页中，为“角色”选择“经典虚拟机参与者”。 在“选择”文本框中键入 **MicrosoftAzureBatch**，在搜索结果列表中选择“MicrosoftAzureBatch”。 
    
        ![添加权限 - 搜索](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. 单击“保存”以保存设置并关闭页面。
    
        ![保存访问设置](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 确认参与者列表中出现了“MicrosoftAzureBatch”。
    
        ![确认 AzureBatch 访问权限](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. 验证是否已将 Azure Batch 提供程序注册到包含 VNet 的 Azure 订阅中，或注册 Azure Batch 提供程序。 如果订阅中已包含 Azure Batch 帐户，则已经为 Azure Batch 注册了订阅。
    1. 在 Azure 门户上的左侧菜单中，单击“订阅”。 
    2. 选择**订阅**。 
    3. 在左侧单击“资源提供程序”，确认 `Microsoft.Batch` 是注册的提供程序。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果列表中未出现 `Microsoft.Batch`，若要注册该提供程序，请在订阅中[创建一个空的 Azure Batch 帐户](../batch/batch-account-create-portal.md)。 稍后可以删除该帐户。 
         


## <a name="next-steps"></a>后续步骤
有关 Azure-SSIS 运行时的详细信息，请参阅以下主题： 

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般概念性信息。 
- [教程：将 SSIS 包部署到 Azure](tutorial-deploy-ssis-packages-azure.md)。 此文提供有关创建 Azure-SSIS IR，并使用 Azure SQL 数据库来承载 SSIS 目录的分步说明。 
- [如何创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。 此文延伸了教程的内容，提供有关使用 Azure SQL 托管实例（人预览版）以及将 IR 加入 VNet 的说明。 
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索有关 Azure-SSIS IR 的信息，以及返回的信息中的状态说明。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过在 Azure-SSIS IR 中添加更多节点来扩展 IR。 
