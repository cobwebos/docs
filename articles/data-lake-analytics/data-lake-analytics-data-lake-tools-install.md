---
title: 安装针对 Visual Studio 的 Azure Data Lake 工具
description: 本文介绍如何安装针对 Visual Studio 的 Azure Data Lake 工具。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914085"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Data Lake 工具

了解如何使用可视化工作室创建 Azure 数据湖分析帐户。 您可以在[U-SQL](data-lake-analytics-u-sql-get-started.md)中定义作业，并将作业提交到数据湖分析服务。 有关数据湖分析的详细信息，请参阅[Azure 数据湖分析概述](data-lake-analytics-overview.md)。

## <a name="prerequisites"></a>先决条件

* Visual Studio：支持除 Express 以外的所有版本****。

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Microsoft Azure SDK for .NET 2.7.1 版或更高版本****。 使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)进行安装。
* **数据湖分析**帐户。 若要创建帐户，请参阅[通过 Azure 门户开始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>为可视化工作室 2017 或 2019 年可视化工作室安装 Azure 数据湖工具

Visual Studio 2017 15.3 或更高版本支持用于可视化工作室的 Azure 数据湖工具。 该工具是**数据存储和处理**以及**Azure 开发**工作负荷的一部分。 在 Visual Studio 安装过程中，启用这两个工作负载中的任何一个。

启用“数据存储和处理”**** 工作负载，如下所示：

![启用数据存储和处理工作负载](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

启用“Azure 开发”**** 工作负载，如下所示：

![选择 Azure 开发工作负荷](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>安装针对 Visual Studio 2013 和 2015 的 Azure Data Lake 工具

下载并安装[微软 Azure 数据湖和可视化工作室的流分析工具](https://aka.ms/adltoolsvs)。 安装后，Visual Studio 有以下更改：

* **服务器资源管理器** > **Azure**节点包含**数据湖分析**节点。
* “工具”菜单中有一个“Data Lake”项********。

## <a name="next-steps"></a>后续步骤

* 要记录诊断信息，请参阅[访问 Azure 数据湖分析的诊断日志](data-lake-analytics-diagnostic-logs.md)。
* 要查看更复杂的查询，请参阅[使用 Azure 数据湖分析分析分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 要使用顶点执行视图，请参阅[在可视化工作室使用数据湖工具中的顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
