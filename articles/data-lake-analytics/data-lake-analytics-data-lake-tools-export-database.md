---
title: "使用针对 Visual Studio 的 Azure Data Lake 工具导出 U-SQL 数据库 | Microsoft Docs"
description: "了解如何使用针对 Visual Studio 的 Azure Data Lake 工具导出 U-SQL 数据库并自动将其导入本地帐户。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 441606258f9541c9552925e7c0cbc9b3a9effb4d
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="export-a-u-sql-database"></a>导出 U-SQL 数据库

本文介绍如何使用[针对 Visual Studio 的 Azure Data Lake 工具](http://aka.ms/adltoolsvs)将 U-SQL 数据库导出为单个 U-SQL 脚本和下载的资源。 可以使用相同的过程将导出的数据库导入本地帐户。

客户通常会维护多个环境进行开发、测试和生产。 这些环境托管在开发人员本地计算机上的本地帐户中，以及 Azure 上的 Azure Data Lake Analytics 帐户中。 

在开发和测试环境中开发与优化 U-SQL 查询时，开发人员往往需要在生产数据库中重建其工作。 数据库导出向导可帮助加速此过程。 开发人员可以使用该向导将现有的数据库环境和示例数据克隆到其他 Data Lake Analytics 帐户。

## <a name="export-steps"></a>导出步骤

### <a name="step-1-export-the-database-in-server-explorer"></a>步骤 1：在服务器资源管理器中导出数据库

你有权访问的所有 Data Lake Analytics 帐户都列在服务器资源管理器中。 若要导出数据库，请执行以下操作：

1. 在服务器资源管理器中，展开包含所要导出的数据库的帐户。
2. 右键单击该数据库，选择“导出”。 
   
    ![服务器资源管理器 - 导出数据库](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     如果“导出”菜单选项不可用，需要[将工具更新到最新版本](http://aka.ms/adltoolsvs)。

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>步骤 2：配置想要导出的对象

如果只需要某个大型数据库的一小部分，可以在导出向导中配置所要导出的对象子集。 

导出操作是通过运行 U-SQL 作业完成的。 因此，从 Azure 帐户导出对象会产生一些费用。

![数据库导出向导 - 选择导出对象](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>步骤 3：检查对象列表和其他配置

在此步骤中，可以检查“导出对象列表”框中的选定对象。 如果存在任何错误，可以选择“上一步”返回，并正确配置所要导出的对象。

还可以配置导出目标的其他设置。 下表列出了配置说明：

|配置|说明|
|-------------|-----------|
|目标名称|此名称指示要将导出的数据库资源保存到的位置。 这些资源的示例包括程序集、附加文件和示例数据。 将在本地数据根文件夹下创建使用此名称的文件夹。|
|项目目录|此路径定义要将导出的 U-SQL 脚本保存到的位置。 所有数据库对象定义都保存在此位置。|
|仅限架构|如果选择此选项，则只会导出数据库定义和资源（例如程序集和附加文件）。|
|架构和数据|如果选择此选项，则会导出数据库定义、资源和数据。 将导出表的前 N 行。|
|自动导入到本地数据库|如果选择此选项，则完成导出后，会自动将导出的数据库导入到本地数据库。|

![数据库导出向导 - 导出对象列表和其他配置](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>步骤 4：检查导出结果

完成导出后，可在向导的日志窗口中查看导出结果。 以下示例演示如何查找所导出的 U-SQL 脚本和数据库资源，包括程序集、附加文件和示例数据。

![数据库导出向导 - 导出结果](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>将导出的数据库导入到本地帐户

导入已导出数据库的最便捷方法是在执行导出过程的步骤 3 期间选中“自动导入到本地数据库”复选框。 如果未选中此框，可在导出日志中找到导出的 U-SQL 脚本。 然后，在本地运行该 U-SQL 脚本，将数据库导入到本地帐户。

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>将导出的数据库导入到 Data Lake Analytics 帐户

若要将数据库导入到不同的 Data Lake Analytics 帐户，请执行以下操作：

1. 将导出的资源（包括程序集、附加文件和示例数据）上传到所要导入到的 Data Lake Analytics 帐户的默认 Azure Data Lake Store 帐户。 可以在本地数据根文件夹下找到已导出资源的文件夹。 可将该文件夹整个上传到默认 Data Lake Store 帐户的根目录。
2. 完成上传后，请将导出的 U-SQL 脚本提交到想要将数据库导入到的 Data Lake Analytics 帐户。

## <a name="known-limitations"></a>已知限制

目前，如果在步骤 3 中选择了“架构和数据”选项，该工具将运行 U-SQL 作业来导出表中存储的数据。 因此，数据导出过程可能较慢且产生费用。 

## <a name="next-steps"></a>后续步骤

* [了解 U-SQL 数据库](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [使用本地运行和 Azure Data Lake U-SQL SDK 来测试及调试 U-SQL 作业](data-lake-analytics-data-lake-tools-local-run.md)


