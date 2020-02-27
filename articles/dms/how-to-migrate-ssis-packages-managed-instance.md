---
title: 将 SSIS 包迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务或数据迁移助手将 SQL Server Integration Services （SSIS）包和项目迁移到 Azure SQL 数据库托管实例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a0669724888f02672d18ef9e8f725eef1c744f90
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650958"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>将 SQL Server Integration Services 包迁移到 Azure SQL 数据库托管实例
如果使用 SQL Server Integration Services （SSIS）并想要将 SSIS 项目/包从 SQL Server 托管的源 SSISDB 迁移到由 Azure SQL 数据库托管实例托管的目标 SSISDB，则可以使用 Azure 数据库迁移服务。

如果你使用的 SSIS 版本早于2012或你使用非 SSISDB 包存储区类型，则在迁移你的 SSIS 项目/包之前，你需要使用 "Integration Services 项目转换向导" （也可以从 SSMS 启动）来转换它们。 有关详细信息，请参阅文章[将项目转换为项目部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务（DMS）目前不支持作为目标迁移目标的 Azure SQL 数据库。 若要将 SSIS 项目/包重新部署到 Azure SQL 数据库，请参阅将[SQL Server Integration Services 包重新部署到 AZURE Sql 数据库](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)一文。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>必备条件

若要完成这些步骤，需满足以下条件：

* 若要使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Microsoft Azure 虚拟网络，可以使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)为本地源服务器提供站点到站点连接。 有关详细信息，请参阅文章[使用 Azure 数据库迁移服务迁移 AZURE SQL 数据库托管实例的网络拓扑]( https://aka.ms/dmsnetworkformi)。 有关创建虚拟网络的详细信息，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)，尤其是包含分步详细信息的快速入门文章。
* 若要确保虚拟网络安全组规则不会阻止以下到 Azure 数据库迁移服务的入站通信端口：443、53、9354、445、12000。 有关虚拟网络 NSG 流量筛选的详细信息，请参阅[筛选网络流量和网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)一文。
* 为[源数据库引擎访问配置 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)。
* 若要打开 Windows 防火墙以允许 Azure 数据库迁移服务访问源 SQL Server，默认情况下，该端口为 TCP 端口1433。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
* 用于托管 SSISDB 的 Azure SQL 数据库托管实例。 如果需要创建一个 Azure SQL 数据库托管实例，请遵循[创建](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的详细信息。
* 为了确保用于连接源 SQL Server 和目标托管实例的登录名是 sysadmin 服务器角色的成员。
* 若要验证是否已在 Azure 数据工厂（ADF）中预配 SSIS，其中包含由 Azure SQL 数据库托管实例托管的目标 SSISDB Azure-SSIS Integration Runtime （IR）（如在[Azure 数据工厂中创建 AZURE SSIS 集成运行时](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)中所述）。

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包

虽然源 SSISDB 评估尚未集成到数据库迁移助手（DMA）中，但你的 SSIS 项目/包将进行评估/验证，因为它们会重新部署到 Azure SQL 数据库托管实例上托管的目标 SSISDB。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”，然后选择“订阅”。

    ![显示门户订阅](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”。

    ![显示资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”。

    ![注册资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户中，选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”。

     ![Azure 市场](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”。

    ![创建 Azure 数据库迁移服务实例](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称。

4. 选择要在其中创建 DMS 实例的位置。

5. 选择现有虚拟网络或创建一个虚拟网络。

    虚拟网络提供 Azure 数据库迁移服务，可访问源 SQL Server 和目标 Azure SQL 数据库托管实例。

    有关如何在 Azure 门户中创建虚拟网络的详细信息，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

    有关更多详细信息，请参阅[使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)一文。

6. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![创建 DMS 服务](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. 选择“创建”来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务实例后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”。

    ![查找 Azure 数据库迁移服务的所有实例](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索创建的实例名称，然后选择该实例。

3. 选择“+ 新建迁移项目”。

4. 在 "**新建迁移项目**" 屏幕上，指定项目的名称，在 "**源服务器类型**" 文本框中，选择 " **SQL Server**，在"**目标服务器类型**"文本框中，选择" **Azure SQL 数据库托管实例**"，然后对于"**选择活动类型**"，选择" **SSIS 包迁移**"。

   ![创建 DMS 项目](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. 选择“创建”来创建项目。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“迁移源详细信息”屏幕上，指定源 SQL Server 的连接详细信息。

2. 如果尚未在服务器上安装受信任的证书，请选中“信任服务器证书”复选框。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 SSL 连接不提供可靠的安全性。 它们易遭受中间人攻击。 不得在生产环境中或在连接到 Internet 的服务器上依赖使用自签名证书的 SSL。

   ![源详细信息](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. 选择“保存”。

## <a name="specify-target-details"></a>指定目标详细信息

1. 在 "**迁移目标详细信息**" 屏幕上，指定目标的连接详细信息。

     ![目标详细信息](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. 选择“保存”。

## <a name="review-the-migration-summary"></a>查看迁移摘要

1. 在“迁移摘要”屏幕的“活动名称”文本框中指定迁移活动的名称。

2. 对于 " **SSIS 项目和环境覆盖" 选项**，指定是覆盖还是忽略现有 SSIS 项目和环境。

    ![迁移项目摘要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 查看并验证与迁移项目关联的详细信息。

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”。

## <a name="next-steps"></a>后续步骤

* 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。
