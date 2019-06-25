---
title: 重新部署到 Azure SQL 数据库的 SQL Server Integration Services 包 |Microsoft Docs
description: 了解如何将 SQL Server Integration Services 包迁移到 Azure SQL 数据库。
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
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080640"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>重新部署到 Azure SQL 数据库的 SQL Server Integration Services 包

如果你使用 SQL Server Integration Services (SSIS)，并想要迁移从源到目标 Azure SQL 数据库承载 SSISDB 的 SQL Server 承载 SSISDB 的 SSIS 项目/包，可以重新使用 Integration Services 部署进行部署向导。 可以从 SQL Server Management Studio (SSMS) 中启动向导。

如果使用的 SSIS 版本早于 2012 年版，在将 SSIS 项目/包重新部署到项目部署模型中时，首先需要使用 Integration Services 项目转换向导来转换它们，该向导也可从 SSMS 中启动。 有关详细信息，请参阅文章[将项目转换为项目部署模型](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)。

> [!NOTE]
> Azure 数据库迁移服务 (DMS) 目前不支持迁移到 Azure SQL 数据库服务器的源 SSISDB，但可以重新部署 SSIS 项目/包使用以下过程。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 评估源 SSIS 项目/包。
> * 将 SSIS 项目/包迁移到 Azure。

## <a name="prerequisites"></a>必备组件

若要完成这些步骤，需满足以下条件：

* SSMS 17.2 版本或更高版本。
* 用于托管 SSISDB 的目标数据库服务器的实例。 如果你还没有一个，创建 Azure SQL 数据库服务器 （不含数据库） 通过导航到 SQL Server （仅限逻辑服务器） 使用 Azure 门户[窗体](https://ms.portal.azure.com/#create/Microsoft.SQLServer)。
* 必须预配 SSIS 在 Azure 数据工厂 (ADF) 与目标 Azure SQL 数据库服务器实例承载的 SSISDB 包含 Azure SSIS 集成运行时 (IR) (如本文所述[预配 Azure SSIS 集成Azure 数据工厂中的运行时](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure))。

## <a name="assess-source-ssis-projectspackages"></a>评估源 SSIS 项目/包

评估源 SSISDB 尚未集成到数据库迁移助手 (DMA) 或 Azure 数据库迁移服务 (DMS)，而将 SSIS 项目/包将会评估/验证为它们重新部署到目标承载的 SSISDBAzure SQL 数据库服务器。

## <a name="migrate-ssis-projectspackages"></a>迁移 SSIS 项目/包

若要将 SSIS 项目/包迁移到 Azure SQL 数据库服务器，请执行以下步骤。

1. 打开 SSMS，并选择“选项”以显示“连接到服务器”对话框   。

2. 上**登录名**选项卡上，指定连接到将承载 SSISDB 的目标 Azure SQL 数据库服务器所必需的信息。

    ![SSIS 登录选项卡](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. 在“连接属性”选项卡的“连接到数据库”文本框中，选择或输入“SSISDB”，然后选择“连接”     。

    ![SSIS 连接属性选项卡](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. 在“SSMS 对象资源管理器”中，展开“Integration Services 目录”节点，再展开“SSISDB”，如果尚不具有文件夹，则右键单击“SSISDB”，然后创建一个新文件夹    。

5. 在“SSISDB”下，展开任意文件夹，右键单击“项目”，然后选择“部署项目”    。

    ![已展开 SSIS SSISDB 节点](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. 在“Integration Services 部署向导”中的“简介”页上，查看信息，然后选择“下一步”   。

    ![“部署向导简介”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. 在“选择源”页上，指定想要部署的现有 SSIS 项目  。

    如果 SSMS 也连接到托管源 SSISDB 的 SQL Server，选择“Integration Services 目录”，然后在目录中输入服务器名称和项目路径以直接部署项目  。

    或者，选择“项目部署文件”，然后指定到现有项目部署文件 (.ispac) 的路径以部署项目  。

    ![“部署向导选择源”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. 选择“**下一步**”。
9. 在“选择目标”页上，指定项目目标  。

    a. 在服务器名称文本框中，输入完全限定的 Azure SQL 数据库服务器名称 (< 服务器名称 >。 database.windows.net)。

    b. 提供身份验证信息，然后选择“连接”  。

    ![“部署向导选择目标”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. 选择**浏览**在 SSISDB 中，指定的目标文件夹，然后选择**下一步**。

    > [!NOTE]
    > 仅在已选择“连接”后“下一步”按钮才会启用   。

10. 在“验证”页上，查看任何错误/警告，然后如有必要，请修改相应的包  。

    ![“部署向导验证”页](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. 选择“**下一步**”。

12. 在“查看”页上，查看部署设置  。

    > [!NOTE]
    > 可以通过选择更改你的设置**上一步**或通过在左窗格中选择的任何步骤链接。

13. 选择“部署”启动部署进程  。

14. 完成部署进程后，可查看“结果”页，其中显示每一次成功或失败的部署操作。
    a. 如果任何操作失败，请在“结果”列中选择“失败”以显示错误说明   。
    b. （可选）选择“保存报表”以将结果保存到 XML 文件  。

15. 选择“关闭”以退出 Integration Services 部署向导  。

如果在没有失败的情况下成功部署项目，则可以选择该项目包含的任意包以在 Azure-SSIS IR 上运行。

## <a name="next-steps"></a>后续步骤

* 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。
