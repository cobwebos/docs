---
title: 模板概述
description: 了解如何使用预定义的模板来快速开始使用 Azure 数据工厂。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: b5e4512030431b6d2e5d0f849e82a281c882b177
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628940"
---
# <a name="templates"></a>模板

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

模板是预定义的 Azure 数据工厂管道，可让你快速开始使用数据工厂。 如果你是数据工厂的新手并想要快速入门，则模板很有帮助。 这些模板减少了生成数据集成项目所需的开发时间，从而提高了开发人员的工作效率。

## <a name="create-data-factory-pipelines-from-templates"></a>从模板创建数据工厂管道

可通过以下两种方式，开始从模板创建数据工厂管道：

1.  在“概述”页上选择“从模板创建管道”打开模板库。 

    ![从“概述”页打开模板库](media/solution-templates-introduction/templates-intro-image1.png)

1.  在资源浏览器中的“创作”选项卡上选择 **+** ，然后选择“模板中的管道”打开模板库。 

    ![从“创作”页打开模板库](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>模板库

![模板库](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>现成的数据工厂模板

数据工厂使用 Azure 资源管理器模板来保存数据工厂管道模板。 在[官方 Azure 数据工厂 GitHub 存储库](https://github.com/Azure/Azure-DataFactory/tree/master/templates)中，可以看到所有资源管理器模板，以及现成数据工厂模板使用的清单文件。 Microsoft 提供的预定义模板包括但不限于以下各项：

-   复制模板：

    -   [从数据库进行批量复制](solution-template-bulk-copy-with-control-table.md)
    
    -   [按 LastModifiedDate 复制新文件](solution-template-copy-new-files-lastmodifieddate.md)

    -   [在基于文件的存储之间复制多个文件容器](solution-template-copy-files-multiple-containers.md)

    -   [移动文件](solution-template-move-files.md)

    -   [从数据库进行增量复制](solution-template-delta-copy-with-control-table.md)

    -   从 \<源\> 复制到 \<目标\>

        -   [从 Amazon S3 复制到 Azure Data Lake Store Gen 2](solution-template-migration-s3-azure.md)

        -   从 Google Big Query 复制到 Azure Data Lake Store Gen 2

        -   从 HDF 复制到 Azure Data Lake Store Gen 2

        -   从 Netezza 复制到 Azure Data Lake Store Gen 1

        -   从本地 SQL Server 复制到 Azure SQL 数据库

        -   从本地 SQL Server 复制到 Azure SQL 数据仓库

        -   从本地 Oracle 复制到 Azure SQL 数据仓库

-   SSIS 模板

    -   将 Azure-SSIS Integration Runtime 计划为执行 SSIS 包

-   转换模板

    -   [使用 Azure Databricks 的 ETL](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>我的模板

还可以通过在“管道”选项卡上选择“另存为模板”，将管道另存为模板。 

![将管道另存为模板](media/solution-templates-introduction/templates-intro-image4.png)

可以在模板库的“我的模板”部分查看另存为模板的管道。  也可以在资源浏览器的“模板”部分查看这些管道。 

![我的模板](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> 若要使用“我的模板”功能，必须启用 GIT 集成。 支持 Azure DevOps GIT 和 GitHub。
