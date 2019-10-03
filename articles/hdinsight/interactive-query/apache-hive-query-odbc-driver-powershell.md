---
title: 使用 ODBC 驱动程序和 PowerShell 查询 Apache Hive - Azure HDInsight
description: 使用 Microsoft Hive ODBC 驱动程序和 PowerShell 查询 Azure HDInsight 上的 Apache Hive 群集。
keywords: hive, hive odbc, powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: 04771ddc633c210ce8c7b3c42a9e46cb2f1ed349
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122175"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>教程：使用 ODBC 和 PowerShell 查询 Apache Hive

使用 Microsoft ODBC 驱动程序可以灵活地与不同类型的数据源（包括 Apache Hive）进行交互。 可以采用 PowerShell 等脚本语言编写代码，以使用 ODBC 驱动程序打开与 Hive 群集的连接，传递所选的查询，并显示相应的结果。

在本教程中，你将执行以下任务：

> [!div class="checklist"]
> * 下载并安装 Microsoft Hive ODBC 驱动程序
> * 创建链接到群集的 Apache Hive ODBC 数据源
> * 使用 PowerShell 从群集查询示例信息

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

开始学习本教程之前，必须做好以下准备：

* HDInsight 上的一个交互式查询群集。 要创建此群集，请参阅 [Azure HDInsight 入门](../hdinsight-hadoop-provision-linux-clusters.md)。 选择“交互式查询”作为群集类型  。

## <a name="install-microsoft-hive-odbc-driver"></a>安装 Microsoft Hive ODBC 驱动程序

下载并安装 [Microsoft Hive ODBC 驱动程序](https://go.microsoft.com/fwlink/?LinkID=286698)。

## <a name="create-apache-hive-odbc-data-source"></a>创建 Apache Hive ODBC 数据源

下列步骤演示如何创建 Apache Hive ODBC 数据源。

1. 从 Windows 中导航到“开始”   > “Windows 管理工具”   > “ODBC 数据源(32 位)/(64 位)  。  此时将打开“ODBC 数据源管理员”  窗口。

    ![ODBC 数据源管理器](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "使用ODBC 数据源管理器配置 DSN")

1. 在”用户 DSN”选项卡中，选择“添加”，打开“创建新数据源”窗口    。

1. 选择“Microsoft Hive ODBC 驱动程序”，然后选择“完成”，打开“Microsoft Hive ODBC 驱动程序 DSN 设置”窗口    。

1. 键入或选择以下值：

   | 属性 | 说明 |
   | --- | --- |
   |  数据源名称 |为数据源提供名称 |
   |  主机 |输入 `CLUSTERNAME.azurehdinsight.net` 。 例如： `myHDICluster.azurehdinsight.net` |
   |  端口 |使用 **443**。|
   |  数据库 |使用“默认”  。 |
   |  机制 |选择“Windows Azure HDInsight 服务”  |
   |  用户名 |输入 HDInsight 群集 HTTP 用户的用户名。 默认的用户名为 **admin**。 |
   |  密码 |输入 HDInsight 群集用户的密码。 选中“保存密码(已加密)”  复选框。|

1. 可选：选择“高级选项”  。  

   | 参数 | 说明 |
   | --- | --- |
   |  使用本机查询 |当此项处于选中状态时，ODBC 驱动程序不会尝试将 TSQL 转换为 HiveQL。 仅当你完全确定要提交纯 HiveQL 语句时才使用此选项。 连接 SQL Server 或 Azure SQL 数据库时，应将此项保留为未选中状态。 |
   |  每块提取的行数 |提取大量记录时，可能需要调整此参数以确保最佳性能。 |
   |  默认字符串列长度、二进制列长度、十进制列小数位数 |数据类型长度和精度可能会影响返回数据的方式。 由于精度损失和截断，这些语句可能会导致返回不正确的信息。 |

    ![高级 DSN 配置选项](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "高级 DSN 配置选项")

1. 选择“测试”以测试数据源  。 如果数据源配置正确，测试结果会显示“成功”  。  

1. 选择“确定”以关闭“测试”窗口  。  

1. 选择“确定”以关闭“Microsoft Hive ODBC 驱动程序 DSN 设置”窗口   。  

1. 选择“确定”以关闭“ODBC 数据源管理器”窗口   。  

## <a name="query-data-with-powershell"></a>使用 PowerShell 查询数据

以下 PowerShell 脚本是一个使用 ODBC 查询 Hive 群集的函数。

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

以下代码片段使用上述函数对你在本教程开头创建的交互式查询群集执行查询。 将 `DATASOURCENAME` 替换为已在“Microsoft Hive ODBC 驱动程序 DSN 设置”屏幕上指定的“数据源名称”   。 在系统提示输入凭据时，输入创建群集时在“群集登录用户名”和“群集登录密码”下输入的用户名和密码   。

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、HDInsight 群集和存储帐户，请将其删除。 为此，请选择已创建群集的资源组，然后单击“删除”  。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Microsoft Hive ODBC 驱动程序和 PowerShell 从 Azure HDInsight 交互式查询群集检索数据。

> [!div class="nextstepaction"]
> [使用 ODBC 将 Excel 连接到 Apache Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
