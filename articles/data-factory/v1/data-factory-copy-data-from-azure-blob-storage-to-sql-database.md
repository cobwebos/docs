---
title: "将数据从 Blob 存储复制到 SQL 数据库 - Azure | Microsoft Docs"
description: "本教程演示如何使用 Azure 数据工厂管道中的复制活动将数据从 Blob 存储复制到 SQL 数据库。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: c3a2d4b126d43017ec004bde82deb190584455c4
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>教程：使用数据工厂将数据从 Blob 存储复制到 SQL 数据库
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [复制向导](data-factory-copy-data-wizard-tutorial.md)
> * [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure 资源管理器模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[版本 2 中的复制活动教程文档](../quickstart-create-data-factory-dot-net.md)。 

此教程介绍如何创建包含管道的数据工厂，以将数据从 Blob 存储复制到 SQL 数据库。

复制活动在 Azure 数据工厂中执行数据移动。 该活动由全球可用的服务提供支持，能以安全、可靠、可缩放的方式在各种数据存储之间复制数据。 有关复制活动的详细信息，请参阅 [Data Movement Activities](data-factory-data-movement-activities.md) （数据移动活动）。  

> [!NOTE]
> 有关数据工厂服务的详细概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)一文。
>
>

## <a name="prerequisites-for-the-tutorial"></a>教程先决条件
开始本教程之前，必须具有以下先决条件：

* **Azure 订阅**。  如果没有订阅，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅[免费使用](http://azure.microsoft.com/pricing/free-trial/)一文。
* **Azure 存储帐户**。 在本教程中，将 Blob 存储用作**源**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
* **Azure SQL 数据库**。 在本教程中，将 Azure SQL 数据库用作**目标**数据存储。 如果没有可在本教程中使用的 Azure SQL 数据库，请参阅[如何创建和配置 Azure SQL 数据库](../../sql-database/sql-database-get-started.md)进行创建。
* **SQL Server 2012/2014 或 Visual Studio 2013**。 可使用 SQL Server Management Studio 或 Visual Studio 创建示例数据库和查看数据库中的结果数据。  

## <a name="collect-blob-storage-account-name-and-key"></a>收集 Blob 存储帐户名和密钥
要完成本教程，需使用 Azure 存储帐户的帐户名和帐户密钥。 记下 Azure 存储帐户的**帐户名**和**帐户密钥**。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 单击左侧菜单上的“更多服务”，并选择“存储帐户”。

    ![浏览存储帐户](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. 在“存储帐户”边栏选项卡中，选择要在本教程中使用的“Azure 存储帐户”。
4. 在“设置”下，选择“访问密钥”链接。
5. 在“存储帐户名”文本框旁，单击“复制”（图像）按钮，然后将其保存/粘贴到某个位置（例如：文本文件中）。
6. 重复上一步以复制或记下 **key1**。

    ![存储访问密钥](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. 单击“X”关闭所有边栏选项卡。

## <a name="collect-sql-server-database-user-names"></a>收集 SQL Server、数据库和用户的名称
要完成本教程，需提供 Azure SQL Server、数据库和用户的名称。 记下用于 Azure SQL 数据库的**服务器**、**数据库**和**用户**的名称。

1. 在 **Azure 门户**中，单击左侧的“更多服务”，并选择“SQL 数据库”。
2. 在“SQL 数据库”边栏选项卡中，选择要在本教程中使用的“数据库”。 记下**数据库名称**。  
3. 在“SQL 数据库”边栏选项卡的“设置”下，单击“属性”。
4. 记下**服务器名称**和**服务器管理员登录名**的值。
5. 单击“X”关闭所有边栏选项卡。

## <a name="allow-azure-services-to-access-sql-server"></a>允许 Azure 服务访问 SQL Server
确保 Azure SQL Server 的“允许访问 Azure 服务”设置处于“打开”状态，以便数据工厂服务可以访问 Azure SQL Server。 若要验证并启用此设置，请执行以下步骤：

1. 单击左侧的“更多服务”中心，并单击“SQL Server”。
2. 选择服务器，并单击“设置”下的“防火墙”。
3. 在“防火墙设置”边栏选项卡中，单击“允许访问 Azure 服务”旁边的“打开”。
4. 单击“X”关闭所有边栏选项卡。

## <a name="prepare-blob-storage-and-sql-database"></a>准备 Blob 存储和 SQL 数据库
现在，执行以下步骤来准备本教程所需的 Azure Blob 存储和 Azure SQL 数据库：  

1. 启动记事本。 复制以下文本，将文件命名为 **emp.txt**，然后将其保存到硬盘上的 **C:\ADFGetStarted** 文件夹。

    ```
    John, Doe
    Jane, Doe
    ```
2. 使用 [Azure 存储资源管理器](http://storageexplorer.com/)等工具创建 **adftutorial** 容器，将 **emp.txt** 文件上传到该容器。

    ![Azure 存储资源管理器下载。 将数据从 Blob 存储复制到 SQL 数据库](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. 使用以下 SQL 脚本在 Azure SQL 数据库中创建 **emp** 表。  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **如果计算机上已安装 SQL Server 2012/2014：**请遵循[使用 SQL Server Management Studio 管理 Azure SQL 数据库](../../sql-database/sql-database-manage-azure-ssms.md)一文中的说明连接到 Azure SQL Server，并运行 SQL 脚本。 

    如果不允许客户端访问 Azure SQL Server，则需要将 Azure SQL Server 的防火墙配置为允许从计算机（IP 地址）访问。 请参阅 [此文](../../sql-database/sql-database-configure-firewall-settings.md) 中的步骤，为 Azure SQL Server 配置防火墙。

## <a name="create-a-data-factory"></a>创建数据工厂
已完成此先决条件。 可使用以下方法之一来创建数据工厂。 单击顶部下拉列表中的其中一个选项或以下链接来执行此教程。     

* [复制向导](data-factory-copy-data-wizard-tutorial.md)
* [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Azure 资源管理器模板](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> 本教程中的数据管道将数据从源数据存储复制到目标数据存储。 该管道并不通过转换输入数据来生成输出数据。 有关如何使用 Azure 数据工厂来转换数据的教程，请参阅[教程：生成第一个使用 Hadoop 群集来转换数据的管道](data-factory-build-your-first-pipeline.md)。
> 
> 通过将一个活动的输出数据集设置为另一个活动的输入数据集，可链接两个活动（两个活动先后运行）。 有关详细信息，请参阅[数据工厂中的计划和执行情况](data-factory-scheduling-and-execution.md)。 
