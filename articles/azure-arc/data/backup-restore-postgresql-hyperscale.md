---
title: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
description: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d27537f017707e937303dd0c08a589db28aac6ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071432"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>启用 Azure Arc 的备份和还原 PostgreSQL 超大规模服务器组

可以对启用了 Azure Arc 的 PostgreSQL 超大规模服务器组进行完整备份/还原。 当你执行此操作时，将备份和/或还原启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的所有节点上的整个数据库集。
若要执行备份并还原，需要确保为服务器组配置了备份存储类。 现在，你需要在创建服务器组时指示备份存储类。 但在创建后，不能将服务器组配置为使用备份存储类。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> 预览不支持 Postgres 引擎版本11的备份/还原。 它仅支持 Postgres 版本12的备份/还原。

## <a name="verify-configuration"></a>验证配置

首先，验证是否已将现有服务器组配置为使用备份存储类。

设置服务器组的名称后，运行以下命令：
```console
 azdata arc postgres server show -n postgres01
```
查看输出的 "存储" 部分：
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
如果你看到在该命令的输出的 "备份" 部分中指示的存储类的名称，则表示你的服务器组已配置为使用备份存储类，并且已准备好进行备份和恢复。 如果看不到 "备份" 部分，则需要删除并重新创建服务器组以配置备份存储类。 此时，在创建服务器组后，还不能配置备份存储类。

>[!IMPORTANT]
>如果你的服务器组已配置为使用备份存储类，请跳过下一步，直接转到 "执行手动完整备份" 步骤。

## <a name="create-a-server-group"></a>创建服务器组 

接下来，创建为备份/还原配置的服务器组。

为了能够备份和还原它们，需要创建一个配置有存储类的服务器。

若要获取 Kubernetes 群集上可用的存储类的列表，请运行以下命令：

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

例如，如果你创建了基于 kubeadm 的简单环境：
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>进行手动完整备份


接下来，执行手动完整备份。

> [!CAUTION]
> **对于 Azure Kubernetes Service 的用户 (AKS 仅) ：** 我们注意到在 Azure Kubernetes Service 上托管的服务器组的备份 (AKS) 的问题。 我们已经致力于解决它。 在将来的版本/更新中部署更新之前，你需要删除服务器组的盒箱。 对于服务器组的每个 pod (通过运行**kubectl get pod-n \<namespace name> **来列出 pod，) 通过运行**kubectl 删除 Pod \<server group pod name> -n \<namespace name> **来删除它们。 请勿删除不属于您的服务器组的 pod。 删除 pod 不会使数据面临风险。 等待所有 pod 恢复联机状态，并在执行备份之前状态 = 正在运行。 在上面的 kubectl get pod 命令的输出中提供了 pod 的状态。


若要对服务器组的整个数据和日志文件夹进行完整备份，请运行以下命令：

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
其中：
- __名称__ 指示备份的名称
- __服务器名称__ 指示服务器组
- "__否"-等待__指示命令行不会等待备份完成，你可以继续使用此命令行窗口

此命令将在构成启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的所有节点之间协调分布式完整备份。 换句话说，它将备份协调器和辅助节点中的所有数据。

例如：
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

备份完成后，将返回备份的 ID、名称和状态。 例如：
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> 尚不能：
> - 计划自动备份
> - 显示执行备份的进度

## <a name="list-backups"></a>列出备份

列出可用于还原的备份。

若要列出可用于还原的备份，请运行以下命令：

```console
azdata arc postgres backup list --server-name <servergroup name>
```

例如：
```console
azdata arc postgres backup list --server-name postgres01
```

它将返回如下所示的输出：
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Timestamp 指示进行备份的时间点。

## <a name="restore-a-backup"></a>还原备份

若要还原整个服务器组的备份，请运行以下命令：

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

其中：
- __backup-id__ 是列表备份命令中所示的备份 id (引用第3步) 。
这会在构成启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的所有节点之间协调分布式完全还原。 换句话说，它将还原协调器和工作节点中的所有数据。

例如：
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

还原操作完成后，它会将类似于下面的输出返回到命令行：
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> 尚不能：
> - 通过指示备份的名称来还原备份
> - 用不同的名称或不同的服务器组还原服务器组
> - 显示还原操作的进度

## <a name="delete-backups"></a>删除备份
无法在预览中设置备份保持期。 不过，您可以手动删除不需要的备份。
用于删除备份的常规命令是：
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
其中：
- `--server-name` 用户要从中删除备份的服务器组的名称
- `--name` 要删除的备份的名称
- `-id`要删除的备份的 ID

> [!NOTE]
> `--name` 和 `-id` 互斥。

可以通过运行上一段中所述的 "列出备份" 命令来检索备份的名称和 ID。

例如，假设已列出以下备份：
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
你需要删除其中的第一个，你可以运行以下命令：
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
如果要在此时列出备份，你将获得以下输出：
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

有关 delete 命令的详细信息，请运行：
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>后续步骤
- 阅读[ (向) 服务器组添加辅助角色节点](scale-out-postgresql-hyperscale-server-group.md)
- 阅读有关[扩展或缩减 (增加/减少服务器组) 的内存/vcore](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
