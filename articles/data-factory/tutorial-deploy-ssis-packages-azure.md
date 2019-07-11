---
title: 预配 Azure-SSIS 集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中预配 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484790"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中预配 Azure-SSIS 集成运行时

本教程提供使用 Azure 门户在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) 的步骤。 Azure-SSIS IR 支持运行部署到由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型），以及部署到文件系统/文件共享/Azure 文件存储中的包（包部署模型）。 预配 Azure-SSIS IR 后，就可以使用熟悉的工具（如 SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) 和命令行实用工具（如 `dtinstall`/`dtutil`/`dtexec`）在 Azure 中部署和运行包。 有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教程中，将完成以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- **Azure SQL 数据库服务器（可选）** 。 如果还没有数据库服务器，请在开始操作之前在 Azure 门户中创建一个。 ADF 进而会在此数据库服务器上创建 SSISDB。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。 
    - 根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单个数据库创建、可以充当弹性池的一部分创建，也可以在托管实例中创建，并可在公用网络中访问或者通过加入虚拟网络来访问。 有关如何选择用于托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 Azure SQL 数据库单一数据库/弹性池/托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果在虚拟网络中结合使用 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例来托管 SSISDB，或需要访问本地数据，那么必须将 Azure-SSIS IR 加入虚拟网络（请参阅[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)）。
    - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。  如果在虚拟网络中结合使用 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例来托管 SSISDB，这并不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。
    - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。
    - 若要连接到数据库服务器，可以将 SQL 身份验证与服务器管理员凭据配合使用，也可以将 Azure Active Directory (AAD) 身份验证与 ADF 托管标识配合使用。 对于后者，需将 ADF 的托管标识添加到有权访问数据库服务器的 AAD 组中，详见[使用 AAD 身份验证创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
    - 确认你的数据库服务器还没有 SSISDB。 预配 Azure-SSIS IR 时不支持使用现有 SSISDB。

> [!NOTE]
> - 有关目前提供 ADF 和 Azure-SSIS IR 的 Azure 区域的列表，请参阅 [ADF + SSIS IR 可用性（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。 
1. 登录到 [Azure 门户](https://portal.azure.com/)。 
1. 在左侧菜单中选择“新建”，然后依次选择“数据 + 分析”、“数据工厂”。    

   ![在“新建”窗格中选择“数据工厂”](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. 在“新建数据工厂”  页的“名称”下，输入 **MyAzureSsisDataFactory**  。 

   ![“新建数据工厂”页](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;MyAzureSsisDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. 对于“订阅”，请选择要在其中创建数据工厂的 Azure 订阅。  
1. 对于**资源组**，请执行以下步骤之一： 

   - 选择“使用现有”，并从列表中选择现有的资源组。  
   - 选择“新建”，并输入资源组的名称。  

   若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。 
1. 对于“版本”，请选择“V2 (预览版)”。   
1. 对于“位置”，请选择数据工厂所在的位置。  此列表只显示支持创建数据工厂的位置。 
1. 选择“固定到仪表板”  。 
1. 选择“创建”  。 
1. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴：  

   ![“正在部署数据工厂”磁贴](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 创建完成后，会显示“数据工厂”页。  

   ![数据工厂主页](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. 选择“创作和监视”，在单独的选项卡中打开数据工厂用户界面 (UI)。  

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时

### <a name="from-the-data-factory-overview"></a>使用数据工厂概览

1. 在“入门”页中，选择“配置 SSIS 集成运行时”磁贴。   

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

### <a name="from-the-authoring-ui"></a>使用创作 UI

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。    

   ![用于查看现有 IR 的选择](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 选择“新建”以创建 Azure-SSIS IR。  

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在“集成运行时安装”窗口中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”，然后选择“下一步”。    

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时

1. 在“集成运行时安装”的“常规设置”页中完成以下步骤：   

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. 对于“名称”，请输入集成运行时的名称。  

   b. 对于“说明”，请输入集成运行时的说明。  

   c. 对于“位置”，请选择集成运行时的位置。  界面上仅显示支持的位置。 建议选择承载 SSISDB 所需的数据库服务器的位置。 

   d. 对于“节点大小”，请选择集成运行时群集中的节点大小  。 仅显示支持的节点大小。 如果需要运行多个计算/内存密集型包，请选择较大的节点大小（纵向扩展）。 

   e. 对于“节点数”，请选择集成运行时群集中的节点数  。 仅显示支持的节点数。 如果需要并行运行多个包，请选择包含许多节点的大型群集（横向扩展）。 

   f. 对于“版本/许可证”，请选择集成运行时的 SQL Server 版本/许可证：  “标准”或“企业”。 如果需要在集成运行时上使用高级功能，请选择“Enterprise”。 

   g. 对于“节省资金”，请选择适用于集成运行时的 Azure 混合权益 (AHB) 选项：  “是”或“否”。 如果需要自带具有软件保障的 SQL Server 许可证，以便充分利用使用混合权益带来的成本节省，请选择“是”。 

   h. 单击“下一步”。  

1. 在“SQL 设置”  页上，完成以下步骤： 

   ![SQL 设置](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 在“创建 SSIS 目录...”对话框中，选择要在 Azure-SSIS IR 中运行的包的部署模型  ：项目部署模型，其中的包将部署到数据库服务器托管的 SSISDB；或包部署模型，其中的包将部署到文件系统/文件共享/Azure 文件。 如果选中此项，需要提供自己的数据库服务器来托管我们将代表你创建和管理的 SSISDB。
   
   b. 对于“订阅”，请选择使用数据库服务器来托管 SSISDB 的 Azure 订阅。  

   c. 对于“位置”，请选择用于托管 SSISDB 的数据库服务器的位置。  建议选择集成运行时的位置。 

   d. 对于“目录数据库服务器终结点”，请选择用于承载 SSISDB 的数据库服务器的终结点。  根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单个数据库创建、可以充当弹性池的一部分创建，也可以在托管实例中创建，并可在公用网络中访问或者通过加入虚拟网络来访问。 有关如何选择用于托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 Azure SQL 数据库单一数据库/弹性池/托管实例](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)。 如果在虚拟网络中选择 Azure SQL 数据库服务器与虚拟网络服务终结点/托管实例来托管 SSISDB，或需要访问本地数据，那么必须将 Azure-SSIS IR 加入虚拟网络（请参阅[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)）。 

   e. 在“使用 AAD 身份验证...”复选框中，选择数据库服务器用来承载 SSISDB 的身份验证方法：  使用 ADF 的托管标识进行 SQL 身份验证或 AAD 身份验证。 如果选中此项，则需要将 ADF 的托管标识添加到有权访问数据库服务器的 AAD 组中，详见[使用 AAD 身份验证创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。 

   f. 对于“管理员用户名”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证用户名。  

   g. 对于“管理员密码”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证密码。  

   h. 对于“目录数据库服务层级”，请选择用于承载 SSISDB 的数据库服务器的服务层级：  基本/标准/高级层或弹性池名称。 

   i. 单击“测试连接”  ，如果成功，则单击“下一步”  。 

1. 在“高级设置”  页上，完成以下步骤： 

   ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. 对于“每个节点的最大并行执行数”，  请选择要在集成运行时群集中并发执行的最大包数（按节点）。 仅显示支持的包数。 如果需要使用多个核心来运行单个属于计算/内存密集型的大型/重型包，则请选择较低的数字。 如果需要在单个核心中运行一个或多个小型/轻型包，则请选择较高的数字。 

   b. 对于“自定义安装容器 SAS URI”，  可以选择输入 Azure 存储 Blob 容器（在其中存储了安装脚本及其关联的文件）的共享访问签名 (SAS) 统一资源标识符 (URI)，具体请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 

   c. 在“选择 VNet...”复选框中，选择是否要将集成运行时加入虚拟网络。  如果结合使用 Azure SQL 数据库服务器与虚拟网络中的虚拟网络服务终结点/托管实例来托管 SSISDB，或需要访问本地数据，则应选中它（请参阅[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)）。 

1. 单击“完成”开始创建集成运行时。  

   > [!NOTE]
   > 此过程应在 5 分钟内完成，不包括任何自定义设置时间。
   >
   > 如果使用 SSISDB，ADF 服务将连接到数据库服务器以准备 SSISDB。 
   > 
   > 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel/Access 文件和各种 Azure 数据源的连接。 还可以安装其他组件，请参阅 [Azure-SSIS IR 的自定义安装](how-to-configure-azure-ssis-ir-custom-setup.md)。

1. 在“连接”选项卡中，根据需要切换到“集成运行时”。   选择“刷新”可刷新状态。  

   ![创建状态，使用“刷新”按钮](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. 使用“操作”列中的链接可以停止/启动、编辑或删除集成运行时。  使用最后一个链接可以查看集成运行时的 JSON 代码。 仅当 IR 已停止时，才会启用编辑和删除按钮。 

   ![“操作”列中的链接](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>部署 SSIS 包

如果使用 SSISDB，则可以将包部署到其中，并使用通过服务器终结点连接到数据库服务器的 SSDT/SSMS 工具在 Azure-SSIS IR 上运行它们。 对于具有公共终结点的 Azure SQL 数据库服务器/托管实例，服务器终结点格式分别为 `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`。 如果不使用 SSISDB，则可以将包部署到文件系统/文件共享/Azure 文件存储中，并使用 `dtinstall`/`dtutil`/`dtexec` 命令行实用工具在 Azure-SSIS IR 上运行它们。 有关详细信息，请参阅[部署 SSIS 包](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 在这两种情况下，还可以使用 ADF 管道中的“执行 SSIS 包”活动在 Azure-SSIS IR 上运行已部署的包，请参阅[将 SSIS 包执行作为一级 ADF 活动调用](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

另请参阅 SSIS 文档中的以下文章： 

- [在 Azure 中部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [连接到 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [在 Azure 中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。
> * 部署 SSIS 包

若要了解如何自定义 Azure-SSIS Integration Runtime，请参阅下文： 

> [!div class="nextstepaction"]
> [自定义 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)