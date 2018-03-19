---
title: "使用 Azure 数据库迁移服务将 SQL Server 迁移到 Azure SQL 数据库托管实例 | Microsoft Docs"
description: "了解如何使用 Azure 数据库迁移服务从本地 SQL Server 迁移到 Azure SQL 数据库托管实例。"
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d74a273061912ea2bdcc39301ce9a727b07ade41
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance"></a>将 SQL Server 迁移到 Azure SQL 数据库托管实例
可以使用 Azure 数据库迁移服务将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库。 本教程介绍如何使用 Azure 数据库迁移服务，将 **Adventureworks2012** 数据库从 SQL Server 的本地实例迁移到 Azure SQL 数据库。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要：

- 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 VNET，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。 [了解使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)。
- 确保 Azure 虚拟网络 (VNET) 网络安全组规则未阻止通信端口 443、53、9354、445、12000。 有关 Azure VNET NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)一文。
- [配置 Windows 防火墙以便访问源数据库引擎](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server。
- 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
- 遵循[在 Azure 门户中创建 Azure SQL 数据库托管实例](https://aka.ms/sqldbmi)一文中的详细信息创建 Azure SQL 数据库托管实例。
- 确保用于连接源 SQL Server 和目标托管实例的登录名是 sysadmin 服务器角色的成员。
- 创建网络共享，供 Azure 数据库迁移服务用来备份源数据库。
- 确保运行源 SQL Server 实例的服务帐户在创建的网络共享中拥有写入特权。
- 记下在前面创建的网络共享中拥有完全控制特权的 Windows 用户（和密码）。 Azure 数据库迁移服务将模拟用户凭据，将备份文件上传到 Azure 存储容器，以执行还原操作。
- 遵循[使用存储资源管理器（预览版）管理 Azure Blob 存储资源](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)一文中的步骤创建 Blob 容器并检索其 SAS URI。创建 SAS URI 时，请务必在策略窗口中选择所有权限（读取、写入、删除、列出）。 这样，Azure 数据库迁移服务便可以访问你的存储帐户容器，以便将用于迁移数据库的备份文件上传到 Azure SQL 数据库托管实例

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1.  登录到 Azure 门户，依次选择“所有服务”和“订阅”。
![显示门户订阅](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。
![显示资源提供程序](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”。
![注册资源提供程序](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>创建实例

1.  在 Azure 门户中，选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”。

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  在“Azure 数据库迁移服务（预览版）”屏幕上，选择“创建”。

    ![创建 Azure 数据库迁移服务实例](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  在“数据库迁移服务”屏幕上，指定服务名称、订阅、资源组、虚拟网络和定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。 *Azure 数据库迁移服务目前以预览版提供，不收取费用。*

    **网络：**选择现有的或创建新的 VNET，使 Azure 数据库迁移服务能够访问源 SQL Server 和目标 Azure SQL 数据库托管实例。 [了解使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)。

    有关如何在 Azure 门户中创建 VNET 的详细信息，请参阅[使用 Azure 门户创建包含多个子网的虚拟网络](https://aka.ms/DMSVnet)。

    ![创建 DMS 服务](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  选择“创建”来创建服务。


## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，请在 Azure 门户中找到并打开它。

1.  选择“+ 新建迁移项目”。

1.  在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“SQL Server”，然后在“目标服务器类型”文本框中选择“Azure SQL 数据库托管实例”。

    ![创建 DMS 项目](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  选择“创建”来创建项目。

## <a name="specify-source-details"></a>指定源详细信息

1.  在“源详细信息”屏幕上，指定源 SQL Server 的连接详细信息。

    ![源详细信息](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  选择“保存”，然后选择用于迁移的“Adventureworks2012”数据库。

    ![选择源数据库](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>指定目标详细信息

1.  选择“保存”，然后在“目标详细信息”屏幕上指定目标的连接详细信息，这是 **AdventureWorks2012** 数据库要迁移到的预配 Azure SQL 数据库托管实例。

    ![选择目标](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  选择“保存”。

1.  在“项目摘要”屏幕上，查看并确认与迁移项目关联的详细信息。

## <a name="run-the-migration"></a>运行迁移

1.  选择最近保存的项目，依次选择“+ 新建活动”和“运行迁移”。

    ![新建活动](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  出现提示时，输入源服务器和目标服务器的凭据，然后选择“保存”。

1.  在“映射到目标数据库”屏幕上，选择要迁移的源数据库。

    ![选择源数据库](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  在“配置迁移设置”屏幕上选择“保存”，并提供以下详细信息：

    | | |
    |--------|---------|
    |**服务器备份位置** | 可让 Azure 数据库迁移服务备份源数据库的本地网络共享。 运行源 SQL Server 实例的服务帐户必须在此网络共享中拥有写入特权。 |
    |**用户名** | Windows 用户名，Azure 数据库迁移服务可以模拟它，并使用它将备份文件上传到 Azure 存储容器，以执行还原操作。 |
    |**密码** | 上述用户的密码。 |
    |**存储 SAS URI** | 可让 Azure 数据库迁移服务访问你的存储帐户容器，以便将用于迁移数据库的备份文件上传到 Azure SQL 数据库托管实例的 SAS URI。  [了解如何获取 Blob 容器的 SAS URI](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![配置迁移设置](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  在“迁移摘要”屏幕上选择“保存”，在“活动名称”文本框中指定迁移活动的名称。

    ![迁移摘要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>监视迁移

1.  选择迁移活动以查看活动状态。

1.  完成迁移后，验证目标 Azure SQL 数据库托管实例上的目标数据库。

    ![监视迁移](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

