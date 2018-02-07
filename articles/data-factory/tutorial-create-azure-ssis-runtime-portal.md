---
title: "使用门户预配 Azure SSIS 集成运行时 | Microsoft Docs"
description: "本文介绍如何使用 Azure 门户创建 Azure SSIS 集成运行时。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: hero-article
ms.date: 01/09/2018
ms.author: spelluru
ms.openlocfilehash: 281fe65393086ec6a04dcba5aae868f4fec097ad
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="provision-an-azure-ssis-integration-runtime-by-using-the-data-factory-ui"></a>使用数据工厂 UI 预配 Azure SSIS 集成运行时
本教程提供使用 Azure 门户在 Azure 数据工厂中预配 Azure-SSIS 集成运行时 (IR) 的步骤。 然后，可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 将 SQL Server Integration Services (SSIS) 包部署到 Azure 上的此运行时。 有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。

在本教程中，将执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建并启动 Azure-SSIS 集成运行时

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。


## <a name="prerequisites"></a>先决条件
- **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。 
- **Azure SQL 数据库服务器** 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 Azure 数据工厂在此数据库服务器上创建 SSIS 目录数据库 (SSISDB)。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。 
    - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
    - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。
    - 确认 Azure SQL 数据库服务器没有 SSIS 目录（SSIDB 数据库）。 预配 Azure-SSIS IR 时不支持使用现有 SSIS 目录。
 
## <a name="create-a-data-factory"></a>创建数据工厂

1. 登录到 [Azure 门户](https://portal.azure.com/)。    
2. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. 在“新建数据工厂”页中，输入 **MyAzureSsisDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameMyAzureSsisDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
      若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2 (预览)”作为**版本**。
5. 选择数据工厂的**位置**。 列表中只会显示支持创建数据工厂的位置。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 创建完成后，会显示图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. 单击“创作和监视”，在单独的选项卡中启动数据工厂用户界面 (UI)。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure SSIS 集成运行时

1. 在入门页，单击“配置 SSIS 集成运行时”磁贴。 

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. 在“集成运行时安装”的“常规设置”页中执行以下步骤： 

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. 指定集成运行时的**名称**。
    2. 选择集成运行时的**位置**。 界面上仅显示支持的位置。
    3. 选择要为 SSIS 运行时配置的**节点大小**。
    4. 指定群集中的**节点数**。
    5. 单击“资源组名称” 的 Azure 数据工厂。 
1. 在“SQL 设置”中执行以下步骤： 

    ![SQL 设置](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. 指定包含 Azure SQL 服务器的 Azure**订阅**。 
    2. 选择**目录数据库服务器终结点**的 Azure SQL Server。
    3. 输入**管理员**用户名。
    4. 输入管理员的**密码**。  
    5. 选择 SSISDB 数据库的**服务层**。 默认值为“基本”。
    6. 单击“资源组名称” 的 Azure 数据工厂。 
1.  在“高级设置”页中，选择“每个节点的最大并行执行数”的值。   

    ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. 此步骤是**可选的**。 如果要将集成运行时加入到某个经典虚拟网络 (VNet)，请选择“选择 Azure-SSIS 集成运行时要加入的 VNet，并允许 Azure 服务配置 VNet 权限/设置”选项，然后执行以下步骤： 

    ![包含 VNet 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. 指定包含经典 VNet 的**订阅**。 
    2. 选择“VNet”。 <br/>
    4. 选择“子网”。<br/> 
1. 单击“完成”开始创建 Azure-SSIS 集成运行时。 

    > [!IMPORTANT]
    > - 完成此过程大约需要 20 分钟
    > - 数据工厂服务将连接到 Azure SQL 数据库来准备 SSIS 目录数据库 (SSISDB)。 该脚本还配置 VNet 的权限和设置（如果已指定），并将 Azure SSIS 集成运行时的新实例加入 VNet 中。
    > - 预配 SQL 数据库实例以托管 SSISDB 时，还会安装 Azure Feature Pack for SSIS 和 Access Redistributable。 除了内置组件支持的数据源外，这些组件还提供与 Excel 和 Access 文件和各种 Azure 数据源的连接。 目前不能安装用于 SSIS 的第三方组件（包括 Microsoft 提供的第三方组件，如 Attunity 提供的 Oracle 和 Teradata 组件以及 SAP BI 组件）。

7. 在“连接”窗口中，根据需要切换到“集成运行时”。 单击“刷新”以刷新状态。 

    ![创建状态](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. 使用“操作”列下面的链接可以监视、停止/启动、编辑或删除集成运行时。 使用最后一个链接可以查看集成运行时的 JSON 代码。 仅当 IR 已停止时，才会启用编辑和删除按钮。 

    ![Azure SSIS IR - 操作](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. 单击“操作”下面的“监视”链接。  

    ![Azure SSIS IR - 详细信息](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. 如果发生了与 Azure SSIS IR 关联的**错误**，此页面上会显示错误数，以及用于查看错误详细信息的链接。 例如，如果数据库服务器中已包含 SSIS 目录，则会出现一条错误，指出 SSISDB 数据库已存在。  
11. 单击顶部的“集成运行时”导航回到前一页，以查看与数据工厂关联的所有集成运行时。  

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>门户中的 Azure SSIS 集成运行时

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，单击“连接”，然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。 
    ![查看现有 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. 单击“新建”创建新的 Azure-SSIS IR。 

    ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. 若要创建 Azure-SSIS 集成运行时，请单击“新建”，如图所示。 
3. 在“集成运行时安装”窗口中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”，然后单击“下一步”。

    ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. 请参阅[预配 Azure SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。 

 
## <a name="deploy-ssis-packages"></a>部署 SSIS 包
现在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 来将 SSIS 包部署到 Azure。 连接到承载着 SSIS 目录 (SSISDB) 的 Azure SQL 服务器。 Azure SQL 服务器的名称采用以下格式：`<servername>.database.windows.net`（适用于 Azure SQL 数据库）。 

参阅 SSIS 文档中的以下文章： 

- [在 Azure 上部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 上的 SSIS 目录](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>后续步骤
本教程介绍了如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建并启动 Azure-SSIS 集成运行时

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[复制云中的数据](tutorial-copy-data-portal.md)
