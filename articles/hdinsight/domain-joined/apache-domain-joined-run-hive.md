---
title: Apache Ranger 中的 Apache Hive 策略 - Azure HDInsight
description: 了解如何为具有企业安全性套餐的 Azure HDInsight 服务中的 Hive 配置 Apache Ranger 策略。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 03c783b5a475f0a49fe94d33aa866654e9c9f5f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397821"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>在具有企业安全性套餐的 HDInsight 中配置 Apache Hive 策略

了解如何为 Apache Hive 配置 Apache Ranger 策略。 本文涉及创建两个 Ranger 策略来限制对 hivesampletable 的访问。 hivesampletable 随 HDInsight 群集提供。 配置策略后，使用 Excel 和 ODBC 驱动程序连接到 HDInsight 中的 Hive 表。

## <a name="prerequisites"></a>先决条件

* 具有企业安全性套餐的 HDInsight 群集。 请参阅[配置具有 ESP 的 HDInsight 群集](apache-domain-joined-configure.md)。
* 一个工作站，具有适用于企业、Office 2016、Office 2013 Professional Plus、Excel 2013 独立版或 Office 2010 Professional Plus 的 Microsoft 365 应用。

## <a name="connect-to-apache-ranger-admin-ui"></a>连接到 Apache Ranger 管理 UI
**连接到 Ranger 管理 UI**

1. 在浏览器中，导航到 Ranger 管理 UI (`https://CLUSTERNAME.azurehdinsight.net/Ranger/`)，其中的 CLUSTERNAME 是群集的名称。

   > [!NOTE]  
   > Ranger 使用的凭据与 Apache Hadoop 群集不同。 若要防止浏览器使用缓存的 Hadoop 凭据，请使用新的 InPrivate 浏览器窗口连接到 Ranger 管理 UI。

