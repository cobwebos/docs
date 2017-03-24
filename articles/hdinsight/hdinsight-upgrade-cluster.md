---
title: "从基于 Windows 的 HDInsight 迁移到基于 Linux 的 HDInsight -Azure | Microsoft 文档"
description: "了解如何从基于 Windows 的 HDInsight 群集迁移到基于 Linux 的 HDInsight 群集。"
services: hdinsight
documentationcenter: 
author: bhanupr
manager: asadk
editor: bhanupr
ms.assetid: 60eb573c-e639-4815-9fc6-ea8b106d8dbc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/11/2017
ms.author: bhanupr
translationtype: Human Translation
ms.sourcegitcommit: 8c59375290e410c34ba25d4e5d8e8f9f8de0cafe
ms.openlocfilehash: ceb5f5c639633d7118a057927b236b51b54f8fa7
ms.lasthandoff: 02/04/2017


---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>将 HDInsight 群集升级到更新版本
若要利用最新的 HDInsight 功能，我们建议将 HDInsight 群集升级到最新版本。 遵循以下准则升级 HDInsight 群集版本。

> [!NOTE]
> HDInsight 群集版本 3.2 和 3.3 接近弃用日期。 有关支持的 HDInsight 版本的信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning.md#supported-hdinsight-versions)。
>
>

## <a name="upgrade-tasks"></a>升级任务
升级 HDInsight 群集的工作流如下所示。

![升级工作流示意图](./media/hdinsight-upgrade-cluster/upgrade-workflow.png)

1. 请阅读本文档的每个部分，了解在升级 HDInsight 群集时可能需要进行的更改。
2. 创建群集作为测试/质量保证环境。 有关创建群集的详细信息，请参阅[了解如何创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
3. 将现有作业、数据源及接收器复制到新环境。 有关详细信息，请参阅[将数据复制到测试环境](hdinsight-migrate-from-windows-to-linux.md#copy-data-to-the-test-environment)。
4. 执行验证测试，以确保作业在新群集上按预期工作。


验证一切都按预期工作后，请为迁移安排停机时间。 在此停机期间，请执行以下操作：

1.    备份所有存储在本地群集节点上的暂时性数据。 例如，如果你的数据直接存储在头节点上。
2.    删除现有的群集。
3.    使用前一群集使用的同一默认数据存储在具有最新（或支持）的 HDI 版本的同一 VNET 子网中创建群集。 这样，新群集便可针对现有生产数据继续运行。
4.    导入任何已备份的暂时性数据。
5.    使用新群集启动作业/继续处理。

## <a name="next-steps"></a>后续步骤
* [了解如何创建基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)
* [从 Windows 客户端使用 SSH 连接到基于 Linux 的群集](hdinsight-hadoop-linux-use-ssh-windows.md)
* [从 Linux、Unix，或 Mac 客户端使用 SSH 连接到基于 Linux 的群集](hdinsight-hadoop-linux-use-ssh-unix.md)
* [使用 Ambari 管理基于 Linux 的群集](hdinsight-hadoop-manage-ambari.md)


