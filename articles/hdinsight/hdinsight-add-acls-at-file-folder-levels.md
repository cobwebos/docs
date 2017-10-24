---
title: "管理文件和文件夹级别的用户权限 - Azure HDInsight | Microsoft Docs"
description: "如何管理已加入域的 HDInsight 群集的文件和文件夹权限。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>管理文件和文件夹级别的用户权限

[已加入域的 HDInsight 群集](hdinsight-domain-joined-introduction.md)对 Azure Active Directory (Azure AD) 用户使用强身份验证，同时对各种服务（例如 YARN 和 Hive）使用基于角色的访问控制 (RBAC) 策略。 如果群集的默认数据存储是 Azure 存储或 Windows Azure 存储 Blob (WASB)，则还可以强制实施文件和文件夹级别权限。 可以使用 Apache Ranger 来控制已同步的 Azure AD 用户和组对群集文件的访问。
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

已加入域的 HDInsight 群集的 Apache Ranger 实例上已预配置了 Ranger-WASB 服务。 Ranger-WASB 服务是类似于 Ranger-HDFS 的策略管理引擎，但强制实施的 Ranger 访问策略不同。 在 Ranger-WASB 服务中，如果传入的资源请求没有匹配的 Ranger 策略，则默认响应为 DENY。 Ranger 服务不会向 WASB 传递权限检查。

## <a name="permission-and-policy-model"></a>权限和策略模型

使用以下流来评估资源访问请求：

![Apache Ranger 策略评估流](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

先评估 DENY 规则，再评估 ALLOW 规则。 匹配结束时，如果没有任何策略相匹配，则返回 DENY。

### <a name="user-variable"></a>USER 变量

为用户分配策略来访问 `/{username}` 子目录时，可以使用 `{USER}` 变量，例如：

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

上述策略授予用户对 `/app-logs/` 目录下其自己的子文件夹的访问权限。 下面是此策略在 Ranger 用户界面中的大致情况：

![USER 变量的用法示例](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>策略模型示例

下表列出了有关策略模型工作原理的几个示例：

| Ranger 策略 | 现有文件系统 | 用户请求 | 结果 |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 由于执行了上级检查，已创建中间文件夹“finance” |
| /data/finance/, bob, WRITE | /data | alice, Create file /data/finance/mydatafile.txt | DENY - 没有匹配的策略 |
| /data/finance*, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 在本例中，存在可选的递归策略 (`*`)；请参阅[通配符](#wildcards) |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | DENY - 由于没有策略，对“/data”执行上级检查失败 |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, Create file /data/finance/mydatafile.txt | DENY - 没有策略，无法对“/data/finance”执行上级检查 |

根据操作的类型，需要文件夹级别或文件级别的权限。 例如，“read/open”调用需要文件级别的读取访问权限，而“create”调用需要上级文件夹级别的权限。

### <a name="wildcards-"></a>通配符 (*)

如果策略的路径中存在通配符 (`*`)，该通配符会应用到该路径及其整个子树。 这种递归等同于使用 `recurse-flag`。 在 Ranger-WASB 中，通配符指示递归和部分名称匹配。

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>使用 Apache Ranger 管理文件和文件夹级别权限

请遵循[这些说明](hdinsight-domain-joined-configure.md)预配新的已加入域的群集（如果尚未这样做）。

浏览到 `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/` 打开 Ranger-WASB。 输入创建群集时定义的群集管理员用户名和密码。

登录后，会看到 Ranger 仪表板：

![Ranger 仪表板](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

若要查看群集的关联 Azure 存储帐户的当前文件和文件夹权限，请在 WASB 控件框中单击“*CLUSTERNAME*_wasb”链接。

![Ranger 仪表板](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

此时会显示当前策略列表。 已包含多个典型策略作为操作起点 - 请查看每个策略的详细信息来了解示例用法。

对于每个策略，可以看到是否已启用该策略、是否已配置审核日志记录，以及所有已分配的组和用户。 每个策略有两个操作按钮：“编辑”和“删除”。

![策略列表](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>添加新策略

1. 在 WASB 策略页的右上角，选择“添加新策略”。

    ![添加新策略](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. 输入描述性的“策略名称”。 指定群集的 Azure“存储帐户”(*ACCOUNT_NAME*.blob.core.windows.net)，以及创建群集时指定的“存储帐户容器”。 输入所访问的文件夹或文件的“相对路径”（相对于群集）。

    ![“新建策略”窗体](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. 在窗体下面，指定此新资源的“允许条件”。 选择适用的组和用户并设置其权限。 在以下示例中，我们要允许 `sales` 组中的所有用户拥有读/写访问权限。

    ![允许销售组](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. 选择“保存”。

### <a name="example-policy-conditions"></a>示例策略条件

使用 Apache Ranger [策略评估流](#permission-and-policy-model)可以根据需要指定允许和拒绝条件的任意组合。 以下是一些示例：

1. 允许所有销售用户，但不允许实习生：

    ![允许销售用户，拒绝实习生](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. 允许所有销售用户并拒绝所有实习生，但名为“hiveuser3”的实习生除外，该用户应该拥有“读取”访问权限：

    ![允许销售用户，拒绝除 hiveuser3 以外的实习生](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>后续步骤

* [在已加入域的 HDInsight 中配置 Hive 策略](hdinsight-domain-joined-run-hive.md)
* [管理已加入域的 HDInsight 群集](hdinsight-domain-joined-manage.md)
* [管理 Ambari - 授权用户访问 Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