2. 使用群集管理员域用户名和密码登录：

    ![HDInsight ESP Ranger 主页](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    目前，Ranger 仅适用于 Yarn 和 Hive。

## <a name="create-domain-users"></a>创建域用户

请参阅[创建具有 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)，了解如何创建 hiveruser1 和 hiveuser2。 本文使用这两个用户帐户。

## <a name="create-ranger-policies"></a>创建 Ranger 策略

本部分将创建用于访问 hivesampletable 的两个 Ranger 策略。 提供对不同列组的 select 权限。 这两个用户是在[创建具有 ESP 的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)的教程中创建的。 在下一部分，需在 Excel 中测试这两个策略。

**创建 Ranger 策略**

1. 打开 Ranger 管理 UI。 请参阅“连接到 Apache Ranger 管理 UI”。
2. 在“Hive”下，选择“CLUSTERNAME_Hive”。 应看到两个预配置的策略。
3. 选择“添加新策略”，然后输入以下值：

    |属性 |值 |
    |---|---|
    |策略名称|read-hivesampletable-all|
    |Hive 数据库|默认值|
    |表|hivesampletable|
    |Hive 列|*|
    |选择用户|hiveuser1|
    |权限|select|

    ![HDInsight ESP Ranger Hive 策略配置](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    > [!NOTE]  
    > 如果域用户未在“选择用户”中填充，请等待几分钟让 Ranger 与 AAD 同步。

4. 选择“添加”以保存策略。

5. 重复最后两个步骤以创建具有以下属性的另一个策略：

    |属性 |值 |
    |---|---|
    |策略名称|read-hivesampletable-devicemake|
    |Hive 数据库|默认值|
    |表|hivesampletable|
    |Hive 列|clientid、devicemake|
    |选择用户|hiveuser2|
    |权限|select|

## <a name="create-hive-odbc-data-source"></a>创建 Hive ODBC 数据源

可以在 [创建 Hive ODBC 数据源](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)中找到相关说明。  

 | 属性  |说明 |
 | --- | --- |
 | 数据源名称 | 为数据源提供名称 |
 | 主机 | 输入 CLUSTERNAME.azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net |
 | 端口 | 使用 **443**。 （此端口已从 563 更改为 443。） |
 | 数据库 | 使用“默认”。 |
 | Hive 服务器类型 | 选择“Hive Server 2” |
 | 机制 | 选择“Azure HDInsight 服务” |
 | HTTP 路径 | 将此字段留空。 |
 | 用户名 | 输入 hiveuser1@contoso158.onmicrosoft.com。 如果域名不同，请更新域名。 |
 | 密码 | 输入 hiveuser1 的密码。 |

请确保在保存数据源之前单击“测试”  。

## <a name="import-data-into-excel-from-hdinsight"></a>将 HDInsight 中的数据导入到 Excel 中

在上一部分中，你配置了两个策略。  hiveuser1 对所有列具有 select 权限，而 hiveuser2 对两个列具有 select 权限。 在此部分中，将模拟这两个用户将数据导入到 Excel 中。

1. 在 Excel 中打开新工作簿或现有工作簿。

1. 在“数据”选项卡中，导航到“获取数据” > “从其他源” > “从 ODBC”来启动“从 ODBC”窗口    。

    ![打开数据连接向导](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. 在下拉列表中，选择在上一部分创建的数据源名称，然后选择“确定”。

1. 第一次使用时，将打开“ODBC 驱动程序”对话框。 从左侧菜单中选择 **Windows**。 然后选择“连接”以打开“导航器”窗口。

1. 等待“选择数据库和表”对话框打开  。 这可能需要几秒钟时间。

1. 选择“hivesampletable”，然后选择“下一步”。

1. 选择“完成”。

1. 在“导入数据”对话框中，可以更改或指定查询。 为此，请选择“属性”。 这可能需要几秒钟时间。

1. 选择“定义”选项卡。命令文本为：

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   根据定义的 Ranger 策略，hiveuser1 对所有列拥有 select 权限。  因此，此查询可以使用 hiveuser1 的凭据，但不能使用 hiveuser2 的凭据。

1. 选择“确定”关闭“连接属性”对话框。

1. 选择“确定”关闭“导入数据”对话框。  

1. 重新输入 hiveuser1 的密码，并单击“确定” 。 需要几秒钟时间才能将数据导入到 Excel 中。 完成后，应看到 11 个列的数据。

测试在最后一个部分中创建的第二个策略 (read-hivesampletable-devicemake)

1. 在 Excel 中添加新工作表。
2. 按照上一过程导入数据。  所做的唯一更改是使用 hiveuser2 的凭据，而不是 hiveuser1 的凭据。 此查询失败，因为 hiveuser2 仅有权查看两个列。 将收到以下错误：

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. 按照相同的过程导入数据。 这次使用 hiveuser2 的凭据，并且还修改 select 语句，从：

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    to:

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    完成后，应看到导入了两列的数据。

## <a name="next-steps"></a>后续步骤

* 有关使用企业安全性套餐配置 HDInsight 群集的信息，请参阅[使用 ESP 配置 HDInsight 群集](apache-domain-joined-configure.md)。
* 有关管理具有 ESP 的 HDInsight 群集的信息，请参阅[管理具有 ESP 的 HDInsight 群集](apache-domain-joined-manage.md)。
* 有关在具有 ESP 的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)。
* 若要使用 Hive JDBC 连接 Hive，请参阅 [Connect to Apache Hive on Azure HDInsight using the Hive JDBC driver](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)（使用 Hive JDBC 驱动程序连接到 Azure HDInsight 上的 Apache Hive）
* 若要使用 Hive ODBC 将 Excel 连接到 Hadoop，请参阅 [Connect Excel to Apache Hadoop with the Microsoft Hive ODBC drive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)（使用 Microsoft Hive ODBC 驱动程序将 Excel 连接到 Apache Hadoop）
* 若要使用 Power Query 将 Excel 连接到 Hadoop，请参阅 [Connect Excel to Apache Hadoop by using Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)（使用 Power Query 将 Excel 连接到 Apache Hadoop）
