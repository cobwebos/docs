---
title: 使用 DMS 迁移到 Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何使用 Azure 数据库迁移服务从本地 SQL Server 迁移到 Azure SQL 数据库托管实例。
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/07/2018
ms.openlocfilehash: 86af0101d84fe9cd44211a931567a85d7b5166e0
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261604"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>使用 DMS 将 SQL Server 迁移到 Azure SQL 数据库托管实例
可使用 Azure 数据库迁移服务将数据库从本地 SQL Server 实例迁移到 [Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance.md)。 有关需要一些手动工作量的其他方法，请参阅[将 SQL Server 实例迁移到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-migrate.md)。

> [!IMPORTANT]
> 从 SQL Server 到 Azure SQL 数据库托管实例的迁移项目处于预览状态，并受 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)的约束。

本教程介绍如何使用 Azure 数据库迁移服务，将 Adventureworks2012 数据库从 SQL Server 的本地实例迁移到 Azure SQL 数据库托管实例。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。
> * 下载迁移报告。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要：

- 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 VNET，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。 [了解使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)。
- 确保 Azure 虚拟网络 (VNET) 网络安全组规则未阻止通信端口 443、53、9354、445、12000。 有关 Azure VNET NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
- 配置[针对源数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 SQL Server（默认情况下为 TCP 端口 1433）。
- 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
- 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
- 遵循[在 Azure 门户中创建 Azure SQL 数据库托管实例](https://aka.ms/sqldbmi)一文中的详细信息创建 Azure SQL 数据库托管实例。
- 确保用于连接源 SQL Server 和目标托管实例的登录名是 sysadmin 服务器角色的成员。
- 创建网络共享，供 Azure 数据库迁移服务用来备份源数据库。
- 确保运行源 SQL Server 实例的服务帐户对你创建的网络共享拥有写入权限，并且源服务器的计算机帐户具有对同一共享的读/写访问权限。
- 请记下在前面创建的网络共享中拥有完全控制权限的 Windows 用户（和密码）。 Azure 数据库迁移服务可模拟用户凭据，将备份文件上传到 Azure 存储容器，以执行还原操作。
- 遵循[使用存储资源管理器管理 Azure Blob 存储资源](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)一文中的步骤创建 Blob 容器并检索其 SAS URI。创建 SAS URI 时，请务必在策略窗口中选择所有权限（读取、写入、删除、列出）。 此详细信息可为 Azure 数据库迁移服务提供你的存储帐户容器的访问权限，以便将用于迁移数据库的备份文件上传到 Azure SQL 数据库托管实例

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，依次选择“所有服务”和“订阅”。

    ![显示门户订阅](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”。

    ![注册资源提供程序](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户中，选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”。

     ![Azure 市场](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”。

    ![创建 Azure 数据库迁移服务实例](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择现有的虚拟网络 (VNET) 或创建一个。
 
    VNET 为 Azure 数据库迁移服务提供源 SQL Server 的访问权限以及目标 Azure SQL 数据库托管实例。

    有关如何在 Azure 门户中创建 VNET 的详细信息，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

    有关更多详细信息，请参阅[使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)一文。

5. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。
   
    ![创建 DMS 服务](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  选择“创建”来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的实例名称，然后选择该实例。
 
3. 选择“+ 新建迁移项目”。

4. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“SQL Server”，然后在“目标服务器类型”文本框中选择“Azure SQL 数据库托管实例”。

   ![创建 DMS 项目](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. 选择“创建”来创建项目。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“源详细信息”屏幕上，指定源 SQL Server 的连接详细信息。

2. 如果尚未在服务器上安装受信任的证书，请选中“信任服务器证书”复选框。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 SSL 连接不提供可靠的安全性。 它们易遭受中间人攻击。 不得在生产环境中或在连接到 Internet 的服务器上依赖使用自签名证书的 SSL。

   ![源详细信息](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. 选择“保存”。

4. 在“选择源数据库”屏幕上，选择用于迁移的“Adventureworks2012”数据库。

   ![选择源数据库](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. 选择“保存”。

## <a name="specify-target-details"></a>指定目标详细信息

1.  在“目标详细信息”屏幕上，指定目标的连接详细信息，该目标是“AdventureWorks2012”数据库要迁移到的已预配的 Azure SQL 数据库托管实例。

    如果尚未预配 Azure SQL 数据库托管实例，请选择“否”以获取帮助预配实例的链接。 仍可继续进行项目创建，然后在 Azure SQL 数据库托管实例准备就绪后返回到此特定项目以执行迁移。   
 
       ![选择目标](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  选择“保存”。

3.  在“项目摘要”屏幕上，查看并确认与迁移项目关联的详细信息。
 
    ![迁移项目摘要](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  选择“保存”。   

## <a name="run-the-migration"></a>运行迁移

1.  选择最近保存的项目，依次选择“+ 新建活动”和“运行迁移”。

    ![新建活动](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  出现提示时，输入源服务器和目标服务器的凭据，然后选择“保存”。

3.  在“选择源数据库”屏幕上，选择要迁移的源数据库。

    ![选择源数据库](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases1.png)

4.  选择“保存”，然后在“配置迁移设置”屏幕上提供以下详细信息：

    | | |
    |--------|---------|
    |**网络位置共享** | 可让 Azure 数据库迁移服务备份源数据库的本地网络共享。 运行源 SQL Server 实例的服务帐户必须在此网络共享中拥有写入特权。 在网络共享中提供服务器的 FQDN 或 IP 地址，例如“'\\\servername.domainname.com\backupfolder”或“\\\IP address\backupfolder”。|
    |**用户名** | Windows 用户名，Azure 数据库迁移服务可以模拟它，并使用它将备份文件上传到 Azure 存储容器，以执行还原操作。 |
    |**密码** | 用户密码。 |
    |**存储帐户设置** | SAS URI 为 Azure 数据库迁移服务提供你的存储帐户容器的访问权限，服务可将备份文件上传到此容器，并用于将数据库迁移到 Azure SQL 数据库托管实例。 [了解如何获取 Blob 容器的 SAS URI](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![配置迁移设置](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings1.png)

5.  选择“保存”，然后在“迁移摘要”屏幕上的“活动名称”文本框中指定迁移活动的名称。

6. 展开“验证选项”部分以显示“选择验证选项”屏幕，指定是否要验证已迁移数据库的查询正确性，然后选择“保存”。  

    ![迁移摘要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary1.png)

7. 选择“运行迁移”。

    迁移活动窗口随即出现，活动的状态为“挂起”。

   ![挂起的迁移活动](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-pending.png)

## <a name="monitor-the-migration"></a>监视迁移

1. 在迁移活动屏幕上，选择“刷新”来更新显示，直到看到迁移的状态显示为“已完成”。
 
   ![已完成的迁移活动](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-finished.png)

2. 迁移完成后，选择“下载报告”可获取报告，其上列出了与迁移过程相关的详细信息。
 
3. 验证目标 Azure SQL 数据库托管实例环境上的目标数据库。

## <a name="next-steps"></a>后续步骤

- 有关展示了如何使用 T-SQL RESTORE 命令将数据库迁移到托管实例的教程，请参阅[使用 restore 命令将备份还原到托管实例](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)。
- 有关托管实例的信息，请参阅[什么是托管实例](../sql-database/sql-database-managed-instance.md)。
- 有关将应用连接到托管实例的信息，请参阅[连接应用程序](../sql-database/sql-database-managed-instance-connect-app.md)。
