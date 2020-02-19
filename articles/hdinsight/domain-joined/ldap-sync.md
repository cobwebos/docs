---
title: Azure HDInsight 中的 Ranger 和 Apache Ambari 中的 LDAP 同步
description: 在 Ranger 和 Ambari 中处理 LDAP 同步，并提供一般准则。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463214"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 中的 Ranger 和 Apache Ambari 中的 LDAP 同步

HDInsight 企业安全性套餐（ESP）群集使用 Ranger 进行授权。 Apache Ambari 和 Ranger 分别同步用户和组并以不同的方式工作。 本文旨在解决 Ranger 和 Ambari 中的 LDAP 同步。

## <a name="general-guidelines"></a>一般性指导

* 始终通过组部署群集。
* 不是在 Ambari 和 Ranger 中更改组筛选器，而是尝试在 Azure AD 中管理所有这些筛选器，并使用嵌套组来引入所需的用户。
* 用户同步后，即使用户不是组的成员，也不会将其删除。
* 如果需要直接更改 LDAP 筛选器，请首先使用 UI，因为它包含一些验证。

## <a name="users-are-synced-separately"></a>用户单独同步

Ambari 和 Ranger 不共享用户数据库，因为它们提供两个不同的目的。 如果用户需要使用 Ambari UI，则需要将用户同步到 Ambari。 如果用户未同步到 Ambari，则 Ambari UI/API 将拒绝该用户，但系统的其他部分将起作用（这些部分受 Ranger 或资源管理器保护，而不是 Ambari）。 如果要将用户包括到 Ranger 策略，请将用户同步到 Ranger。

部署安全群集后，组成员会将可传递的（所有子组及其成员）同步到 Ambari 和 Ranger。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 用户同步和配置

在头节点中，cron 作业 `/opt/startup_scripts/start_ambari_ldap_sync.py`每小时运行一次，以便计划用户同步。Cron 作业将调用 Ambari rest Api 来执行同步。脚本将提交要同步的用户和组的列表（因为用户可能不属于指定的组，这两者都是单独指定的）。 Ambari 将 sAMAccountName 作为用户名和所有组成员按方式同步。

日志应 `/var/log/ambari-server/ambari-server.log`中。 有关详细信息，请参阅[Configure Ambari 日志记录级别](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 群集中，后期用户创建挂钩用于为同步的用户创建主文件夹，并将其设置为主文件夹的所有者。 如果用户未正确同步到 Ambari，则用户可能会面临访问过渡和其他临时文件夹的故障。

### <a name="update-groups-to-be-synced-to-ambari"></a>要同步到 Ambari 的更新组

如果无法管理 Azure AD 中的组成员身份，则有两个选择：

* 在[同步 LDAP 用户和组](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)中更全面地执行一次同步。 每当组成员身份更改时，你都必须再次执行此同步。

* 编写一个 cron 作业，使用新组[定期调用 AMBARI API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) 。

## <a name="ranger-user-sync-and-configuration"></a>Ranger 用户同步和配置

Ranger 有一个内置同步引擎，每小时运行一次即可同步用户。 它不会与 Ambari 共享用户数据库。 HDInsight 将搜索筛选器配置为同步管理员用户、监视程序用户，以及在群集创建过程中指定的组的成员。 组成员将进行可传递同步：

* 禁用增量同步。
* 启用用户组同步映射。
* 指定搜索筛选器以包括可传递的组成员。
* 为用户同步 sAMAccountName 并为组提供 name 属性。

### <a name="group-or-incremental-sync"></a>组或增量同步

Ranger 支持组同步选项，但它可作为与用户筛选器的交集一起工作。 不是组成员身份和用户筛选器之间的联合。 Ranger 中的组同步筛选器的典型用例是-组筛选器：（dn = clusteradmingroup），用户筛选器：（city = 西雅图）。

增量同步仅适用于已同步的用户（第一次）。 在初始同步后，增量不同步添加到组的任何新用户。

### <a name="update-ranger-sync-filter"></a>更新 Ranger 同步筛选器

可以在 Ambari UI 中的 "Ranger 用户-同步配置" 部分下找到 LDAP 筛选器。 现有筛选器的格式将为 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`。 确保在末尾添加谓词，并使用 `net ads` search 命令或 ldp.exe 或类似的内容来测试筛选器。

## <a name="ranger-user-sync-logs"></a>Ranger 用户同步日志

Ranger 用户同步可能会在头节点中发生。 日志位于 `/var/log/ranger/usersync/usersync.log`。 若要增加日志的详细级别，请执行以下步骤：

1. 登录到 Ambari。
1. 请参阅 Ranger 配置部分。
1. 请参阅 Advanced **usersync-log4j**部分。
1. 将 `log4j.rootLogger` 更改为 `DEBUG` 级别（更改后应类似于 `log4j.rootLogger = DEBUG,logFile,FilterLog`）。
1. 保存配置并重新启动 ranger。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的身份验证问题](./domain-joined-authentication-issues.md)
* [将 Azure AD 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
