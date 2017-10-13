---
title: "管理已加入域的 HDInsight 群集 - Azure | Microsoft Docs"
description: "了解如何管理已加入域的 HDInsight 群集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 9b56ce6cc5bdd3b8d48d047751e978cad08598e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>管理已加入域的 HDInsight 群集（预览版）
了解已加入域的 HDInsight 中的用户和角色以及如何管理已加入域的 HDInsight 群集。

## <a name="users-of-domain-joined-hdinsight-clusters"></a>已加入域的 HDInsight 群集的用户
未加入域的 HDInsight 群集具有两个在群集创建期间创建的用户帐户：

* **Ambari 管理员**：此帐户也称为 Hadoop 用户 或 HTTP 用户。 此帐户可用于在 https://&lt;clustername>.azurehdinsight.net 中登录到 Ambari。 也可将其用于在 Ambari 视图上运行查询、通过外部工具（即 PowerShell、Templeton、Visual Studio）执行作业和使用 Hive ODBC 驱动程序和 BI 工具（即 Excel、PowerBI 或 Tableau）进行身份验证。
* **SSH 用户**：此帐户可用于 SSH 和执行 sudo 命令。 它具有 Linux VM 的 root 权限。

加入域的 HDInsight 群集具有除 Ambari 管理员和 SSH 用户外的三个新用户。

* **Ranger 管理员**：此帐户是本地的 Apache Ranger 管理员帐户。 它不是 Active Directory 域用户。 此帐户可用于设置策略和让其他用户成为管理员或委托管理员（这些用户则可管理策略）。 默认情况下，用户名为 admin，密码与 Ambari 管理员密码相同。 可从 Ranger 中的“设置”页更新密码。
* **群集管理员域用户**：此帐户是指定为 Hadoop 群集管理员的 Active Directory 域用户（包括 Ambari 和 Ranger）。 群集创建过程中必须提供用户的凭据。 此用户具有以下权限：

  * 将计算机加入到域，并将其放在群集创建期间指定的 OU 中。
  * 在群集创建期间指定的 OU 内创建服务主体。
  * 创建反向 DNS 条目。

    请注意其他 AD 用户也具有这些权限。

    群集内有一些终结点不受 Ranger 管理（如 Templeton），因此不安全。 这些终结点已对所有用户锁定（群集管理员域用户除外）。
* **常规**：群集创建期间，可提供多个 Active Directory 组。 这些组中的用户将同步到 Ranger 和 Ambari。 这些用户为域用户，仅对由 Ranger 管理的终结点具有访问权限（例如，Hiveserver2）。 所有 RBAC 策略和审核都适用于这些用户。

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>已加入域的 HDInsight 群集的角色
已加入域的 HDInsight 具有以下角色：

* 群集管理员
* 群集操作员
* 服务管理员
* 服务操作员
* 群集用户

**查看这些角色的权限**

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 在左侧菜单中，单击“角色”。
3. 单击蓝色问号查看权限：

    ![已加入域的 HDInsight 角色权限](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>打开 Ambari 管理 UI
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在边栏选项卡中打开 HDInsight 群集。 请参阅[列出和显示群集](hdinsight-administer-use-management-portal.md#list-and-show-clusters)。
3. 在顶部菜单中单击“仪表板”以打开 Ambari。
4. 使用群集管理员域用户名和密码登录到 Ambari。
5. 在右上角单击“管理员”下拉菜单，并单击“管理 Ambari”。

    ![加入域的 HDInsight 管理 Ambari](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    UI 如下所示：

    ![加入域的 HDInsight Ambari 管理 UI](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>列出从 Active Directory 同步的域用户
1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 在左侧菜单中，单击“用户”。 可看到从 Active Directory 同步到 HDInsight 群集的所有用户。

    ![加入域的 HDInsight Ambari 管理 UI 列表用户](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>列出从 Active Directory 同步的域组
1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 在左侧菜单中，单击“组”。 可看到从 Active Directory 同步到 HDInsight 群集的所有组。

    ![加入域的 HDInsight Ambari 管理 UI 列表组](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>配置 Hive 视图权限
1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 在左侧菜单中，单击“视图”。
3. 单击“HIVE” 以显示详细信息。

    ![加入域的 HDInsight Ambari 管理 UI Hive 视图](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. 单击“Hive 视图”链接以配置 Hive 视图。
5. 向下滚动到“权限”部分。

    ![加入域的 HDInsight Ambari 管理 UI Hive 视图配置权限](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. 单击“添加用户”或“添加组”，并指定可以使用 Hive 视图的用户或组。

## <a name="configure-users-for-the-roles"></a>为角色配置用户
 若要查看角色及其权限的列表，请参阅[已加入域的 HDInsight 群集的角色](#roles-of-domain---joined-hdinsight-clusters)。

1. 打开 Ambari 管理 UI。  请参阅[打开 Ambari 管理 UI](#open-the-ambari-management-ui)。
2. 在左侧菜单中，单击“角色”。
3. 单击“添加用户”或“添加组”将用户和组分配到不同角色。

## <a name="next-steps"></a>后续步骤
* 若要配置已加入域的 HDInsight 群集，请参阅 [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md)（配置已加入域的 HDInsight 群集）。
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关在已加入域的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
