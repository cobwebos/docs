---
title: 管理企业安全性套餐群集-Azure HDInsight
description: 了解如何通过企业安全性套餐管理 Azure HDInsight 群集。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 93880269edd72477f3aa85b2dbdc9d9f3ec8ef25
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435895"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>使用企业安全性套餐管理 HDInsight 群集

了解 HDInsight 企业安全性套餐 (ESP) 中的用户和角色，以及如何管理 ESP 群集。

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>使用 VSCode 链接到已加入域的群集

可以使用 Apache Ambari 管理的用户名链接标准群集，还可以使用域用户名（例如：`user1@contoso.com`）链接安全 Apache Hadoop 群集。

1. 打开 [Visual Studio Code](https://code.visualstudio.com/)。 确保安装了[Spark & Hive 工具](../hdinsight-for-vscode.md)扩展。

1. 按照 Visual Studio Code 的[链接群集](../hdinsight-for-vscode.md#link-a-cluster)中的步骤进行操作。

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>使用 IntelliJ 链接到已加入域的群集

可以使用 Ambari 管理的用户名链接标准群集，还可以使用域用户名（例如：`user1@contoso.com`）链接安全 hadoop 群集。

1. 打开 IntelliJ IDEA。 确保满足所有[先决条件](../spark/apache-spark-intellij-tool-plugin.md#prerequisites)。

1. 按照[链接群集](../spark/apache-spark-intellij-tool-plugin.md#link-a-cluster)的 IntelliJ 中的步骤进行操作。

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>使用 Eclipse 链接到已加入域的群集

可以使用 Ambari 管理的用户名链接标准群集，还可以使用域用户名（例如：`user1@contoso.com`）链接安全 hadoop 群集。

1. 打开 Eclipse。 确保满足所有[先决条件](../spark/apache-spark-eclipse-tool-plugin.md#prerequisites)。

1. 按照链接用于 Eclipse 的[群集](../spark/apache-spark-eclipse-tool-plugin.md#link-a-cluster)中的步骤进行操作。

## <a name="access-the-clusters-with-enterprise-security-package"></a>使用企业安全性套餐访问群集

企业安全包（以前称为 HDInsight Premium）提供对群集的多用户访问，其中身份验证通过 Active Directory 以及 Apache Ranger 和存储 ACL (ADLS ACL) 授权来完成。 授权提供多个用户之间的安全边界，并仅允许特权用户根据授权策略访问数据。

安全和用户隔离对于使用企业安全包的 HDInsight 群集很重要。 为了满足这些要求，将阻止通过 SSH 访问使用企业安全包的群集。 下表显示了对于每种群集类型建议的访问方法：

|工作负载|方案|访问方法|
|--------|--------|-------------|
|Apache Hadoop|Hive - 交互式作业/查询  |<ul><li>[Beeline](#beeline)</li><li>[Hive 视图](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|交互式作业/查询，PySpark 交互式环境|<ul><li>[Beeline](#beeline)</li><li>[带有 Livy 的 Zeppelin](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Hive 视图](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Apache Spark|批处理方案 - Spark 提交，PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|交互式查询 (LLAP)|交互|<ul><li>[Beeline](#beeline)</li><li>[Hive 视图](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC - Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio 工具](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Any|安装自定义应用程序|<ul><li>[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > 企业安全性套餐中未安装/支持 Jupyter。

使用标准 API 从安全角度获得帮助。 你还可以获得以下优势：

- **管理** - 可以使用标准 API（Livy、HS2 等）管理代码和自动执行作业。
- **审核**-通过 ssh，没有办法审核，哪些用户可以通过 ssh 连接到群集。 通过标准终结点构造作业时就不会出现这种情况，因为这些作业将在用户上下文中执行。

### <a name="beeline"></a>使用 Beeline

在计算机上安装 Beeline，并使用以下参数通过公共 Internet 进行连接：

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

如果本地安装了 Beeline 并通过 Azure 虚拟网络进行连接，请使用以下参数：

```
Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

若要查找头节点的完全限定域名，请使用“使用 Ambari REST API 管理 HDInsight”文档中的信息。

## <a name="users-of-hdinsight-clusters-with-esp"></a>使用 ESP 的 HDInsight 群集的用户

非 ESP HDInsight 群集具有两个在群集创建期间创建的用户帐户：

- **Ambari 管理员**：此帐户也称为 Hadoop 用户 或 HTTP 用户。 此帐户可用于在 `https://CLUSTERNAME.azurehdinsight.net`登录到 Ambari。 它还可用于在 Ambari 视图上运行查询、通过外部工具（例如 PowerShell、Templeton、Visual Studio）执行作业和使用 Hive ODBC 驱动程序和 BI 工具（例如 Excel、Power BI 或 Tableau）进行身份验证。

使用 ESP 的 HDInsight 群集除 Ambari 管理员之外，还有三个新用户。

- **Ranger 管理员**：此帐户是本地的 Apache Ranger 管理员帐户。 它不是 active directory 域用户。 此帐户可用于设置策略和让其他用户成为管理员或委托管理员（这些用户则可管理策略）。 默认情况下，用户名为 admin，密码与 Ambari 管理员密码相同。 可从 Ranger 中的“设置”页更新密码。

- **群集管理员域用户**：此帐户是指定为 Hadoop 群集管理员的 Active Directory 域用户（包括 Ambari 和 Ranger）。 群集创建过程中必须提供用户的凭据。 此用户具有以下权限：
    - 将计算机加入到域，并将其放在群集创建期间指定的 OU 中。
    - 在群集创建期间指定的 OU 内创建服务主体。
    - 创建反向 DNS 条目。

    请注意其他 AD 用户也具有这些权限。

    群集中有一些终结点（例如，Templeton）不受 Ranger 管理，因此不安全。 这些终结点已对所有用户锁定（群集管理员域用户除外）。

- **常规**：群集创建期间，可提供多个 Active Directory 组。 这些组中的用户将同步到 Ranger 和 Ambari。 这些用户为域用户，仅对由 Ranger 管理的终结点（例如，Hiveserver2）具有访问权限。 所有 RBAC 策略和审核都适用于这些用户。

## <a name="roles-of-hdinsight-clusters-with-esp"></a>使用 ESP 的 HDInsight 群集的角色

HDInsight 企业安全性套餐具有以下角色：

- 群集管理员
- 群集操作员
- 服务管理员
- 服务操作员
- 群集用户

**查看这些角色的权限**

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 从左侧菜单中选择 "**角色**"。
3. 选择蓝色问号以查看权限：

    ![ESP HDInsight 角色权限](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>打开 Ambari 管理 UI

1. 导航到 `https://CLUSTERNAME.azurehdinsight.net/`，其中 CLUSTERNAME 是群集的名称。
1. 使用群集管理员域用户名和密码登录到 Ambari。
1. 从右上角选择 "**管理**" 下拉菜单，然后选择 "**管理 Ambari**"。

    ![ESP HDInsight 管理 Apache Ambari](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI 如下所示：

    ![ESP HDInsight Apache Ambari 管理 UI](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>列出从 Active Directory 同步的域用户

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 从左侧菜单中选择 "**用户**"。 可看到从 Active Directory 同步到 HDInsight 群集的所有用户。

    ![ESP HDInsight Ambari 管理 UI 列表用户](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>列出从 Active Directory 同步的域组

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 从左侧菜单中选择 "**组**"。 可看到从 Active Directory 同步到 HDInsight 群集的所有组。

    ![ESP HDInsight Ambari 管理 UI 列表组](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>配置 Hive 视图权限

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 从左侧菜单中选择 "**视图**"。
3. 选择 " **HIVE** " 以显示详细信息。

    ![ESP HDInsight Ambari 管理 UI Hive 视图](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)

4. 选择 " **Hive 视图**" 链接以配置 hive 视图。
5. 向下滚动到“权限”部分。

    ![ESP HDInsight Ambari 管理 UI Hive 视图配置权限](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)

6. 选择 "**添加用户**" 或 "**添加组**"，然后指定可以使用 Hive 视图的用户或组。

## <a name="configure-users-for-the-roles"></a>为角色配置用户

 若要查看角色及其权限的列表，请参阅“使用 ESP 的 HDInsight 群集的角色”。

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 从左侧菜单中选择 "**角色**"。
3. 选择 "**添加用户**" 或 "**添加组**"，将用户和组分配给不同角色。

## <a name="next-steps"></a>后续步骤

- 有关使用企业安全性套餐配置 HDInsight 群集的信息，请参阅[使用 ESP 配置 HDInsight 群集](apache-domain-joined-configure.md)。
- 有关如何配置 Hive 策略和运行 Hive 查询的信息，请参阅[使用 ESP 配置 HDInsight 群集的 Apache Hive 策略](apache-domain-joined-run-hive.md)。
