---
title: 将 SSIS 包重新部署到 SQL 单一数据库
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure 数据库迁移服务和数据迁移助手将 SQL Server Integration Services 包和项目迁移或重新部署到 Azure SQL 数据库单一数据库。
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
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648523"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>通过 Azure 数据库迁移服务将 SSIS 包重新部署到 Azure SQL 数据库

如果使用 SQL Server Integration Services （SSIS）并想要将 SSIS 项目/包从 SQL Server 托管的源 SSISDB 迁移到 Azure SQL Database 托管的目标 SSISDB，则可以使用 Integration Services 部署向导重新部署它们。 可以从 SQL Server Management Studio (SSMS) 中启动向导。

如果使用的 SSIS 版本早于 2012 年版，在将 SSIS 项目/包重新部署到项目部署模型中时，首先需要使用 Integration Services 项目转换向导来转换它们，该向导也可从 SSMS 中启动。 有关详细信息，请参阅文章[将项目转换为项目部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务（DMS）目前不支持将源 SSISDB 迁移到 Azure SQL 数据库服务器，但你可以使用以下过程重新部署 SSIS 项目/包。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>必备条件

若要完成这些步骤，需满足以下条件：

* SSMS 17.2 版本或更高版本。
* 用于托管 SSISDB 的目标数据库服务器的实例。 如果还没有数据库，请通过导航到 SQL Server （仅限逻辑服务器）[表单](https://ms.portal.azure.com/#create/Microsoft.SQLServer)，使用 Azure 门户创建 Azure SQL 数据库服务器（不包含数据库）。
* 必须在 Azure 数据工厂（ADF）中预配 SSIS，其中包含由 Azure SQL 数据库服务器实例托管的目标 SSISDB Azure-SSIS Integration Runtime （IR）（如在[Azure 数据工厂中预配 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)一文中所述）。

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包

虽然源 SSISDB 评估尚未集成到数据库迁移助手（DMA）或 Azure 数据库迁移服务（DMS）中，但你的 SSIS 项目/包将在重新部署到 Azure SQL 数据库服务器上托管的目标 SSISDB 时进行评估/验证。

## <a name="migrate-ssis-projectspackages"></a>迁移 SSIS 项目/包

若要将 SSIS 项目/包迁移到 Azure SQL Database 服务器，请执行以下步骤。

1. 打开 SSMS，并选择“选项”以显示“连接到服务器”对话框。

2. 在 "**登录**" 选项卡上，指定连接到将托管目标 SSISDB 的 Azure SQL 数据库服务器所需的信息。

    ![SSIS 登录选项卡](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. 在“连接属性”选项卡的“连接到数据库”文本框中，选择或输入“SSISDB”，然后选择“连接”。

    ![SSIS 连接属性选项卡](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. 在“SSMS 对象资源管理器”中，展开“Integration Services 目录”节点，再展开“SSISDB”，如果尚不具有文件夹，则右键单击“SSISDB”，然后创建一个新文件夹。

5. 在“SSISDB”下，展开任意文件夹，右键单击“项目”，然后选择“部署项目”。

    ![已展开 SSIS SSISDB 节点](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. 在“Integration Services 部署向导”中的“简介”页上，查看信息，然后选择“下一步”。

    ![“部署向导简介”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. 在“选择源”页上，指定想要部署的现有 SSIS 项目。

    如果 SSMS 也连接到托管源 SSISDB 的 SQL Server，选择“Integration Services 目录”，然后在目录中输入服务器名称和项目路径以直接部署项目。

    或者，选择“项目部署文件”，然后指定到现有项目部署文件 (.ispac) 的路径以部署项目。

    ![“部署向导选择源”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. 选择“**下一页**”。
9. 在“选择目标”页上，指定项目目标。

    a. 在 "服务器名称" 文本框中，输入完全限定的 Azure SQL 数据库服务器名称（< server_name ">"。

    b. 提供身份验证信息，然后选择“连接”。

    ![“部署向导选择目标”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 选择 "**浏览**" 以在 SSISDB 中指定目标文件夹，然后选择 "**下一步**"。

    > [!NOTE]
    > 仅在已选择“连接”后“下一步”按钮才会启用。

10. 在“验证”页上，查看任何错误/警告，然后如有必要，请修改相应的包。

    ![“部署向导验证”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 选择“**下一页**”。

12. 在“查看”页上，查看部署设置。

    > [!NOTE]
    > 可以通过选择 "**上一**步" 或在左窗格中选择任一步骤链接来更改设置。

13. 选择“部署”启动部署进程。

14. 完成部署进程后，可查看“结果”页，其中显示每一次成功或失败的部署操作。
    a. 如果任何操作失败，请在“结果”列中选择“失败”以显示错误说明。
    b. （可选）选择“保存报表”以将结果保存到 XML 文件。

15. 选择“关闭”以退出 Integration Services 部署向导。

如果在没有失败的情况下成功部署项目，则可以选择该项目包含的任意包以在 Azure-SSIS IR 上运行。

## <a name="next-steps"></a>后续步骤

* 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。
