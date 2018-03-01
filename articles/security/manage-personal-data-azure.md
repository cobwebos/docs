---
title: "在 Microsoft Azure 中管理个人数据 | Microsoft Docs"
description: "有关如何在 Azure Active Directory 和 Azure SQL 数据库中更正、更新、删除和导出个人数据的指南"
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 51dca8955745b40a9126b142ea15e707fe58bc72
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="manage-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中管理个人数据

本文提供有关如何在 Azure Active Directory 和 Azure SQL 数据库中更正、更新、删除和导出个人数据的指南。

## <a name="scenario"></a>场景

一家总部位于都柏林的公司面向本地和国际客户群，为在爱尔兰和世界各地举办的高端婚礼提供一站式购物服务。 他们的办公室、客户、员工和供应商遍布世界各地，可以为各场地提供全面的服务。

该公司可以跟踪请柬答复，了解食物过敏和饮食偏好等信息，还提供许多其他服务。 在婚礼前的几个月内，婚礼来宾可以在同一个网页上报名参加各种活动，如骑马、冲浪、划船等，甚至可以进行互动。 该公司从员工、供应商、客户和婚礼来宾处收集个人信息。 由于业务具有国际性质，该公司必须遵守多个级别的法规。

## <a name="problem-statement"></a>问题陈述

- 数据管理员必须能够更正不准确的个人信息，更新不完整的或有变化的个人信息。

- 数据管理员必须能够根据数据使用者的请求删除个人信息。

- 数据管理员需要导出数据，并根据数据使用者的请求，以常见的结构化格式向其提供数据。

## <a name="company-goals"></a>公司目标

- 必须在 Azure Active Directory 和 Azure SQL 数据库中更正或更新不准确或不完整的客户、婚礼来宾、员工和供应商个人信息。

- 必须根据数据使用者的请求从 Azure Active Directory 和 Azure SQL 数据库中删除个人信息。

- 必须根据数据使用者的请求以常见的结构化格式导出个人数据。

## <a name="solutions"></a>解决方案

### <a name="azure-active-directory-rectifycorrect-inaccurate-or-incomplete-personal-data-and-erasedelete-personal-datauser-profiles"></a>Azure Active Directory：纠正/更正不准确或不完整的个人数据并擦除/删除个人数据/用户个人资料

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 是 Microsoft 提供的基于云的多租户目录和标识管理服务。
在 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) 环境中，可通过使用 [Azure 门户](https://portal.azure.com/)更正、更新或删除客户和员工的用户个人资料和用户工作信息中包含的个人数据，如用户姓名、工作头衔、地址或电话号码。

必须使用目录的全局管理员帐户登录。

#### <a name="how-do-i-correct-or-update-user-profile-and-work-information-in-azure-active-directory"></a>如何在 Azure Active Directory 中更正或更新用户个人资料和工作信息？

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有服务”，在文本框中输入“用户和组”，并选择 **Enter**。

    ![media/image1.png](media/manage-personal-data-azure/image001.png)

3. 在“用户和组”边栏选项卡中，选择“用户”。

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. 在“用户和组”边栏选项卡上，选择“用户”，从列表中选择一个用户，然后在所选用户对应的边栏选项卡上，选择“个人资料”，查看需要更正或更新的用户个人资料信息。

    ![media/image3.png](media/manage-personal-data-azure/image005.png)

5. 更正或更新信息，然后在命令栏中选择“保存”。

6.  在所选用户对应的边栏选项卡上，选择“工作信息”，查看需要更正或更新的用户工作信息。

    ![media/image4.png](media/manage-personal-data-azure/image007.png)

7. 更正或更新用户工作信息，然后在命令栏中选择“保存”。

#### <a name="how-do-i-delete-a-user-profile-in-azure-active-directory"></a>如何从 Azure Active Directory 中删除用户个人资料？

1. 使用目录全局管理员的帐户登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“所有服务”，在文本框中输入“用户和组”，并选择 **Enter**。

    ![](media/manage-personal-data-azure/image001.png)

3. 在“用户和组”边栏选项卡中，选择“用户”。

    ![media/image2.png](media/manage-personal-data-azure/image003.png)

4. 在“用户和组 - 用户”边栏选项卡中，从列表中选择一个用户。

    ![media/image3.png](media/manage-personal-data-azure/image007.png)

5. 在所选用户的边栏选项卡中，选择“概述”，并在命令栏中选择“删除”。

    ![](media/manage-personal-data-azure/image013.png)

### <a name="sql-database-rectifycorrect-inaccurate-or-incomplete-personal-data-erasedelete-personal-data-export-personal-data"></a>SQL 数据库：纠正/更正不准确或不完整的个人数据；擦除/删除个人数据；导出个人数据 

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50)是一个云数据库，可帮助开发人员生成和维护应用程序。

在 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50)中，可使用标准 SQL 查询更新个人数据，也可以删除该数据。 此外，可以使用多种方法（包括 Azure SQL Server 导入和导出向导），以多种格式（包括 BACPAC 文件）从 SQL 数据库中导出个人数据。

#### <a name="how-do-i-correct-update-or-erase-personal-data-in-sql-database"></a>如何在 SQL 数据库中更正、更新或擦除个人数据？

要了解如何在 SQL 数据库中更正或更新个人数据，请访问[更新 (Transact SQL)](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql)、[更新文本](https://docs.microsoft.com/sql/t-sql/queries/updatetext-transact-sql)、[使用公用表表达式更新](https://docs.microsoft.com/sql/t-sql/queries/with-common-table-expression-transact-sql)或[更新编写文本](https://docs.microsoft.com/sql/t-sql/queries/writetext-transact-sql)文档。

要了解如何删除 SQL 数据库中的个人数据，请访问[删除 (Transact SQL) ](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql)文档。

#### <a name="how-do-i-export-personal-data-to-a-bacpac-file-in-sql-database"></a>如何将 SQL 数据库中的个人数据导出到 BACPAC 文件？

BACPAC 文件包括 SQL 数据库数据和元数据，是带有 BACPAC 扩展名的 zip 文件。 可通过使用 [Azure 门户](https://portal.azure.com/)、SQLPackage 命令行实用程序、SQL Server Management Studio (SSMS) 或 PowerShell 来完成此操作。

要了解如何将数据导出到 BACPAC 文件，请访问[将 Azure SQL 数据库导出到 BACPAC 文件](https://docs.microsoft.com/azure/sql-database/sql-database-export)页面，其中包括上面列出的每个方法的详细说明。

如何使用 SQL Server 导入和导出向导从 SQL 数据库中导出个人数据？

此向导可帮助将数据从数据源复制到目标。 有关该向导的介绍（包括获取方式、权限信息，以及如何获取工具相关帮助），请访问[使用 SQL Server 导入和导出向导导入和导出数据](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)网页。

有关向导步骤的概述，请访问 [Steps in the SQL Server Import and Export Wizard](https://docs.microsoft.com/sql/integration-services/import-export-data/steps-in-the-sql-server-import-and-export-wizard)（SQL Server 导入和导出向导中的步骤）网页。

## <a name="next-steps"></a>后续步骤：

[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/?v=16.50) 

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

