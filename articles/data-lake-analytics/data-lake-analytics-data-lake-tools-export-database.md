---
title: "如何使用针对 Visual Studio 的 Azure Data Lake 工具导出 U-SQL 数据库 | Microsoft Docs"
description: "了解如何使用针对 Visual Studio 的 Azure Data Lake 工具同时导出 U-SQL 数据库并将其导入本地帐户。"
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
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>如何导出 U-SQL 数据库

本文档介绍如何使用 [针对 Visual Studio 的 Azure Data Lake 工具](http://aka.ms/adltoolsvs)将 U-SQL 数据库导出为单个 U-SQL 脚本和下载的资源。 也支持使用相同的过程将导出的数据库导入本地帐户。

客户通常会维护多个环境进行开发、测试和生产。 这些环境托管在开发人员本地计算机上的本地帐户中，以及 Azure 上的 Azure Data Lake Analytics 帐户中。 在开发和测试环境中开发和优化 U-SQL 查询时，开发人员经常需要在生产数据库中重新创建所有内容。 “数据库导出向导”可帮助加速此过程。 开发人员可以使用该向导将现有的数据库环境和示例数据克隆到其他 Azure Data Lake Analytics 帐户。

## <a name="export-steps"></a>导出步骤

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>步骤 1：在服务器资源管理器中右键单击数据库，并单击“导出...”

你有权访问的所有 Azure Data Lake Analytics 帐户都列在服务器资源管理器中。 展开包含所要导出的数据库的帐户，然后右键单击该数据库并选择“导出...”。如果找不到上下文菜单，则需要[将工具更新到最新版本](http://aka.ms/adltoolsvs)。

![Data Lake Analytics 工具 - 导出数据库](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>步骤 2：配置想要导出的对象

有时数据库很大，而你只是需要其一小部分，在这种情况下，可以在导出向导中配置所要导出的对象子集。 请注意，导出操作是通过运行 U-SQL 作业完成的，因此，从 Azure 帐户导出对象会产生一些费用。

![Data Lake Analytics 工具 - 导出数据库向导](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>步骤 3：检查对象列表和详细配置

在此步骤中，可以仔细检查对话框顶部的选定对象。 如果存在错误，可以单击“上一步”返回，并重新配置所要导出的对象。

还可以执行有关导出目标的其他配置，下表列出了这些配置的说明：

|配置|说明|
|-------------|-----------|
|目标名称|此名称指示要将导出的数据库资源（例如程序集、附加文件和示例数据）保存到的位置。 将在本地数据根文件夹下创建使用此名称的文件夹。|
|项目目录|此路径定义要将导出的 U-SQL 脚本（包含所有数据库对象定义）保存到的位置。|
|仅限架构|如果选择此选项，则只会导出数据库定义和资源（例如程序集和附加文件）。|
|架构和数据|如果选择此选项，则会导出数据库定义、资源和数据。 将导出表的前 N 行。|
|自动导入到本地数据库|如果选中此配置，则完成导出后，会自动将导出的数据库导入到本地数据库。|

![Data Lake Analytics 工具 - 导出数据库向导配置](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>步骤 4：检查导出结果

完成所有这些设置和导出过程后，可以通过向导中的日志窗口查找导出的结果。 通过以下屏幕截图中红色矩形标示的日志，可以找到所导出的 U-SQL 脚本和数据库资源（包括程序集、附加文件和示例数据）的位置。

![Data Lake Analytics 工具 - 导出数据库向导已完成](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>如何将导出的数据库导入到本地帐户

最方便的导入方法是在执行导出过程的步骤 3 期间选中“自动导入到本地数据库”。 如果忘记了选中此选项，可以使用导出日志找到导出的 U-SQL 脚本，然后在本地运行该 U-SQL 脚本，将数据库导入到本地帐户。

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>如何将导出的数据库导入到 Azure Data Lake Analytics 帐户

若要将数据库导入到其他 Azure Data Lake Analytics 帐户，需要执行两个步骤：

1. 将导出的资源（包括程序集、附加文件和示例数据）上传到所要导入到的 Azure Data Lake Analytics 帐户的默认 Azure Data Lake Store 帐户。 可以在本地数据根文件夹下找到已导出资源的文件夹，并将该文件夹整个上传到默认存储帐户的根目录。
2. 完成上传后，请将导出的 U-SQL 脚本提交到想要将数据库导入到的 Azure Data Lake Analytics 帐户。

## <a name="known-limitation"></a>已知限制

目前，如果在向导中选择“架构和数据”，该工具将运行 U-SQL 作业来导出表中存储的数据。 正因如此，数据导出过程可能较慢，且会产生费用。 

## <a name="next-steps"></a>后续步骤

* [了解 U-SQL 数据库](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [如何使用本地运行和 Azure Data Lake U-SQL SDK 来测试和调试 U-SQL 作业](data-lake-analytics-data-lake-tools-local-run.md)


