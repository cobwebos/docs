---
title: 使用 Power Query 将 Excel 连接到 Apache Hadoop - Azure HDInsight
description: 了解如何利用商业智能组件和使用 Power Query for Excel 访问 HDInsight 上的 Hadoop 中存储的数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 8664efd40bb5392f56803515f09cccc800fdf21c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397107"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>使用 Power Query 将 Excel 连接到 Apache Hadoop

Microsoft 大数据解决方案的一个关键功能是，将 Microsoft 商业智能 (BI) 组件与 Azure HDInsight 中的 Apache Hadoop 群集相集成。 一个主要示例是能够使用 Microsoft Power Query for Excel 外接程序将 Excel 连接到包含与 Hadoop 群集关联的数据的 Azure 存储帐户。 本文逐步说明如何设置和使用 Power Query 来查询与 HDInsight 管理的 Hadoop 群集关联的数据。

## <a name="prerequisites"></a>先决条件

* HDInsight 中的 Apache Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./apache-hadoop-linux-tutorial-get-started.md)。
* 运行 Windows 10、Windows 7、Windows Server 2008 R2 或更高版本操作系统的工作站。
* 适用于企业、Office 2016、Office 2013 Professional Plus、Excel 2013 独立版或 Office 2010 Professional Plus Microsoft 365 应用。

## <a name="install-microsoft-power-query"></a>安装 Microsoft Power Query

Power Query 可以导入已输出的数据，或者导入已由在 HDInsight 群集上运行的 Hadoop 作业生成的数据。

在 Excel 2016 中，Power Query 已集成到“获取并转换”部分下的数据功能区。 对于较旧的 Excel 版本，请从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?LinkID=286689)下载 Microsoft Power Query for Excel 并安装它。

## <a name="import-hdinsight-data-into-excel"></a>将 HDInsight 数据导入 Excel

使用用于 Excel 的 Power Query 外接程序，可以轻松地将 HDInsight 群集中的数据导入到 Excel 中，并可以在 Excel 中使用 PowerPivot 和 Power Map 之类的 BI 工具对数据进行检查、分析和显示。

1. 启动 Excel。

1. 创建一个新的空白工作簿。

1. 基于 Excel 版本，执行以下步骤：

   * Excel 2016

     * 选择 >“数据”   > “获取数据”   > “从 Azure”   > “从 Azure HDInsight(HDFS)”  。

       ![HDI.PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * 选择“Power Query“   > “从 Azure”   > “从 Microsoft Azure HDInsight”  。

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **注意：** 如果未看到“Power Query”菜单，请转到“文件” > “选项” > “加载项”，从页面底部的下拉“管理”框中选择“COM 加载项”       。  。

       **注意：** Power Query 还允许通过选择“来自其他源”从 HDFS 中导入数据  。

1. 在“Azure HDInsight(HDFS)”  对话框的“帐户名称或 URL”  文本框中，输入与群集关联的 Azure Blob 存储帐户的名称。 然后选择“确定”。  此帐户可以是默认存储帐户或链接的存储帐户。  格式为 `https://StorageAccountName.blob.core.windows.net/`。

1. 对于“帐户密钥”，请输入 Blob 存储帐户的密钥，然后选择“连接”   。 （只需要在首次访问此存储时输入帐户信息。）

1. 在“查询编辑器”左侧的“导航器”窗格中，双击与群集关联的 Blob 存储容器名称  。 默认情况下，该容器名称与群集名称相同。

1. 在“名称”列中找到“HiveSampleData.txt”（文件夹路径是“../hive/warehouse/hivesampletable/”），然后选择 HiveSampleData.txt 左侧的“二进制”     。 HiveSampleData.txt 随所有群集提供。 （可选）可使用自己的文件。

    ![HDI Excel Power Query 导入数据](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. 可根据需要重命名列名称。 准备就绪后，选择“关闭并加载”。   数据已加载到工作簿：

    ![HDI Excel Power Query 导入的表](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>后续步骤

在本文中，已了解如何使用 Power Query 将数据从 HDInsight 检索到 Excel 中。 同样地，也可以将来自 HDInsight 的数据检索到 Azure SQL 数据库中。 也可以将数据上载到 HDInsight。 要了解更多信息，请参阅下列文章：

* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](apache-hadoop-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 HDInsight](apache-hadoop-connect-excel-hive-odbc-driver.md)。
* [使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询](apache-hadoop-visual-studio-tools-get-started.md)。
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](../hdinsight-for-vscode.md)。
* [将数据上传到 HDInsight](./../hdinsight-upload-data.md)。
