---
title: 使用 Hive 和 Hadoop 分析传感器数据 - Azure HDInsight | Microsoft Docs
description: 了解如何通过将 Hive 查询控制台与 HDInsight (Hadoop) 配合使用来分析传感器数据，并通过 PowerView 在 Microsoft Excel 中可视化数据。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 04/14/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: eb3dc93d7cb741a8a3099abe13d00f40c9639705
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31398957"
---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>使用 HDInsight 中 Hadoop上的 Hive 查询控制台分析传感器数据

了解如何通过将 Hive 查询控制台与 HDInsight (Hadoop) 配合使用来分析传感器数据，并通过使用 Power View 在 Microsoft Excel 中可视化数据。

> [!IMPORTANT]
> 本文档中的步骤仅适用于基于 Windows 的 HDInsight 群集。 Windows 上仅可使用低于 HDInsight 3.4 版本的 HDInsight。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。


在本示例中，使用 Hive 处理历史数据，并确定与加热和空调系统有关的问题。 具体而言，通过执行以下任务，确定系统无法可靠地维持固定温度：

* 创建 HIVE 表，查询存储在逗号分隔值 (CSV) 文件中的数据。
* 创建 HIVE 查询以分析数据。
* 若要检索分析的数据，请使用 Microsoft Excel 连接到 HDInsight。
* 若要直观显示数据，请使用 Power View。

![解决方案体系结构关系图](./media/apache-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>先决条件

* HDInsight (Hadoop) 群集：有关创建群集的信息，请参阅[在 HDInsight 中创建 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)。
* Microsoft Excel 2013

  > [!NOTE]
  > Microsoft Excel 用于通过 [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US) 实现数据可视化。

* [Microsoft Hive ODBC 驱动程序](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>运行示例

1. 在 Web 浏览器中，导航到以下 URL： 

         https://<clustername>.azurehdinsight.net

    将 `<clustername>` 替换为 HDInsight 群集的名称。

    出现提示时，可以通过使用设置此群集时所用的管理员用户名和密码进行身份验证。

2. 在打开的网页中，单击“入门库”选项卡，并在“使用示例数据的解决方案”类别下方，单击“传感器数据分析”示例。

    ![入门库映像](./media/apache-hive-analyze-sensor-data/getting-started-gallery.png)

3. 按照网页上提供的说明完成该示例。
