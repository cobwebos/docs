<properties 
	pageTitle="使用 Hive 和 Microsoft Azure HDInsight (Hadoop) 分析传感器数据" 
	description="了解如何使用 Hive 和 Excel 通过 HDInsight (Hadoop) 分析和可视化传感器数据" 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	wacn.date="" 
	ms.author="larryfr"/>

# 通过将 Hive 与 HDInsight 配合使用分析传感器数据

了解如何通过将 Hive 查询控制台与 HDInsight (Hadoop) 配合使用来分析传感器数据，然后通过使用 Power View 在 Microsoft Excel 中可视化数据。

> [AZURE.NOTE] 本文档中的步骤仅适用于基于 Windows 的 HDInsight 群集。

在本示例中，你将使用 Hive 来处理由采暖、通风和空调 (HVAC) 系统生成的历史数据，以识别无法可靠地保持设定温度的系统。你将了解如何执行以下操作：

- 创建 HIVE 表，以查询存储在逗号分隔值 (CSV) 文件中的数据。
- 创建 HIVE 查询以分析数据。
- 使用 Microsoft Excel 连接到 HDInsight（使用开放的数据库连接 (ODBC)），以检索分析的数据。
- 使用 Power View 可视化数据。

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

## 先决条件

* HDInsight (Hadoop) 群集：有关创建群集的信息，请参阅[在 HDInsight 中设置 Hadoop 群集](hdinsight-provision-clusters)。

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel 用于通过 [Power View](https://support.office.com/zh-cn/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=zh-CN&rs=zh-CN&ad=CN) 实现数据可视化。

* [Microsoft Hive ODBC 驱动程序](http://www.microsoft.com/zh-cn/download/details.aspx?id=40886)

## 运行示例

1. 在 Azure 门户中，单击要在其上运行示例的群集，然后单击底部的**"查询控制台"**。或者，你也可以通过使用以下 URL 直接打开查询控制台：

	 	https://<clustername>.azurehdinsight.cn

	出现提示时，可以通过使用设置此群集时所用的管理员用户名和密码进行身份验证。

2. 在打开的网页中，单击**"入门库"**选项卡，然后在**"使用示例数据的解决方案"**类别下方，单击**"传感器数据分析"**示例。

3. 按照网页上提供的说明完成该示例。

<!---HONumber=56-->