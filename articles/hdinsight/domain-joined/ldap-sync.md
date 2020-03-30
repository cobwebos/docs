---
title: 在 Azure HDInsight 中，在游侠和阿帕奇安巴里中的 LDAP 同步
description: 在 Ranger 和 Ambari 中解决 LDAP 同步问题，并提供一般指南。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463214"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>在 Azure HDInsight 中，在游侠和阿帕奇安巴里中的 LDAP 同步

HDInsight 企业安全包 （ESP） 群集使用 Ranger 进行授权。 Apache Ambari 和 Ranger 都独立同步用户和组，并且工作方式稍有不同。 本文旨在解决 Ranger 和 Ambari 中的 LDAP 同步问题。

## <a name="general-guidelines"></a>一般性指导

* 始终部署具有组的群集。
* 请尝试在 Azure AD 中管理所有这些筛选器，并使用嵌套组引入所需的用户，而不是更改 Ambari 和 Ranger 中的组筛选器。
* 同步用户后，即使用户不是组的成员，也不会将其删除。
* 如果需要直接更改 LDAP 筛选器，请使用 UI，因为它包含一些验证。

## <a name="users-are-synced-separately"></a>用户单独同步

Ambari 和 Ranger 不共享用户数据库，因为它们具有两种不同的用途。 如果用户需要使用 Ambari UI，则需要将用户同步到 Ambari。 如果用户未同步到 Ambari，Ambari UI / API 将拒绝它，但系统的其他部分将工作（这些部分由游侠或资源管理器守卫，而不是 Ambari）。 如果要将用户包含在 Ranger 策略中，请将用户同步到 Ranger。

部署安全群集时，组成员会以传输方式（所有子组及其成员）同步到 Ambari 和 Ranger。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 用户同步和配置

从头节点、cron 作业`/opt/startup_scripts/start_ambari_ldap_sync.py`每小时运行一次以计划用户同步。cron 作业调用 Ambari 休息 API 以执行同步。该脚本提交要同步的用户和组的列表（因为用户可能不属于指定的组，两者都是单独指定的）。 Ambari 将 sAMAccountName 同步为用户名，并且所有组成员都以传递方式同步。

日志应位于`/var/log/ambari-server/ambari-server.log`中。 有关详细信息，请参阅配置[Ambari 日志记录级别](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 群集中，帖子用户创建挂钩用于为同步用户创建主文件夹，并将这些文件夹设置为主文件夹的所有者。 如果用户未正确同步到 Ambari，则用户在访问暂存文件夹和其他临时文件夹时可能会面临失败。

### <a name="update-groups-to-be-synced-to-ambari"></a>更新要同步到 Ambari 的组

如果无法管理 Azure AD 中的组成员身份，则有两种选择：

* 在[同步 LDAP 用户和组](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)中更全面地执行一次性同步。 每当组成员身份发生更改时，您必须再次执行此同步。

* 编写 cron 作业，定期使用新组调用[Ambari API。](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634)

## <a name="ranger-user-sync-and-configuration"></a>游侠用户同步和配置

Ranger 具有一个内置同步引擎，每小时运行一次以同步用户。 它不与 Ambari 共享用户数据库。 HDInsight 配置搜索筛选器以同步管理员用户、看门狗用户和群集创建期间指定的组成员。 组成员将经过同步：

* 禁用增量同步。
* 启用用户组同步映射。
* 指定搜索筛选器以包括传递组成员。
* 同步用户 sAMAccount 名称和组的名称属性。

### <a name="group-or-incremental-sync"></a>组或增量同步

Ranger 支持组同步选项，但它用作与用户筛选器的交集。 不是组成员身份和用户筛选器之间的联合。 Ranger 中组同步筛选器的典型用例是 - 组筛选器：（dn_群集管理组）、用户筛选器：（城市_西雅图）。

增量同步仅适用于已同步的用户（首次）。 增量不会同步在初始同步后添加到组的任何新用户。

### <a name="update-ranger-sync-filter"></a>更新游侠同步筛选器

LDAP 筛选器可以在 Ambari UI 下 Ranger 用户同步配置部分中找到。 现有筛选器将处于 窗体`(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`中。 请确保在末尾添加谓词，并使用`net ads`搜索命令或 ldp.exe 或类似内容测试筛选器。

## <a name="ranger-user-sync-logs"></a>游侠用户同步日志

游侠用户同步可能发生在任一头节点外。 日志在`/var/log/ranger/usersync/usersync.log`中。 要增加日志的详细性，执行以下步骤：

1. 登录安巴里。
1. 转到游侠配置部分。
1. 转到高级**用户同步日志4j**部分。
1. 将`log4j.rootLogger`更改为`DEBUG`级别（更改后，它应`log4j.rootLogger = DEBUG,logFile,FilterLog`看起来像 ）。
1. 保存配置并重新启动护林员。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的身份验证问题](./domain-joined-authentication-issues.md)
* [将 Azure AD 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
