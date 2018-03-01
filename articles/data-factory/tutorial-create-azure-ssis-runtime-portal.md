---
title: "使用 Azure 数据工厂预配 SSIS 集成运行时 | Microsoft Docs"
description: "本文介绍如何使用 Azure 数据工厂创建 Azure-SSIS 集成运行时。"
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: 2e3e1fff385de87e69ea36d8ffec128f662f6e50
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-azure-data-factory-ui"></a>使用 Azure 数据工厂 UI 预配 Azure-SSIS 集成运行时
本教程提供使用 Azure 门户在 Azure 数据工厂中预配 Azure-SSIS 集成运行时 (IR) 的步骤。 然后，可以使用 SQL Server Data Tools 或 SQL Server Management Studio 将 SQL Server Integration Services (SSIS) 包部署到 Azure 上的此运行时。 有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教程中，将完成以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="prerequisites"></a>先决条件
- **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。 
- **Azure SQL 数据库服务器** 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 Azure 数据工厂在此数据库服务器上创建 SSIS 目录（SSISDB 数据库）。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 数据库而无需跨 Azure 区域。 
- 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
- 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。
- 确认 Azure SQL 数据库服务器没有 SSIS 目录（SSISDB 数据库）。 预配 Azure-SSIS IR 时不支持使用现有 SSIS 目录。

> [!NOTE]
> - 可以在下述区域创建第 2 版数据工厂：美国东部、美国东部 2、东南亚、西欧。 
> - 可以在下述区域创建 Azure-SSIS IR：美国东部、美国东部 2、美国中部、北欧、西欧、澳大利亚东部。 

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
2. 登录到 [Azure 门户](https://portal.azure.com/)。    
3. 在左侧菜单中选择“新建”，然后依次选择“数据 + 分析”、“数据工厂”。 
   
   ![在“新建”窗格中选择“数据工厂”](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. 在“新建数据工厂”页的“名称”下，输入 **MyAzureSsisDataFactory**。 
      
   ![“新建数据工厂”页](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 *全局唯一*。 如果收到错误，请更改数据工厂的名称（例如改为 **&lt;yourname&gt;MyAzureSsisDataFactory**），并重新尝试创建。 有关数据工厂项目的命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. 对于“订阅”，请选择要在其中创建数据工厂的 Azure 订阅。 
5. 对于**资源组**，请执行以下步骤之一：
     
   - 选择“使用现有”，并从列表中选择现有的资源组。 
   - 选择“新建”，并输入资源组的名称。   
         
   若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
6. 对于“版本”，请选择“V2 (预览版)”。
7. 对于“位置”，请选择数据工厂所在的位置。 此列表只显示支持创建数据工厂的位置。
8. 选择“固定到仪表板”。     
9. 选择“创建”。
10. 仪表板上会显示状态为“正在部署数据工厂”的以下磁贴： 

   ![“正在部署数据工厂”磁贴](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. 创建完成后，会显示“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. 选择“创作和监视”，在单独的选项卡中打开数据工厂用户界面 (UI)。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时

1. 在“入门”页中，选择“配置 SSIS 集成运行时”磁贴。 

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. 在“集成运行时安装”的“常规设置”页中完成以下步骤： 

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. 至于“名称”，请指定集成运行时的名称。

   b. 至于“位置”，请选择集成运行时的位置。 界面上仅显示支持的位置。

   c. 至于“节点大小”，请选择要为 SSIS 运行时配置的节点大小。

   d.单击“下一步”。 至于“节点数”，请指定群集中的节点数。
   
   e. 选择“**下一步**”。 
3. 在“SQL 设置”页上，完成以下步骤： 

   ![SQL 设置](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. 至于“订阅”，请指定包含 Azure 数据库服务器的 Azure 订阅。

   b. 至于“目录数据库服务器终结点”，请选择 Azure 数据库服务器。

   c. 至于“管理员用户名”，请输入管理员用户名。

   d.单击“下一步”。 至于“管理员密码”，请输入管理员的密码。

   e. 至于“目录数据库服务器层”，请选择 SSISDB 数据库的服务层。 默认值为“基本”。

   f. 选择“**下一步**”。 
4. 在“高级设置”页中，选择“每个节点的最大并行执行数”的值。   

   ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 此步骤是*可选的*。 如果要将集成运行时加入到某个虚拟网络（通过经典部署模型或 Azure 资源管理器创建），请选择“选择 Azure-SSIS 集成运行时要加入的 VNet，并允许 Azure 服务配置 VNet 权限/设置”复选框。 然后完成以下步骤： 

   ![使用虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a. 至于“订阅”，请指定包含虚拟网络的订阅。

   b. 至于“VNet 名称”，请选择虚拟网络的名称。

   c. 至于“子网名称”，请选择虚拟网络中子网的名称。 
6. 选择“完成”，开始创建 Azure-SSIS 集成运行时。 

   > [!IMPORTANT]
   > 完成此过程大约需要 20 分钟。
   >
   > 数据工厂服务将连接到 Azure SQL 数据库，以便准备 SSIS 目录（SSISDB 数据库）。 在指定的情况下，此脚本还为虚拟网络配置权限和设置。 它会将 Azure-SSIS 集成运行时的新实例加入虚拟网络。
   > 
   > 预配 Azure-SSIS IR 的实例时，还会安装 Azure Feature Pack for SSIS 和 Access Redistributable。 除了内置组件支持的数据源外，这些组件还提供与 Excel 和 Access 文件和各种 Azure 数据源的连接。 目前无法为 SSIS 安装第三方组件 （此限制包括 Microsoft 提供的第三方组件，如 Attunity 提供的 Oracle 和 Teradata 组件以及 SAP BI 组件）。

7. 在“连接”选项卡中，根据需要切换到“集成运行时”。 选择“刷新”可刷新状态。 

   ![创建状态，使用“刷新”按钮](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. 使用“操作”列中的链接可以停止/启动、编辑或删除集成运行时。 使用最后一个链接可以查看集成运行时的 JSON 代码。 仅当 IR 已停止时，才会启用编辑和删除按钮。 

   ![“操作”列中的链接](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>创建 Azure-SSIS 集成运行时

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，选择“连接”，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。 
   ![用于查看现有 IR 的选择](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. 选择“新建”以创建 Azure-SSIS IR。 

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. 在“集成运行时安装”窗口中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”，然后选择“下一步”。

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. 请参阅[预配 Azure-SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

 
## <a name="deploy-ssis-packages"></a>部署 SSIS 包
现在，请使用 SQL Server Data Tools 或 SQL Server Management Studio 将 SSIS 包部署到 Azure。 连接到托管 SSIS 目录（SSISDB 数据库）的 Azure 数据库服务器。 Azure 数据库服务器的名称采用 `<servername>.database.windows.net` 格式（适用于 Azure SQL 数据库）。 

请参阅 SSIS 文档中的以下文章： 

- [在 Azure 上部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 上的 SSIS 目录](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 预配 Azure-SSIS 集成运行时。

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
> [在云中复制数据](tutorial-copy-data-portal.md)
