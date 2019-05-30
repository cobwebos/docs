---
title: 使用 Hive ODBC 驱动程序将 Excel 连接到 Apache Hadoop - Azure HDInsight
description: 了解如何设置和使用针对 Excel 的 Microsoft Hive ODBC 驱动程序来从 Microsoft Excel 查询 HDInsight 群集中的数据。
keywords: hadoop excel,hive excel,hive odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: fcb9171d2285efab0f65e6ab424908bc42c0ea2f
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391891"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Azure HDInsight 中的 Apache Hadoop

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的大数据解决方案可将 Microsoft 商业智能 (BI) 组件与已在 Azure HDInsight 中部署的 Apache Hadoop 群集集成。 此集成的一个例子是，能够使用 Microsoft Hive 开放式数据库连接 (ODBC) 驱动程序将 Excel 连接到 HDInsight 中的 Hadoop 群集的 Hive 数据仓库。

还可以使用用于 Excel 的 Microsoft Power Query 外接程序从 Excel 连接与 HDInsight 群集和其他数据源（包括其他非 HDInsight Hadoop 群集）关联的数据。 有关安装和使用 Power Query 的信息，请参阅[Excel 连接到 HDInsight 利用 Power Query](../hdinsight-connect-excel-power-query.md)。

## <a name="prerequisites"></a>必备组件

在开始阅读本文前，必须具有以下项目：

* HDInsight Hadoop 群集。 要创建此群集，请参阅 [Azure HDInsight 入门](apache-hadoop-linux-tutorial-get-started.md)。
* 具有 Office 2010 Professional Plus 或更高版本，或 Excel 2010 或更高版本的工作站。

## <a name="install-microsoft-hive-odbc-driver"></a>安装 Microsoft Hive ODBC 驱动程序
下载并安装[Microsoft Hive ODBC 驱动程序](https://go.microsoft.com/fwlink/?LinkID=286698)版本匹配的应用程序将在其中使用 ODBC 驱动程序版本。  在本教程中，驱动程序用于 Office Excel。

## <a name="create-apache-hive-odbc-data-source"></a>创建 Apache Hive ODBC 数据源
下列步骤演示如何创建 Hive ODBC 数据源。

1. 在 Windows 中，导航到“开始”>“Windows 管理工具”>“ODBC 数据源(32 位)/(64 位)”。  此操作会打开“ODBC 数据源管理器”窗口  。

    ![ODBC 数据源管理器](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "使用ODBC 数据源管理器配置 DSN")

1. 在”用户 DSN”选项卡中，选择“添加”，打开“创建新数据源”窗口    。

1. 选择“Microsoft Hive ODBC 驱动程序”，然后选择“完成”，打开“Microsoft Hive ODBC 驱动程序 DSN 设置”窗口    。

1. 键入或选择以下值：

   | 属性 | 描述 |
   | --- | --- |
   |  数据源名称 |为数据源提供名称 |
   |  主机 |输入 `HDInsightClusterName.azurehdinsight.net` 。 例如： `myHDICluster.azurehdinsight.net` |
   |  Port |使用 **443**。 （此端口已从 563 更改为 443。） |
   |  数据库 |使用“默认”  。 |
   |  机制 |选择**Windows Azure HDInsight 服务** |
   |  用户名 |输入 HDInsight 群集 HTTP 用户的用户名。 默认的用户名为 **admin**。 |
   |  密码 |输入 HDInsight 群集用户的密码。 选中该复选框**保存密码 （加密）** 。|

1. 可选：选择“高级选项...”   

   | 参数 | 描述 |
   | --- | --- |
   |  使用本机查询 |选择此项时，ODBC 驱动程序不会尝试将 TSQL 转换为 HiveQL。 仅 100% 确定提交的是纯 HiveQL 语句时，才应使用此项。 连接 SQL Server 或 Azure SQL 数据库时，应将此项保留为未选中状态。 |
   |  每块提取的行数 |提取大量记录时，可能需要调整此参数以确保最佳性能。 |
   |  默认字符串列长度、二进制列长度、十进制列小数位数 |数据类型长度和精度可能会影响返回数据的方式。 由于精度损失和/或截断，可能会返回不正确的信息。 |

    ![高级选项](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "高级 DSN 配置选项")

1. 选择“测试”以测试数据源  。 如果正确配置了数据源，测试结果会显示“成功!”  。  

1. 选择“确定”以关闭“测试”窗口  。  

1. 选择“确定”以关闭“Microsoft Hive ODBC 驱动程序 DSN 设置”窗口   。  

1. 选择“确定”以关闭“ODBC 数据源管理器”窗口   。  

## <a name="import-data-into-excel-from-hdinsight"></a>将数据从 HDInsight 导入 Excel

下列步骤介绍如何使用在前面部分中创建的 ODBC 数据源将数据从 Hive 表导入到 Excel 工作簿。

1. 在 Excel 中打开新工作簿或现有工作簿。

2. 在“数据”选项卡中，导航到“获取数据” > “从其他源” > “从 ODBC”来启动“从 ODBC”窗口      。

    ![打开数据连接向导](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "打开数据连接向导")

3. 从下拉列表中，选择在上一部分中创建的数据源名称，然后选择**确定**。

4. 第一个用于**ODBC 驱动程序**对话框将打开。 选择**Windows**从左侧菜单中。 然后选择**Connect**以打开**导航器**窗口。

5. 在“导航器”中，导航到“HIVE” > “默认” > “hivesampletable”，然后选择“加载”      。 需要一段时间才能将数据导入到 Excel 中。

    ![HDInsight Hive ODBC 导航器](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "打开数据连接向导")

## <a name="next-steps"></a>后续步骤

本文已介绍如何使用 Microsoft Hive ODBC 驱动程序将来自 HDInsight 服务的数据检索到 Excel 中。 同样地，也可以将来自 HDInsight 服务的数据检索到 SQL 数据库中。 也可以将数据上传到 HDInsight 服务中。 若要了解更多信息，请参阅以下文章：

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Power Query 将 Excel 连接到 Apache Hadoop](apache-hadoop-connect-excel-power-query.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。