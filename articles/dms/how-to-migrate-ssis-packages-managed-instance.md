---
title: 将 SQL Server Integration Services 包迁移到 Azure SQL 数据库托管实例 |Microsoft Docs
description: 了解如何将 SQL Server Integration Services 包迁移到 Azure SQL 数据库托管实例。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 82a047616c199e37bfa22f53e02f3f7b224b47c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083179"
---
# <a name="migrate-sql-server-integration-services-packages-to-an-azure-sql-database-managed-instance"></a>将 SQL Server Integration Services 包迁移到 Azure SQL 数据库托管实例
如果你使用 SQL Server Integration Services (SSIS)，并想要迁移从源到目标 Azure SQL 数据库托管实例承载的 SSISDB 的 SQL Server 承载 SSISDB 的 SSIS 项目/包，可以使用 Azure 数据库迁移服务。

如果您使用的 SSIS 的版本低于 2012年或之前迁移 SSIS 项目/包使用非 SSISDB 包存储区类型，您需要使用 Integration Services 项目转换向导，也可以从 SSMS 启动转换。 有关详细信息，请参阅文章[将项目转换为项目部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务 (DMS) 目前不支持 Azure SQL 数据库作为目标迁移目标。 若要重新部署到 Azure SQL 数据库的 SSIS 项目/包，请参阅文章[重新部署 SQL Server Integration Services 包部署到 Azure SQL 数据库](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>必备组件

若要完成这些步骤，需满足以下条件：

* 若要使用 Azure 资源管理器部署模型，它提供通过使用站点到站点连接到你的本地源服务器的 Azure 数据库迁移服务创建 Azure 虚拟网络 (VNet) [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)。 有关详细信息，请参阅文章[Azure SQL 数据库的网络拓扑管理使用 Azure 数据库迁移服务迁移实例]( https://aka.ms/dmsnetworkformi)。 有关创建 VNet 的详细信息，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)，尤其是提供了分步详细信息的快速入门文章。
* 若要确保你的 VNet 网络安全组规则未阻止到 Azure 数据库迁移服务的以下入站的通信端口：443、53、9354、445、12000。 有关 Azure VNet NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)一文。
* 若要配置你[针对源数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access?view=sql-server-2017)。
* 若要打开 Windows 防火墙，以允许 Azure 数据库迁移服务访问源 SQL Server，默认情况下为 TCP 端口 1433年。
* 如果使用动态端口运行多个命名 SQL Server 实例，则可能需要启用 SQL Browser 服务并允许通过防火墙访问 UDP 端口 1434，以便 Azure 数据库迁移服务可连接到源服务器上的命名实例。
* 如果在源数据库的前面使用了防火墙设备，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库，并通过 SMB 端口 445 访问文件。
* Azure SQL 数据库托管实例来承载 SSISDB。 如果需要创建一个，请遵循本文中的详细信息[创建 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)。
* 若要确保用于连接到源的登录名 SQL Server 和目标托管的实例是 sysadmin 服务器角色的成员。
* 若要验证 SSIS 在 Azure 数据工厂 (ADF) 与目标 Azure SQL 数据库承载 SSISDB 包含 Azure SSIS 集成运行时 (IR) 预配托管实例 (如本文所述[创建 Azure SSISAzure 数据工厂中的 integration runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime))。

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包

评估源 SSISDB 的不尚未集成到数据库迁移助手 (DMA)，而将 SSIS 项目/包将评估/验证如正在重新部署到目标 Azure SQL 数据库托管实例承载的 SSISDB。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”  ，然后选择“订阅”  。

    ![显示门户订阅](media/how-to-migrate-ssis-packages-mi/portal-select-subscriptions.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”  。

    ![显示资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”   。

    ![注册资源提供程序](media/how-to-migrate-ssis-packages-mi/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例

1. 在 Azure 门户中，选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”    。

     ![Azure 市场](media/how-to-migrate-ssis-packages-mi/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/how-to-migrate-ssis-packages-mi/dms-create1.png)

3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称  。

4. 选择要在其中创建 DMS 实例的位置。

5. 选择一个现有 VNet，或者创建一个。

    VNet 提供 Azure 数据库迁移服务访问源 SQL Server 和目标 Azure SQL 数据库托管的实例。

    有关如何在 Azure 门户中创建 VNet 的详细信息，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

    有关更多详细信息，请参阅[使用 Azure 数据库迁移服务迁移 Azure SQL 数据库托管实例的网络拓扑](https://aka.ms/dmsnetworkformi)一文。

6. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![创建 DMS 服务](media/how-to-migrate-ssis-packages-mi/dms-create-service2.png)

7. 选择“创建”  来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务实例后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”  ，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”  。

    ![查找 Azure 数据库迁移服务的所有实例](media/how-to-migrate-ssis-packages-mi/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索创建的实例名称，然后选择该实例  。

3. 选择“+ 新建迁移项目”  。

4. 上**新建迁移项目**屏幕上，在指定项目的名称**源服务器类型**文本框中，选择**SQL Server**，在**目标服务器类型**文本框中，选择**Azure SQL 数据库托管实例**，，然后为**选择活动类型**，选择**SSIS 包迁移**.

   ![创建 DMS 项目](media/how-to-migrate-ssis-packages-mi/dms-create-project2.png)

5. 选择“创建”  来创建项目。

## <a name="specify-source-details"></a>指定源详细信息

1. 在“迁移源详细信息”  屏幕上，指定源 SQL Server 的连接详细信息。

2. 如果尚未在服务器上安装受信任的证书，请选中“信任服务器证书”复选框  。

    如果没有安装受信任的证书，SQL Server 会在实例启动时生成自签名证书。 此证书用于加密客户端连接的凭据。

    > [!CAUTION]
    > 使用自签名证书加密的 SSL 连接不提供可靠的安全性。 它们易遭受中间人攻击。 不得在生产环境中或在连接到 Internet 的服务器上依赖使用自签名证书的 SSL。

   ![源详细信息](media/how-to-migrate-ssis-packages-mi/dms-source-details1.png)

3. 选择“保存”。 

## <a name="specify-target-details"></a>指定目标详细信息

1. 上**迁移目标详情**屏幕上，指定目标的连接详细信息。

     ![目标详细信息](media/how-to-migrate-ssis-packages-mi/dms-target-details2.png)

2. 选择“保存”。 

## <a name="review-the-migration-summary"></a>查看迁移摘要

1. 在“迁移摘要”屏幕的“活动名称”文本框中指定迁移活动的名称。  

2. 有关**SSIS 项目和环境覆盖选项**，指定是否覆盖或忽略现有 SSIS 项目和环境。

    ![迁移项目摘要](media/how-to-migrate-ssis-packages-mi/dms-project-summary2.png)

3. 查看并验证与迁移项目关联的详细信息。

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”  。

## <a name="next-steps"></a>后续步骤

* 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。
