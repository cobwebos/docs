---
title: 预配 Azure-SSIS 集成运行时 | Microsoft Docs
description: 了解如何在 Azure 数据工厂中预配 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: abbf64fadfdd6dd194afe0fb498303ab18a9e069
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425336"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中预配 Azure-SSIS 集成运行时
本教程提供使用 Azure 门户在 Azure 数据工厂中预配 Azure-SSIS 集成运行时 (IR) 的步骤。 然后，可以使用 SQL Server Data Tools 或 SQL Server Management Studio 在 Azure 的此运行时中部署并运行 SQL Server Integration Services (SSIS) 包。 有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教程中，将完成以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

## <a name="prerequisites"></a>先决条件
- **Azure 订阅**。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 
- **Azure SQL 数据库服务器** 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 Azure 数据工厂在此数据库服务器上创建 SSIS 目录（SSISDB 数据库）。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 数据库而无需跨 Azure 区域。 
- 根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单个数据库创建、可以充当弹性池的一部分创建，也可以在托管实例（预览版）中创建，并可在公共网络中访问或者通过加入虚拟网络来访问。 如果将 Azure SQL 数据库与虚拟网络服务终结点/托管实例（预览版）配合使用以托管 SSISDB，或者需要访问本地数据，则需将 Azure-SSIS IR 加入虚拟网络，详见[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 
- 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 将 Azure SQL 数据库与虚拟网络服务终结点/托管实例（预览版）配合使用以托管 SSISDB 时，这不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。 
- 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。 
- 若要连接到数据库服务器，可以将 SQL 身份验证与服务器管理员凭据配合使用，也可以将 Azure Active Directory (AAD) 身份验证与 Azure 数据工厂 (ADF) 托管服务标识 (MSI) 配合使用。  对于后者，需将 ADF MSI 添加到有权访问数据库服务器的 AAD 组中，详见[使用 AAD 身份验证创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 
- 确认 Azure SQL 数据库服务器没有 SSIS 目录（SSISDB 数据库）。 预配 Azure-SSIS IR 时不支持使用现有 SSIS 目录。 

> [!NOTE]
> - 要查看目前提供数据工厂的 Azure 区域的列表，请在以下页面上选择感兴趣的区域，然后展开“分析”以找到“数据工厂”：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。 
> - 要查看目前提供 Azure-SSIS 集成运行时的 Azure 区域的列表，请在以下页面上选择感兴趣的区域，然后展开“分析”以找到“SSIS 集成运行时”：[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/)。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。 
1. 登录到 [Azure 门户](https://portal.azure.com/)。 
1. 在左侧菜单中选择“新建”，然后依次选择“数据 + 分析”、“数据工厂”。 

   ![在“新建”窗格中选择“数据工厂”](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. 在“新建数据工厂”页的“名称”下，输入 **MyAzureSsisDataFactory**。 

   ![“新建数据工厂”页](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;MyAzureSsisDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. 对于“订阅”，请选择要在其中创建数据工厂的 Azure 订阅。 
1. 对于**资源组**，请执行以下步骤之一： 

   - 选择“使用现有”，并从列表中选择现有的资源组。 
   - 选择“新建”，并输入资源组的名称。 

   若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。 
1. 对于“版本”，请选择“V2 (预览版)”。 
1. 对于“位置”，请选择数据工厂所在的位置。 此列表只显示支持创建数据工厂的位置。 
1. 选择“固定到仪表板”。 
1. 选择“创建”。 
1. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴： 

   ![“正在部署数据工厂”磁贴](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 创建完成后，会显示“数据工厂”页。 

   ![数据工厂主页](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. 选择“创作和监视”，在单独的选项卡中打开数据工厂用户界面 (UI)。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时

1. 在“入门”页中，选择“配置 SSIS 集成运行时”磁贴。 

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. 在“集成运行时安装”的“常规设置”页中完成以下步骤： 

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. 对于“名称”，请输入集成运行时的名称。 

   b. 对于“说明”，请输入集成运行时的说明。 

   c. 对于“位置”，请选择集成运行时的位置。 界面上仅显示支持的位置。 建议选择承载 SSISDB 所需的数据库服务器的位置。 

   d. 对于“节点大小”，请选择集成运行时群集中的节点大小。 仅显示支持的节点大小。 如果需要运行多个计算/内存密集型包，请选择较大的节点大小（纵向扩展）。 

   e. 对于“节点数”，请选择集成运行时群集中的节点数。 仅显示支持的节点数。 如果需要并行运行多个包，请选择包含许多节点的大型群集（横向扩展）。 

   f. 对于“版本/许可证”，请选择集成运行时的 SQL Server 版本/许可证：Standard 或 Enterprise。 如果需要在集成运行时上使用高级功能，请选择“Enterprise”。 

   g. 对于“节省资金”，请选择适用于集成运行时的 Azure 混合权益 (AHB) 选项：“是”或“否”。 如果需要自带具有软件保障的 SQL Server 许可证，以便充分利用使用混合权益带来的成本节省，请选择“是”。 

   h. 单击“下一步”。 

1. 在“SQL 设置”页上，完成以下步骤： 

   ![SQL 设置](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 对于“订阅”，请选择使用数据库服务器来托管 SSISDB 的 Azure 订阅。 

   b. 对于“位置”，请选择用于托管 SSISDB 的数据库服务器的位置。 建议选择集成运行时的位置。 

   c. 对于“目录数据库服务器终结点”，请选择用于承载 SSISDB 的数据库服务器的终结点。 根据所选数据库服务器的不同，SSISDB 的创建方式也不相同：可以代表你作为单独的数据库创建、可以充当弹性池的一部分创建，也可以在托管实例（预览版）中创建，并可在公共网络中访问或者通过加入虚拟网络来访问。 有关选择用来承载 SSISDB 的数据库服务器类型的指导，请参阅[比较 SQL 数据库与托管实例（预览版）](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview)。 如果选择将 Azure SQL 数据库与虚拟网络服务终结点/托管实例（预览版）配合使用以托管 SSISDB，或者需要访问本地数据，则需将 Azure-SSIS IR 加入虚拟网络。 请参阅[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 

   d. 在“使用 AAD 身份验证...”复选框中，选择数据库服务器用来托管 SSISDB 的身份验证方法：SQL 或使用 Azure 数据工厂 (ADF) 托管服务标识 (MSI) 的 Azure Active Directory (AAD)。 如果勾选它，需将 ADF MSI 添加到有权访问数据库服务器的 AAD 组中，详见[使用 AAD 身份验证创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 

   e. 对于“管理员用户名”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证用户名。 

   f. 对于“管理员密码”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证密码。 

   g. 对于“目录数据库服务层”，请选择用于承载 SSISDB 的数据库服务器的服务层：基本/标准/高级层或弹性池名称。 

   h. 单击“测试连接”，如果成功，则单击“下一步”。 

1. 在“高级设置”页上，完成以下步骤： 

   ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. 对于“每个节点的最大并行执行数”，请选择要在集成运行时群集中并发执行的最大包数（按节点）。 仅显示支持的包数。 如果需要使用多个核心来运行单个属于计算/内存密集型的大型/重型包，则请选择较低的数字。 如果需要在单个核心中运行一个或多个小型/轻型包，则请选择较高的数字。 

   b. 对于“自定义安装容器 SAS URI”，可以选择输入 Azure 存储 Blob 容器（在其中存储了安装脚本及其关联的文件）的共享访问签名 (SAS) 统一资源标识符 (URI)，具体请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 

   c. 在“选择 VNet...”复选框中，选择是否要将集成运行时加入虚拟网络。 如果将 Azure SQL 数据库与虚拟网络服务终结点/托管实例（预览版）配合使用以托管 SSISDB，或者需要访问本地数据，则应将它选中，详见[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)。 

1. 单击“完成”开始创建集成运行时。 

   > [!IMPORTANT]
   > 完成此过程大约需要 20 到 30 分钟。
   >
   > 数据工厂服务将连接到 Azure SQL 数据库服务器，以便准备 SSIS 目录（SSISDB 数据库）。 
   > 
   > 预配 Azure-SSIS IR 的实例时，还会安装 Azure Feature Pack for SSIS 和 Access Redistributable。 除了内置组件支持的数据源外，这些组件还提供与 Excel 和 Access 文件和各种 Azure 数据源的连接。 还可以安装其他组件。 有关详细信息，请参阅 [Azure-SSIS 集成运行时的自定义设置](how-to-configure-azure-ssis-ir-custom-setup.md)。 

1. 在“连接”选项卡中，根据需要切换到“集成运行时”。 选择“刷新”可刷新状态。 

   ![创建状态，使用“刷新”按钮](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. 使用“操作”列中的链接可以停止/启动、编辑或删除集成运行时。 使用最后一个链接可以查看集成运行时的 JSON 代码。 仅当 IR 已停止时，才会启用编辑和删除按钮。 

   ![“操作”列中的链接](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。 

   ![用于查看现有 IR 的选择](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 选择“新建”以创建 Azure-SSIS IR。 

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在“集成运行时安装”窗口中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”，然后选择“下一步”。 

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

## <a name="deploy-ssis-packages"></a>部署 SSIS 包
现在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 来将 SSIS 包部署到 Azure。 连接到托管 SSIS 目录（SSISDB 数据库）的 Azure SQL 数据库服务器。 Azure SQL 数据库服务器的名称采用 `<servername>.database.windows.net` 格式。 

请参阅 SSIS 文档中的以下文章： 

- [在 Azure 上部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [连接到 Azure 上的 SSIS 目录](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [在 Azure 上计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>后续步骤
本教程介绍了以下操作： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
> [在云中复制数据](tutorial-copy-data-portal.md)
