---
title: 配置、优化 AzCopy 和排查 Azure 存储空间问题 |Microsoft Docs
description: 配置、优化 AzCopy 并对其进行故障排除。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c16fea8f710751a051995ecece8a3d0ce8f933c7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926459"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>配置、优化 AzCopy 并对其进行故障排除

AzCopy 是一个命令行实用工具，可用于在存储帐户中复制 blob 或文件。 本文将帮助你执行高级配置任务，并帮助你解决使用 AzCopy 时可能出现的问题。

> [!NOTE]
> 如果你正在寻找有助于你开始处理 AzCopy 的内容，请参阅以下文章：
> - [AzCopy 入门](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>配置代理设置

若要为 AzCopy 配置代理设置，请设置 `https_proxy` 环境变量。 如果在 Windows 上运行 AzCopy，则 AzCopy 会自动检测代理设置，因此你无需在 Windows 中使用此设置。 如果选择在 Windows 中使用此设置，它将替代自动检测。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | 在命令提示符下，使用： `set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中使用： `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支持需要通过 NTLM 或 Kerberos 进行身份验证的代理。

## <a name="optimize-performance"></a>优化性能

您可以对性能进行基准测试，然后使用命令和环境变量在性能和资源消耗之间找到最佳平衡点。

### <a name="run-benchmark-tests"></a>运行基准测试

可以对特定的 blob 容器运行性能基准测试，以查看一般性能统计信息和标识性能瓶颈。 

> [!NOTE]
> 在当前版本中，此功能仅可用于 Blob 存储容器。

使用以下命令运行性能基准测试。

|    |     |
|--------|-----------|
| **语法** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **示例** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

此命令通过将测试数据上传到指定目标来运行性能基准测试。 在测试完成后，将在内存中生成测试数据，将其上传到目标，然后从目标中删除这些数据。 可以通过使用可选的命令参数来指定要生成的文件数以及要使用的文件大小。

若要查看此命令的详细帮助指南，请键入 `azcopy bench -h`，然后按 ENTER 键。

### <a name="optimize-throughput"></a>优化吞吐量

您可以使用 `cap-mbps` 标志将上限设置为吞吐量数据速率。 例如，以下命令的大写吞吐量为每秒 `10` 兆字节（MB）。

```azcopy
azcopy --cap-mbps 10
```

传输小文件时吞吐量可能会下降。 可以通过设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量来提高吞吐量。 此变量指定可能发生的并发请求数。  

如果计算机的 Cpu 少于5个，则将此变量的值设置为 `32`。 否则，默认值等于16乘以 Cpu 数。 此变量的最大默认值为 `3000`，但您可以手动将此值设置为更高或更低。 

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空，则可以通过查看任何 AzCopy 日志文件的开头来读取正在使用的值。 选择的值以及选择的值将在其中进行报告。

设置此变量之前，建议运行基准测试。 基准测试过程将报告建议的并发值。 或者，如果网络条件和负载不同，请将此变量设置为单词 `AUTO` 而不是特定的数字。 这将导致 AzCopy 始终运行它在基准测试中使用的相同自动优化过程。

### <a name="optimize-memory-use"></a>优化内存使用

设置 `AZCOPY_BUFFER_GB` 环境变量，以指定在下载和上传文件时 AzCopy 要使用的系统内存的最大数量。
以千兆字节（GB）为单位表示此值。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>排查问题

AzCopy 为每个作业创建日志和计划文件。 可以使用日志调查并解决任何潜在问题。 

日志将包含失败状态（`UPLOADFAILED`、`COPYFAILED`和 `DOWNLOADFAILED`）、完整路径和失败的原因。

默认情况下，日志和计划文件位于 Windows 上的 `%USERPROFILE%\.azcopy` 目录中或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目录中，但你可以根据需要更改该位置。

> [!IMPORTANT]
> 向 Microsoft 支持部门提交请求（或解决涉及任何第三方的问题）时，请共享要执行的命令的修正版本。 这可确保 SAS 不会意外地与任何人共享。 可以在日志文件的开头找到经修订的版本。

### <a name="review-the-logs-for-errors"></a>查看日志中的错误

以下命令将从 `04dc9ca9-158f-7945-5933-564021086c79` 日志中获取 `UPLOADFAILED` 状态的所有错误：

**Windows （PowerShell）**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>查看和恢复作业

每个传输操作都将创建一个 AzCopy 作业。 使用以下命令查看作业的历史记录：

```
azcopy jobs list
```

若要查看作业统计信息，请使用以下命令：

```
azcopy jobs show <job-id>
```

若要按状态筛选传输，请使用以下命令：

```
azcopy jobs show <job-id> --with-status=Failed
```

使用以下命令可恢复失败/取消的作业。 此命令使用其标识符和 SAS 令牌，因为出于安全原因，它不是永久性的：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

恢复作业时，AzCopy 会查看作业计划文件。 计划文件列出了在首次创建作业时标识为要处理的所有文件。 恢复作业时，AzCopy 将尝试传输尚未传输的计划文件中列出的所有文件。

## <a name="change-the-location-of-the-plan-and-log-files"></a>更改计划和日志文件的位置

默认情况下，计划和日志文件位于 Windows 的 `%USERPROFILE%\.azcopy` 目录中，或位于 Mac 和 Linux 上的 `$HOME$\.azcopy` 目录中。 你可以更改此位置。

### <a name="change-the-location-of-plan-files"></a>更改计划文件的位置

使用任何这些命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空，则计划文件将写入到默认位置。

### <a name="change-the-location-of-log-files"></a>更改日志文件的位置

使用任何这些命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空，则将日志写入默认位置。

## <a name="change-the-default-log-level"></a>更改默认日志级别

默认情况下，AzCopy 日志级别设置为 `INFO`。 如果要减少日志详细级别以节省磁盘空间，请使用 ``--log-level`` 选项覆盖此设置。 

可用的日志级别有： `NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC`和 `FATAL`。

## <a name="remove-plan-and-log-files"></a>删除计划和日志文件

如果要从本地计算机中删除所有计划和日志文件以节省磁盘空间，请使用 `azcopy jobs clean` 命令。

若要删除仅与一个作业关联的计划和日志文件，请使用 `azcopy jobs rm <job-id>`。 将此示例中的 `<job-id>` 占位符替换为作业的作业 id。


