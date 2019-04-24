---
title: 将 SQL Server Integration Services 包迁移到 Azure | Microsoft Docs
description: 了解如何将 SQL Server Integration Services 包迁移到 Azure。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532458"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>将 SQL Server Integration Services 包迁移到 Azure
如果使用 SQL Server Integration Services (SSIS) 并想将 SSIS 项目/包从 SQL Sever 托管的源 SSISDB 迁移到 Azure SQL 数据库服务器或 Azure SQL 数据库托管实例托管的目标 SSISDB，可以使用 Integration Services 部署向导来部署它们。 可以从 SQL Server Management Studio (SSMS) 中启动向导。

如果使用的 SSIS 版本早于 2012 年版，在将 SSIS 项目/包重新部署到项目部署模型中时，首先需要使用 Integration Services 项目转换向导来转换它们，该向导也可从 SSMS 中启动。 有关详细信息，请参阅文章[将项目转换为项目部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务 (DMS) 目前不支持源 SSISDB 的迁移，但可以使用以下进程部署 SSIS 项目/包。 

在本文中，学习如何：
> [!div class="checklist"]
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>必备组件
若要完成这些步骤，需满足以下条件：

- SSMS 17.2 版本或更高版本。
- 用于托管 SSISDB 的目标数据库服务器的实例。
 
  如果还没有实例：
    - 对于 Azure SQL 数据库，可以通过导航到 SQL Server（仅逻辑服务器）[表单](https://ms.portal.azure.com/#create/Microsoft.SQLServer)，使用 Azure 门户创建 Azure SQL 数据库服务器（不含数据库）。
    - 对于 Azure SQL 数据库托管实例，请遵循[创建 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的详细信息。

- 必须在包含 Azure-SSIS Integration Runtime (IR) 的 Azure 数据工厂 (ADF) 中预配 SSIS，并且目标 SSISDB 由 Azure SQL 数据库服务器实例（如文章[在 Azure 数据工厂中预配 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) 中所述）或 Azure SQL 数据库托管实例（如文章[在 Azure 数据工厂中创建 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime) 中所述）托管。 

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包
虽然尚未将源 SSISDB 的评估集成到数据库迁移助手 (DMA) 或 Azure 数据库迁移服务 (DMS) 中，但在将 SSIS 项目/包重新部署到 Azure SQL 数据库服务器或 Azure SQL 数据库托管实例上托管的目标 SSISDB 时，将对其进行评估/验证。

## <a name="migrate-ssis-projectspackages"></a>迁移 SSIS 项目/包
若要将 SSIS 项目/包迁移到 Azure SQL 数据库服务器或 Azure SQL 数据库托管实例中，请执行以下步骤。

1.  打开 SSMS，并选择“选项”以显示“连接到服务器”对话框。

2.  在“登录”选项卡上，指定连接到托管目标 SSISDB 的 Azure SQL 数据库服务器或 Azure SQL 数据库托管实例所需的信息。

    ![SSIS 登录选项卡](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  在“连接属性”选项卡的“连接到数据库”文本框中，选择或输入“SSISDB”，然后选择“连接”。

    ![SSIS 连接属性选项卡](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  在“SSMS 对象资源管理器”中，展开“Integration Services 目录”节点，再展开“SSISDB”，如果尚不具有文件夹，则右键单击“SSISDB”，然后创建一个新文件夹。

5.  在“SSISDB”下，展开任意文件夹，右键单击“项目”，然后选择“部署项目”。

    ![已展开 SSIS SSISDB 节点](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  在“Integration Services 部署向导”中的“简介”页上，查看信息，然后选择“下一步”。

    ![“部署向导简介”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  在“选择源”页上，指定想要部署的现有 SSIS 项目。

    如果 SSMS 也连接到托管源 SSISDB 的 SQL Server，选择“Integration Services 目录”，然后在目录中输入服务器名称和项目路径以直接部署项目。

    或者，选择“项目部署文件”，然后指定到现有项目部署文件 (.ispac) 的路径以部署项目。

    ![“部署向导选择源”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  选择“**下一步**”。
9.  在“选择目标”页上，指定项目目标。

       a. 在“服务器名称”文本框中，输入完全限定的 Azure SQL 数据库服务器名称 (<server_name>.database.windows.net) 或 Azure SQL 数据库托管实例名称 (<server_name.dns_prefix>.database.windows.net)。
 
       b. 提供身份验证信息，然后选择“连接”。
    
       ![“部署向导选择目标”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 选择“浏览”以在 SSISDB 中指定目标文件夹，然后选择“下一步”。

    > [!NOTE]
    > 仅在已选择“连接”后“下一步”按钮才会启用。 

10. 在“验证”页上，查看任何错误/警告，然后如有必要，请修改相应的包。

    ![“部署向导验证”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 选择“**下一步**”。

12. 在“查看”页上，查看部署设置。

    > [!NOTE]
    > 可以通过选择“上一步”或选择左侧窗格中的任意步骤链接来更改设置。

13. 选择“部署”启动部署进程。

14. 完成部署进程后，可查看“结果”页，其中显示每一次成功或失败的部署操作。
    a. 如果任何操作失败，请在“结果”列中选择“失败”以显示错误说明。
    b. （可选）选择“保存报表”以将结果保存到 XML 文件。

15. 选择“关闭”以退出 Integration Services 部署向导。

如果在没有失败的情况下成功部署项目，则可以选择该项目包含的任意包以在 Azure-SSIS IR 上运行。

## <a name="next-steps"></a>后续步骤
- 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。