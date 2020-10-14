---
title: 在 Azure 数据工厂中创建 Azure-SSIS 集成运行时
description: 了解如何在 Azure 数据工厂中创建 Azure-SSIS 集成运行时，以便能够在 Azure 中部署和运行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: a7c3553eee1623ef0e41e01c76f638b64ee46740
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018182"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建 Azure-SSIS 集成运行时

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文提供了在 Azure 数据工厂 (ADF) 中预配 Azure-SQL Server Integration Services (SSIS) 集成运行时 (IR) 的步骤。 Azure-SSIS IR 支持：

- 运行部署在由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 中的包（项目部署模型）
- 运行部署在由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中的包（包部署模型）

预配 Azure-SSIS IR 后，可以使用熟悉的工具在 Azure 中部署和运行包。 这些工具已启用 Azure 并包括 SQL Server Data Tools (SSDT)、SQL Server Management Studio (SSMS) 和命令行实用工具，例如 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 和 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec)。

[预配 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) 教程介绍了如何通过 Azure 门户或数据工厂应用创建 Azure-SSIS IR。 该教程还介绍了如何选择性地使用 Azure SQL 数据库服务器或托管实例来承载 SSISDB。 本文对该教程的基础上有所延伸，介绍如何执行以下可选任务：

- 将 Azure SQL 数据库服务器与 IP 防火墙规则/虚拟网络服务终结点一起使用，或者将托管实例用于专用终结点来托管 SSISDB。 作为先决条件，需要配置虚拟网络权限和设置，才能让 Azure-SSIS IR 加入虚拟网络。

- 对数据工厂的托管标识使用 Azure Active Directory (Azure AD) 身份验证，以连接到 Azure SQL 数据库服务器或托管实例。 作为先决条件，需要将数据工厂的托管标识添加为可创建 SSISDB 实例的数据库用户。

- 将 Azure-SSIS IR 加入虚拟网络，或将自承载 IR 配置为代理，使 Azure-SSIS IR 能够访问本地数据。

