---
title: Azure HDInsight 中的 Ranger 和 Apache Ambari 中的 LDAP 同步
description: 解决 Ranger 和 Ambari 中的 LDAP 同步问题并提供一般性指导。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463214"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 中的 Ranger 和 Apache Ambari 中的 LDAP 同步

HDInsight 企业安全性套餐 (ESP) 群集使用 Ranger 进行授权。 Apache Ambari 和 Ranger 都独立地同步用户和组，但其工作方式略有不同。 本文可帮助用户解决 Ranger 和 Ambari 中的 LDAP 同步问题。

## <a name="general-guidelines"></a>一般性指导

* 始终通过组部署群集。
* 不要在 Ambari 和 Ranger 中更改组筛选器，请尽量在 Azure AD 中管理所有这些筛选器，并使用嵌套组来引入所需的用户。
* 同步某个用户后，即使该用户不属于组，也不会将其删除。
* 如果你需要直接更改 LDAP 筛选器，请首先使用 UI，因为它包含一些验证。

## <a name="users-are-synced-separately"></a>用户是分别同步的

Ambari 和 Ranger 不共享用户数据库，因为它们用于两种不同的用途。 如果用户需要使用 Ambari UI，则需要将用户同步到 Ambari。 如果用户未同步到 Ambari，则 Ambari UI/API 会拒绝该用户，但系统的其他部分可以正常运行（这些部分受 Ranger 或资源管理器保护，而不是受 Ambari 保护）。 如果要将用户包括到某个 Ranger 策略中，请将用户同步到 Ranger。

部署安全群集后，系统会以传递方式将组成员（包括所有子组及其成员）同步到 Ambari 和 Ranger。 

## <a name="ambari-user-sync-and-configuration"></a>Ambari 的用户同步和配置

在头节点中，cron 作业 `/opt/startup_scripts/start_ambari_ldap_sync.py` 每小时运行一次来调度用户同步。Cron 作业将调用 Ambari REST API 来执行同步。脚本将提交要同步的用户和组的列表（由于用户可能不属于指定的组，因此这两者是分别指定的）。 Ambari 以传递方式将 sAMAccountName 作为用户名和所有组成员进行同步。

日志应位于 `/var/log/ambari-server/ambari-server.log` 中。 有关详细信息，请参阅[配置 Ambari 日志记录级别](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html)。

在 Data Lake 群集中，将使用创建用户后的挂钩为同步的用户创建主文件夹，并将这些用户设置为主文件夹的所有者。 如果用户未正确同步到 Ambari，则用户在访问暂存文件夹和其他临时文件夹时可能会遇到故障。

### <a name="update-groups-to-be-synced-to-ambari"></a>更新要同步到 Ambari 的组

如果无法在 Azure AD 中管理组成员身份，则有两个选择：

* 执行一次性同步，这在[同步 LDAP 用户和组](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html)中进行了更全面的介绍。 每当组成员身份更改时，你都必须再次执行此同步。

* 编写一个 cron 作业，使用新组[定期调用 Ambari API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634)。

## <a name="ranger-user-sync-and-configuration"></a>Ranger 的用户同步和配置

Ranger 有一个内置的同步引擎，每小时运行一次来同步用户。 它不与 Ambari 共享用户数据库。 HDInsight 会对搜索筛选器进行配置，以便对管理员用户、监视用户以及在创建群集期间指定的组的成员进行同步。 组成员将以传递方式进行同步：

* 禁用增量同步。
* 启用用户组同步映射。
* 将搜索筛选器指定为包括可传递的组成员。
* 同步用户的 sAMAccountName 和组的名称特性。

### <a name="group-or-incremental-sync"></a>组同步或增量同步

Ranger 支持组同步选项，但它作为与用户筛选器的交集工作， 而不是作为组成员身份与用户筛选器的并集工作。 Ranger 中的组同步筛选器的典型用例如下：组筛选器：(dn=clusteradmingroup)，用户筛选器：(city=seattle)。

增量同步仅适用于已首次同步的用户。 增量同步不会同步在初始同步后添加到组的任何新用户。

### <a name="update-ranger-sync-filter"></a>更新 Ranger 同步筛选器

可以在 Ambari UI 中的“Ranger 用户-同步配置”部分找到 LDAP 筛选器。 现有筛选器的格式将为 `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`。 请确保在末尾添加谓词，并使用 `net ads` 搜索命令、ldp.exe 或类似的工具来测试筛选器。

## <a name="ranger-user-sync-logs"></a>Ranger 用户同步日志

Ranger 用户同步可以在任一头节点中进行。 日志位于 `/var/log/ranger/usersync/usersync.log` 中。 若要增大日志的详细级别，请执行以下步骤：

1. 登录到 Ambari。
1. 转到 Ranger 配置部分。
1. 转到“高级 **usersync-log4j**”部分。
1. 将 `log4j.rootLogger` 更改为 `DEBUG` 级别（更改后应类似于 `log4j.rootLogger = DEBUG,logFile,FilterLog`）。
1. 保存配置并重启 Ranger。

## <a name="next-steps"></a>后续步骤

* [Azure HDInsight 中的身份验证问题](./domain-joined-authentication-issues.md)
* [将 Azure AD 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
