---
title: 安装针对 Visual Studio 的 Azure Data Lake 工具 | Microsoft Docs
description: 了解如何安装针对 Visual Studio 的 Azure Data Lake 工具。
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: 37b01c404006bbba79b185049aea6c7f77ce3c68
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887347"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>安装用于 Visual Studio 的 Data Lake 工具

了解如何使用 Visual Studio 创建 Azure Data Lake Analytics 帐户、在 [U-SQL](data-lake-analytics-u-sql-get-started.md) 中定义作业，并将作业提交到 Data Lake Analytics 服务。 有关 Data Lake Analytics 的详细信息，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>先决条件

* **Visual Studio**：支持除 Express 以外的所有版本。
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **用于 .NET 的 Microsoft Azure SDK** 2.7.1 版或更高版本。  使用 [Web 平台安装程序](http://www.microsoft.com/web/downloads/platform.aspx)进行安装。
* **Data Lake Analytics** 帐户。 若要创建帐户，请参阅[通过 Azure 门户开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>安装针对 Visual Studio 2017 的 Azure Data Lake 工具

Visual Studio 2017 15.3 或更高版本支持针对 Visual Studio 的 Azure Data Lake 工具。 Visual Studio 安装程序中的**数据存储和处理**以及 **Azure 开发**工作负载包含此工具。 作为 VisualStudio 安装的一部分，启用这两个工作负载中的任何一个。  

按如下所示启用“数据存储和处理”工作负荷：![启用数据存储和处理工作负荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

按如下所示启用“Azure 开发”工作负荷：![启用 Azure 开发工作负荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>安装针对 Visual Studio 2013 和 2015 的 Azure Data Lake 工具

[从下载中心](http://aka.ms/adltoolsvs)下载并安装针对 Visual Studio 的 Azure Data Lake 工具。 安装后，请注意：
* “服务器资源管理器” > **Azure**节点包含“Data Lake Analytics”节点。 
* “工具”菜单中有一个“Data Lake”项。


## <a name="next-steps"></a>后续步骤
* 若要记录诊断信息，请参阅 [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要使用顶点执行视图，请参阅[使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
