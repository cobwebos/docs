---
title: 将 AdventureWorks 示例数据库还原到 SQL 托管实例
description: 将 AdventureWorks 示例数据库还原到 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d238a889648e789087a0803f6b50288318462c7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629057"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>将 AdventureWorks 示例数据库还原到 SQL 托管实例-Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure?view=sql-server-ver15&tabs=tsql&preserve-view=true) 是一个示例数据库，其中包含在教程和示例中经常使用的 OLTP 数据库。 它由 Microsoft 提供并维护，作为 [SQL Server 示例 GitHub 存储库](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

本文档介绍了将 AdventureWorks 示例数据库还原到 SQL 托管实例-Azure Arc 的简单过程。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>下载 AdventureWorks 备份文件

将 AdventureWorks 备份 ( .bak) 文件下载到 SQL 托管实例容器中。 在此示例中，使用 `kubectl exec` 命令远程执行 SQL 托管实例容器中的命令，将 .bak 文件下载到容器中。 `wget`如果要将其他数据库备份文件推送到 SQL 托管实例容器内部，请从可访问的任何位置下载此文件。 在 SQL 托管实例容器中时，可以使用标准 T-sql 轻松还原 `RESTORE DATABASE` 。

运行类似于下面的命令下载 .bak 文件，并在运行它之前替换 pod 名称和命名空间名称的值。
> [!NOTE]
>  容器需要通过 443 internet 连接才能从 GitHub 下载文件

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

示例

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>还原 AdventureWorks 数据库

同样，你可以运行 `kubectl` exec 命令来使用 `sqlcmd` SQL 托管实例容器中包含的 CLI 工具来执行 t-sql 命令来还原数据库。

运行如下命令来还原数据库。 在运行之前，请替换 pod 名称、密码和命名空间名称的值。

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
示例

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