本文将介绍如何使用 Azure 门户、Azure PowerShell 和 Azure 资源管理器模板来预配 Azure-SSIS IR。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 订阅**。 如果还没有订阅，可以创建一个 [免费试用](https://azure.microsoft.com/pricing/free-trial/) 帐户。

- Azure SQL 数据库服务器或 SQL 托管实例（可选）。 如果还没有数据库服务器或托管实例，请在启动之前在 Azure 门户中创建一个。 数据工厂进而会在此数据库服务器上创建一个 SSISDB 实例。 

  建议在集成运行时所在的同一 Azure 区域中创建数据库服务器或托管实例。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。

  请记住以下几点：

  - 系统可以代表你创建 SSISDB 实例作为单一数据库、创建此实例作为弹性池的一部分，或者在托管实例中创建。 可以在公用网络中访问或者通过加入虚拟网络来访问该实例。 有关在 SQL 数据库与 SQL 托管实例之间进行选择以托管 SSISDB 的指导，请参阅本文中的[比较 SQL 数据库与 SQL 托管实例](#comparison-of-sql-database-and-sql-managed-instance)部分。 
  
    如果将具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或带有专用终结点的 SQL 托管实例用于托管 SSISDB，或者需要访问本地数据而不配置自承载 IR，则需要将 Azure-SSIS IR 加入虚拟网络。 有关详细信息，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

  - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 将 Azure SQL 数据库服务器与 IP 防火墙规则/虚拟网络服务终结点或带有专用终结点的 SQL 托管实例一起使用来托管 SSISDB 时，此设置不适用。 有关详细信息，请参阅[保护 Azure SQL 数据库的安全](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)。

  - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。

  - 若要连接到数据库服务器，可以结合服务器管理员凭据使用 SQL 身份验证，或者结合数据工厂的托管标识使用 Azure AD 身份验证。 对于后者，需将数据工厂的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 有关详细信息，请参阅[为 Azure-SSIS IR 启用 Azure AD 身份验证](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。

  - 确认你的数据库服务器还没有 SSISDB 实例。 预配 Azure-SSIS IR 时不支持使用现有的 SSISDB 实例。

- **Azure 资源管理器虚拟网络（可选）** 。 如果下列条件中至少有一个属实，则必须配置 Azure 资源管理器虚拟网络：

  - 正在具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器上托管 SSISDB，或使用专用终结点托管实例。

  - 需要从 SSIS 包连接到本地据存储，该包运行在没有配置自承载 IR 的 Azure-SSIS IR 上。

- **Azure PowerShell（可选）** 。 若要运行 PowerShell 脚本来预配 Azure-SSIS IR，请按照[如何安装和配置 Azure PowerShell](/powershell/azure/install-az-ps) 中的说明进行操作。

### <a name="regional-support"></a>区域支持

有关提供数据工厂和 Azure-SSIS IR 的 Azure 区域列表，请参阅[数据工厂和 SSIS IR 在各区域的上市情况](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)。

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>比较 SQL 数据库和 SQL 托管实例

下表比较了 Azure SQL 数据库服务器和 SQL 托管实例与 Azure-SSIR IR 相关的某些功能：

| 功能 | SQL 数据库| SQL 托管实例 |
|---------|--------------|------------------|
| **计划** | SQL Server 代理不可用。<br/><br/>请参阅[在数据工厂管道中计划包执行](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)。| 可以使用托管实例代理。 |
| **身份验证** | 可以使用包含的数据库用户创建 SSISDB 实例。该用户是 **db_owner** 角色的成员，代表具有数据工厂托管标识的任意 Azure AD 组。<br/><br/>请参阅[在 Azure SQL 数据库服务器中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)。 | 可以使用代表数据工厂托管标识的包含的数据库用户创建 SSISDB 实例。 <br/><br/>请参阅[在 Azure SQL 托管实例中启用 Azure AD 身份验证以创建 SSISDB](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance)。 |
| **服务层** | 在 Azure SQL 数据库服务器上创建 Azure-SSIS IR 时，可以选择 SSISDB 的服务层级。 有多个服务层级。 | 在托管实例上创建 Azure-SSIS IR 时，无法选择 SSISDB 的服务层级。 托管实例上的所有数据库共享分配给该实例的相同资源。 |
| **虚拟网络** | 如果使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器，则 Azure-SSIS IR 可以加入 Azure 资源管理器虚拟网络。 | 如果将托管实例用于专用终结点，则 Azure-SSIS IR 可以加入 Azure 资源管理器虚拟网络。 在未为托管实例启用公共终结点的情况下，虚拟网络是必需的。<br/><br/>如果将 Azure-SSIS IR 加入与托管实例相同的虚拟网络，请确保 Azure-SSIS IR 与托管实例位于不同的子网中。 如果将 Azure-SSIS IR 加入与托管实例不同的虚拟网络，我们建议使用虚拟网络对等互连或网络间的连接。 请参阅 [将应用程序连接到 Azure SQL 数据库托管实例](../sql-database/sql-database-managed-instance-connect-app.md)。 |
| **分布式事务** | 通过弹性事务支持此功能。 不支持 Microsoft 分布式事务处理协调器 (MSDTC) 事务。 如果 SSIS 包使用 MSDTC 协调分布式事务，请考虑迁移到 Azure SQL 数据库弹性事务。 有关详细信息，请参阅[跨云数据库的分布式事务](../sql-database/sql-database-elastic-transactions-overview.md)。 | 不支持。 |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>使用 Azure 门户创建集成运行时

在本部分，你将使用 Azure 门户（具体而言，使用数据工厂用户界面 (UI) 或应用）创建 Azure-SSIS IR。

### <a name="create-a-data-factory"></a>创建数据工厂

若要通过 Azure 门户创建数据工厂，请按照[通过 UI 创建数据工厂](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)中的分步说明操作。 执行此操作时，请选择“固定到仪表板”，以便在创建后能够快速访问数据工厂。 

创建数据工厂后，在 Azure 门户中打开其概述页。 选择“创建和监视”磁贴，在单独的选项卡中打开该数据工厂的“开始”页。 然后可以继续创建 Azure-SSIS IR。   

### <a name="provision-an-azure-ssis-integration-runtime"></a>预配 Azure-SSIS 集成运行时

在“入门”页中，选择“配置 SSIS Integration Runtime”磁贴以打开“集成运行时设置”窗格。 

   ![“配置 Azure SSIS 集成运行时”磁贴](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   “集成运行时设置”窗格中有三个页面，在其中可以连续配置常规设置、部署设置和高级设置。

#### <a name="general-settings-page"></a>“常规设置”页

在“集成运行时设置”窗格的“常规设置”页中完成以下步骤 。

   ![常规设置](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. 对于“名称”，请输入集成运行时的名称。

   2. 对于“说明”，请输入集成运行时的说明。

   3. 对于“位置”，请选择集成运行时的位置。 界面上仅显示支持的位置。 建议选择承载 SSISDB 所需的数据库服务器的位置。

   4. 对于“节点大小”，请选择集成运行时群集中的节点大小。 仅显示支持的节点大小。 如果需要运行多个计算密集型或内存密集型包，请选择较大的节点大小（纵向扩展）。
   > [!NOTE]
   > 如果需要 [计算隔离](https://docs.microsoft.com/azure/azure-government/azure-secure-isolation-guidance#compute-isolation)，请选择 **Standard_E64i_v3** 的节点大小。 此节点大小代表了使用其整个物理主机的隔离虚拟机，并提供某些工作负载所需的隔离级别（如美国国防部的影响级别 5 (IL5) 工作负荷。
   
   5. 对于“节点数”，请选择集成运行时群集中的节点数。 仅显示支持的节点数。 如果需要并行运行多个包，请选择包含许多节点的大型群集（横向扩展）。

   6. 对于“版本/许可证”，请选择集成运行时的 SQL Server 版本：“标准”或“企业”。 如果需要在集成运行时上使用高级功能，请选择“Enterprise”。

   7. 对于“节省资金”，请选择适用于集成运行时的“Azure 混合权益”选项：“是”或“否”。 如果需要自带具有软件保障的 SQL Server 许可证，以便充分利用使用混合权益带来的成本节省，请选择“是”。

   8. 选择“**下一页**”。

#### <a name="deployment-settings-page"></a>“部署设置”页

在“集成运行时设置”窗格的“部署设置”页中，你可以选择创建 SSISDB 和/或 Azure-SSIS IR 包存储 。

##### <a name="creating-ssisdb"></a>创建 SSISDB

在“集成运行时设置”窗格的“部署设置”页中，如果要将包部署到 SSISDB（项目部署模型），请选中“创建由 Azure SQL 数据库服务器/托管实例托管的 SSIS 目录 (SSISDB) 来存储项目/包/环境/执行日志”复选框  。 或者，如果要将包部署到由 Azure SQL 托管实例（包部署模型）托管的文件系统、Azure 文件存储或 SQL Server 数据库 (MSDB) 中，则无需创建 SSISDB 或选中该复选框。

无论采用哪种部署模型，如果要使用由 Azure SQL 托管实例托管的 SQL Server 代理来协调/安排包执行，请选中该复选框，因为它已由 SSISDB 启用。 有关详细信息，请参阅[通过 Azure SQL 托管实例代理来安排 SSIS 包执行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-managed-instance-agent)。
   
如果选中该复选框，请完成以下步骤以提供你自己的数据库服务器来承载我们将代表你创建和管理的 SSISDB。

   ![SSISDB 的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. 对于“订阅”，请选择使用数据库服务器来托管 SSISDB 的 Azure 订阅。 

   1. 对于“位置”，请选择用于托管 SSISDB 的数据库服务器的位置。 建议选择集成运行时的位置。

   1. 对于“目录数据库服务器终结点”，请选择用于承载 SSISDB 的数据库服务器的终结点。 
   
      根据所选的数据库服务器，系统可以代表你创建 SSISDB 实例作为单一数据库、创建此实例作为弹性池的一部分，或者在托管实例中创建。 可以在公用网络中访问或者通过加入虚拟网络来访问该实例。 有关选择用来托管 SSISDB 的数据库服务器类型的指导，请参阅[比较 SQL 数据库与 SQL 托管实例](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)。   

      如果选择具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来承载 SSISDB，或者需要在未配置自承载 IR 的情况下访问本地数据，则需要将 Azure-SSIS IR 加入虚拟网络。 有关详细信息，请参阅[在虚拟网络中创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。

   1. 选中“结合 ADF 的托管标识使用 Azure AD 身份验证”复选框，选择数据库服务器用来承载 SSISDB 的身份验证方法。 选择使用数据工厂的托管标识进行 SQL 身份验证或 Azure AD 身份验证。

      如果选中该复选框，需将数据工厂的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 有关详细信息，请参阅[使用 Azure AD 身份验证创建 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)。
   
   1. 对于“管理员用户名”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证用户名。 

   1. 对于“管理员密码”，请输入用于承载 SSISDB 的数据库服务器的 SQL 身份验证密码。 

   1. 对于“目录数据库服务层级”，请选择用于承载 SSISDB 的数据库服务器的服务层级。 选择“基本”、“标准”或“高级”层级，或选择弹性池名称。

如果适用，请选择“测试连接”，如果成功，请选择”下一步” 。

##### <a name="creating-azure-ssis-ir-package-stores"></a>创建 Azure-SSIS IR 包存储

在“集成运行时设置”窗格的“部署设置”页中，如果要使用 Azure-SSIS IR 包存储来管理部署到 MSDB、文件系统或 Azure 文件存储中的包（包部署模型），请选中“创建包存储以管理部署到由 Azure SQL 托管实例托管的文件系统/Azure 文件/SQL Server 数据库 (MSDB) 中的包”复选框  。
   
借助 Azure-SSIS IR 包存储，你可以通过与[旧版 SSIS 包存储](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)类似的 SSMS 导入/导出/删除/运行包以及监视/停止正在运行的包。 有关详细信息，请参阅[使用 Azure-SSIS IR 包存储管理 SSIS 包](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store)。
   
如果选中此复选框，则可以通过选择“新建”将多个包存储添加到 Azure-SSIS IR。 相反，多个 Azure SSIS IR 可以共享一个包存储。

![MSDB/文件系统/Azure 文件存储的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

在“添加包存储”窗格上，完成以下步骤。
   
   1. 对于“包存储名称”，请输入包存储的名称。 

   1. 对于“包存储链接服务”，请选择现有的链接服务，该服务存储在其中部署包的文件系统/Azure 文件存储/Azure SQL 托管实例的访问信息，或通过选择“新建”创建新链接服务 。 在“新建链接服务”窗格中，完成以下步骤。
   
      > [!NOTE]
      > 可以使用 **Azure 文件存储** 或 **文件系统** 链接服务来访问 azure 文件。 如果使用 **Azure 文件存储** 链接服务，Azure-SSIS IR 包存储区仅支持 **基本** (**帐户密钥** ，也不支持 **SAS URI**) 身份验证方法。 若要在**Azure 文件存储**链接服务上使用**基本**身份验证，可以 `?feature.upgradeAzureFileStorage=false` 在浏览器中追加到 ADF 门户 URL。 或者，可以使用 **文件系统** 链接服务来访问 Azure 文件。 

      ![链接服务的部署设置](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. 对于“名称”，请输入链接服务的名称。 
         
      1. 对于“说明”，请输入链接服务的说明。 
         
      1. 对于“类型”，请选择“Azure 文件存储”、“Azure SQL 托管实例”或“文件系统”   。

      1. 可以忽略“通过集成运行时连接”，因为我们始终使用 Azure-SSIS IR 来提取包存储的访问信息。

      1. 如果选择“Azure 文件存储”，请完成以下步骤。 

         1. 对于“帐户选择方法”，请选择“从 Azure 订阅”或“手动输入”  。
         
         1. 如果选择“从 Azure 订阅”，请选择相关的“Azure 订阅”、“存储帐户名称”和“文件共享”   。
            
         1. 如果选择“手动输入”，对于“主机”，请输入 `\\<storage account name>.file.core.windows.net\<file share name>`，对于“用户名”，请输入 `Azure\<storage account name>`，对于“密码”，请输入 `<storage account key>`，或选择在其中作为机密存储的“Azure 密钥保管库”    。

      1. 如果选择“Azure SQL 托管实例”，请完成以下步骤。 

         1. 选择“连接字符串”，以进行手动输入，或选择在其中作为机密存储的“Azure 密钥保管库” 。
         
         1. 如果选择“连接字符串”，请完成以下步骤。 

            1. 对于“完全限定的域名”，请输入 `<server name>.<dns prefix>.database.windows.net` 或 `<server name>.public.<dns prefix>.database.windows.net,3342` 分别作为 Azure SQL 托管实例的专用或公共终结点。 如果输入专用终结点，则“测试连接”不适用，因为 ADF UI 无法访问它。

            1. 对于“数据库名称”，请输入 `msdb`。
               
            1. 对于“身份验证类型”，请选择“SQL 身份验证”、“托管标识”或“服务主体”   。

            1. 如果选择“SQL 身份验证”，请输入相关的“用户名”和“密码”，或选择在其中作为机密存储的“Azure 密钥保管库”   。

            1. 如果选择“托管标识”，请向 ADF 托管标识授予对 Azure SQL 托管实例的访问权限。

            1. 如果选择“服务主体”，请输入相关的“服务主体 ID”和“服务主体密钥”，或选择在其中作为机密存储的“Azure 密钥保管库”   。

      1. 如果选择“文件系统”，请输入在其中为“主机”部署了包的文件夹的 UNC 路径，以及相关的“用户名”和“密码”，或选择在其中作为机密存储的“Azure 密钥保管库”    。

      1. 如果适用，请选择“测试连接”，如果成功，请选择“创建” 。

   1. 添加的包存储将显示在“部署设置”页上。 若要删除它们，请选中其复选框，然后选择“删除”。

如果适用，请选择“测试连接”，如果成功，请选择”下一步” 。

#### <a name="advanced-settings-page"></a>“高级设置”页

在“集成运行时设置”窗格的“高级设置”页中，请完成以下步骤 。

   ![高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. 对于“每个节点的最大并行执行数”，请选择要在集成运行时群集中并发运行的最大包数（按节点）。 仅显示支持的包数。 如果需要使用多个核心来运行单个计算密集型或内存密集型的大型包，请选择较小的数字。 如果需要在单个核心中运行一个或多个小型包，请选择较大的数字。

   1. 选中“使用其他系统配置/组件安装自定义 Azure-SSIS 集成运行时”复选框，选择是否要在 Azure-SSIS IR 上添加标准/快速自定义设置。 有关详细信息，请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

      如果选中该复选框，请完成以下步骤。

      ![包含自定义安装的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. 对于“自定义安装容器 SAS URI”，请输入用于存储标准自定义安装脚本和关联文件的容器的 SAS URI。

      1. 对于“快速自定义安装”，请选择“新建”打开“添加快速自定义安装”面板，然后在“快速自定义安装类型”下拉菜单中选择任何类型，例如“运行 cmdkey 命令”、“添加环境变量”、“安装许可的组件”等。      

         如果选择“安装许可的组件”类型，则可以在“组件名称”下拉菜单中选择我们的 ISV 合作伙伴提供的任何集成组件；如果需要，请在“许可密钥/许可文件”框中输入从合作伙伴购买的产品许可密钥/上传产品许可文件。   
  
         添加的快速自定义安装将显示在“高级设置”页上。 若要删除它们，可以选中其复选框，然后选择“删除”。

   1. 选中“选择 Azure-SSIS 集成运行时要加入到的 VNet，允许 ADF 创建特定的网络资源，并提供自己的静态公共 IP 地址(可选)”复选框，选择是否要将集成运行时加入虚拟网络。 

      如果你使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或使用专用终结点托管 SSISDB 的托管实例，则选择此项; 如果你需要访问本地数据 (（即，你的 SSIS 包中的本地数据源或目标，则) ，而无需配置自承载 IR。 有关详细信息，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 

      如果选中该复选框，请完成以下步骤。

      ![使用虚拟网络的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. 对于“订阅”，请选择包含你的虚拟网络的 Azure 订阅。

      1. 对于“位置”，系统已选择集成运行时所在的位置。

      1. 对于“类型”，请选择虚拟网络的类型：“经典”或“Azure 资源管理器”。 我们建议选择 Azure 资源管理器虚拟网络，因为经典虚拟网络在不久后将被弃用。

      1. 对于“VNet 名称”，请选择虚拟网络的名称。 它应与包含虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例一起用于托管 SSISDB。 或者，它应该是连接到本地网络的同一个虚拟网络。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何虚拟网络。

      1. 对于“子网名称”，请选择虚拟网络的子网名称。 它应该包含用于承载 SSISDB 的虚拟网络服务终结点的 Azure SQL 数据库服务器所用的虚拟网络相同。 它应与用于托管实例的托管实例的子网不同，后者用于托管 SSISDB。 否则，它可以是为 Azure-SSIS IR 提供你自己的静态公共 IP 地址的任何子网。

      1. 选中“为 Azure-SSIS Integration Runtime 提供静态公共 IP 地址”复选框，以选择是否要为 Azure-SSIS IR 提供自己的静态公共 IP 地址，以便可以在数据源的防火墙中允许这些地址。

         如果选中该复选框，请完成以下步骤。

         1. 对于“第一个静态公共 IP 地址”，请选择符合 Azure-SSIS IR 的要求的第一个静态公共 IP 地址。 如果没有任何符合要求的 IP 地址，请单击“新建”链接以在 Azure 门户中创建静态公共 IP 地址，然后单击此处的刷新按钮，以便可以选择创建的地址。
      
         1. 对于“第二个静态公共 IP 地址”，请选择符合 Azure-SSIS IR 的要求的第二个静态公共 IP 地址。 如果没有任何符合要求的 IP 地址，请单击“新建”链接以在 Azure 门户中创建静态公共 IP 地址，然后单击此处的刷新按钮，以便可以选择创建的地址。

   1. 选中“将自承载集成运行时安装为 Azure-SSIS 集成运行时的代理”复选框，选择是否要将自承载 IR 配置为 Azure-SSIS IR 的代理。 有关详细信息，请参阅[将自承载 IR 设置为代理](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)。 

      如果选中该复选框，请完成以下步骤。

      ![使用自承载 IR 的高级设置](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. 对于“自承载集成运行时”，选择现有的自承载 IR 作为 Azure-SSIS IR 的代理。

      1. 对于“暂存存储链接服务”，请选择现有的 Azure Blob 存储链接服务，或创建新的Azure Blob 存储链接服务进行暂存。

      1. 对于“暂存路径”，请指定所选 Azure Blob 存储帐户中的某个 Blob 容器，或将其留空以使用默认容器进行暂存。

   1. 选择“VNet 验证” > “继续”。  

在“摘要”部分检查所有预配设置，将建议的文档链接添加为书签，然后选择“完成”开始创建集成运行时。 

   > [!NOTE]
   > 此过程应在 5 分钟内完成（不包括任何自定义安装时间）。 但是，Azure-SSIS IR 可能需要 20-30 分钟才能加入虚拟网络。
   >
   > 如果使用 SSISDB，数据工厂服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
   > 
   > 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel 文件、Access 文件和各种 Azure 数据源的连接。 有关内置/已预安装组件的详细信息，请参阅 [Azure-SSIS IR 上的内置/已预安装组件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime)。 有关可安装的其他组件的详细信息，请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

#### <a name="connections-pane"></a>“连接”窗格

在“管理”中心的“连接”窗格中，切换到“集成运行时”页，然后选择“刷新”   。 

   ![“连接”窗格](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   可以通过选择 Azure-SSIS IR 的名称来对其进行编辑/重新配置。 此外，还可以选择相应的按钮来监视/启动/停止/删除 Azure-SSIS IR，通过“执行 SSIS 包”活动自动生成 ADF 管道以在 Azure-SSIS IR 上运行，并查看 Azure-SSIS IR 的 JSON 代码/有效负载。  只能在 Azure-SSIS IR 停止时对其执行编辑/删除操作。

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>门户中的 Azure SSIS 集成运行时

1. 在 Azure 数据工厂 UI 中切换到“编辑”选项卡，选择“连接”。  然后切换到“集成运行时”选项卡以查看数据工厂中的现有集成运行时。

   ![查看现有 IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. 选择“新建”，创建新的 Azure-SSIS IR，并打开“集成运行时设置”窗格 。

   ![通过菜单创建集成运行时](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. 在“集成运行时设置”窗格中选择“直接迁移现有的 SSIS 包以在 Azure 中执行”磁贴，然后选择“下一步”  。

   ![指定集成运行时的类型](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. 请参阅[预配 Azure SSIS 集成运行时](#provision-an-azure-ssis-integration-runtime)部分，了解用于设置 Azure-SSIS IR 的剩余步骤。

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>使用 Azure PowerShell 创建集成运行时

在本部分，你将使用 Azure PowerShell 来创建 Azure-SSIS IR。

### <a name="create-variables"></a>创建变量

复制并粘贴以下脚本。 指定变量的值。 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>登录并选择订阅

添加以下脚本，以登录并选择 Azure 订阅。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>验证与数据库服务器的连接

添加以下脚本来验证 Azure SQL 数据库服务器或托管实例。

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>配置虚拟网络

添加以下脚本以自动配置加入 Azure-SSIS 集成运行时的虚拟网络权限和设置。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/management/overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。

如果资源组已存在，请不要将此代码复制到脚本中。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>创建数据工厂

运行以下命令创建数据工厂。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>创建集成运行时

运行以下命令，在 Azure 中创建运行 SSIS 包的 Azure-SSIS 集成运行时：

如果不使用 SSISDB，则可以省略 `CatalogServerEndpoint`、`CatalogPricingTier` 和 `CatalogAdminCredential` 参数。

如果不使用具有 IP 防火墙规则/虚拟网络服务终结点的 Azure SQL 数据库服务器或具有专用终结点的托管实例来托管 SSISDB，或者需要访问本地数据，则可以省略 `VNetId` 和 `Subnet` 参数或为它们传递空值。 如果将自承载 IR 配置为 Azure-SSIS IR 的代理以访问本地数据，则也可以忽略这些参数。 否则不能省略这些参数，且必须传递虚拟网络配置中的有效值。 有关详细信息，请参阅[将 Azure-SSIS IR 加入虚拟网络](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

如果使用托管实例来承载 SSISDB，则可以省略 `CatalogPricingTier` 参数，或传递该参数的空值。 否则不能省略此参数，且必须传递 Azure SQL 数据库支持的定价层列表中的有效值。 有关详细信息，请参阅 [SQL 数据库资源限制](../sql-database/sql-database-resource-limits.md)。

如果对数据工厂的托管标识使用 Azure AD 身份验证以连接到数据库服务器，则可以省略 `CatalogAdminCredential` 参数。 但必须将数据工厂的托管标识添加到有权访问数据库服务器的 Azure AD 组中。 有关详细信息，请参阅[为 Azure-SSIS IR 启用 Azure AD 身份验证](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)。 否则，不能忽略此参数，且必须传递由 SQL 身份验证的服务器管理员用户名和密码构成的有效对象。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>启动集成运行时

运行以下命令以启动 Azure-SSIS Integration Runtime。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> 此过程应在 5 分钟内完成（不包括任何自定义安装时间）。 但是，Azure-SSIS IR 可能需要 20-30 分钟才能加入虚拟网络。
>
> 如果使用 SSISDB，数据工厂服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
> 
> 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel 文件、Access 文件和各种 Azure 数据源的连接。 有关内置/已预安装组件的详细信息，请参阅 [Azure-SSIS IR 上的内置/已预安装组件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime)。 有关可安装的其他组件的详细信息，请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

### <a name="full-script"></a>完整脚本

下面是创建 Azure-SSIS 集成运行时的完整脚本。

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>使用 Azure 资源管理器模板创建集成运行时

在本部分，你将使用 Azure 资源管理器模板创建 Azure-SSIS 集成运行时。 下面是示例演练：

1. 使用以下 Azure 资源管理器模板创建一个 JSON 文件。 请将尖括号中的值（占位符）替换为自己的值。

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. 若要部署 Azure 资源管理器模板，请按以下示例所示运行 `New-AzResourceGroupDeployment` 命令。 在此示例中，`ADFTutorialResourceGroup` 是资源组的名称。 `ADFTutorialARM.json` 是包含数据工厂和 Azure-SSIS IR 的 JSON 定义的文件。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    此命令将创建数据工厂并在其中创建 Azure-SSIS IR，但不会启动 IR。

3. 若要启动 Azure-SSIS IR，请运行 `Start-AzDataFactoryV2IntegrationRuntime` 命令：

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> 此过程应在 5 分钟内完成（不包括任何自定义安装时间）。 但是，Azure-SSIS IR 可能需要 20-30 分钟才能加入虚拟网络。
>
> 如果使用 SSISDB，数据工厂服务将连接到数据库服务器以准备 SSISDB。 它还会配置虚拟网络的权限和设置（如果已指定），并将 Azure-SSIS IR 加入虚拟网络中。
> 
> 预配 Azure-SSIS IR 时，还会安装 Access Redistributable 和 Azure Feature Pack for SSIS。 除了内置组件已支持的数据源外，这些组件还提供与 Excel 文件、Access 文件和各种 Azure 数据源的连接。 有关内置/已预安装组件的详细信息，请参阅 [Azure-SSIS IR 上的内置/已预安装组件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime)。 有关可安装的其他组件的详细信息，请参阅 [Azure-SSIS IR 的自定义安装](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。

## <a name="deploy-ssis-packages"></a>部署 SSIS 包

如果使用 SSISDB，可将包部署到其中，并使用已启用 Azure 的 SSDT 或 SSMS 工具在 Azure-SSIS IR 上运行它们。 这些工具通过数据库服务器的服务器终结点来与该服务器建立连接： 

- 对于 Azure SQL 数据库服务器，服务器终结点格式为 `<server name>.database.windows.net`。
- 对于具有专用终结点的托管实例，服务器终结点格式为 `<server name>.<dns prefix>.database.windows.net`。
- 对于具有公共终结点的托管实例，服务器终结点格式为 `<server name>.public.<dns prefix>.database.windows.net,3342`。 

如果不使用 SSISDB，则可以将包部署到由 Azure SQL 托管实例托管的文件系统、Azure 文件存储或 MSDB 中，并使用 [dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017) 和 [AzureDTExec](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-azure-enabled-dtexec) 命令行实用工具在 Azure-SSIS IR 上运行它们。 

有关详细信息，请参阅[部署 SSIS 项目/包](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15)。

在这两种情况下，还可以使用数据工厂管道中的“执行 SSIS 包”活动在 Azure-SSIS IR 上运行已部署的包。 有关详细信息，请参阅[以第一类数据工厂活动的形式调用 SSIS 包执行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

## <a name="next-steps"></a>后续步骤

请参阅本文档中的其他 Azure-SSIS IR 主题：

- [Azure-SSIS 集成运行时](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 此文提供有关集成运行时（包括 Azure-SSIS IR）的一般信息。
- [监视 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)。 此文介绍如何检索并了解有关 Azure-SSIS IR 的信息。
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)。 此文介绍如何停止、启动或删除 Azure-SSIS IR。 此外，介绍如何通过添加更多节点来横向扩展 Azure-SSIS IR。
- [在 Azure 中部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 中的 SSISDB](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [在 Azure 中计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)