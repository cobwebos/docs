---
title: 对配合 Azure 存储使用的 AzCopy 进行配置、优化和故障排除 | Microsoft Docs
description: 对 AzCopy 进行配置、优化和故障排除
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: c3ee0f335741c171c3a7ee1df3eea6dea9c4b728
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176152"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>对 AzCopy 进行配置、优化和故障排除

AzCopy 是一个命令行实用工具，可用于向/从存储帐户复制 Blob 或文件。 本文将帮助你执行高级配置任务，以排查使用 AzCopy 时可能出现的问题。

> [!NOTE]
> 如果你正在寻找 AzCopy 入门内容，请参阅以下文章：
> - [AzCopy 入门](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 Bucket 传输数据](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>配置代理设置

若要为 AzCopy 配置代理设置，请设置 `https_proxy` 环境变量。 如果在 Windows 中运行 AzCopy，AzCopy 会自动检测代理设置，因此你无需在 Windows 中使用此设置。 如果在 Windows 中选择使用此设置，此设置会替代自动检测。

| 操作系统 | Command  |
|--------|-----------|
| **Windows** | 在命令提示符处使用 `set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中使用 `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy 目前不支持要求通过 NTLM 或 Kerberos 进行身份验证的代理。

## <a name="optimize-performance"></a>优化性能

可以指定性能基准，然后使用命令和环境变量在性能与资源消耗量之间找到最佳的平衡。

本部分将帮助你执行以下优化任务：

> [!div class="checklist"]
> * 运行基准测试
> * 优化吞吐量
> * 优化内存用量 
> * 优化文件同步

### <a name="run-benchmark-tests"></a>运行基准测试

可对特定的 Blob 容器运行性能基准测试，以查看一般性能统计信息和识别性能瓶颈。 

使用以下命令运行性能基准测试。

|    |     |
|--------|-----------|
| **语法** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **示例** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 此示例将路径参数括在单引号 ('') 内。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

此命令通过将测试数据上传到指定的目标来运行性能基准测试。 测试数据将在内存中生成、上传到目标，并在完成测试后从目标中删除。 可以使用可选的命令参数来指定要生成的文件数以及文件的大小。

有关详细参考文档，请参阅 [azcopy bench](storage-ref-azcopy-bench.md)。

若要查看此命令的详细帮助指导，请键入 `azcopy bench -h` 并按 ENTER 键。

### <a name="optimize-throughput"></a>优化吞吐量

可以在命令中使用 `cap-mbps` 标志来设置吞吐量数据速率的上限。 例如，以下命令将恢复作业，并将每秒的`10`上限吞吐量恢复为兆字节（mb）。 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

传输小型文件时，吞吐量可能会下降。 可以设置 `AZCOPY_CONCURRENCY_VALUE` 环境变量来提高吞吐量。 此变量指定可发生的并发请求数。  

如果计算机中的 CPU 少于 5 个，则此变量的值将设置为 `32`。 否则，默认值等于 16 乘以 CPU 数。 此变量的最大默认值为 `3000`，但可以手动增大或减小此值。 

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，你可以通过查看任何 AzCopy 日志文件的开头部分来读取所用的值。 日志中会报告所选的值以及选择该值的原因。

在设置此变量之前，我们建议运行基准测试。 基准测试过程将报告建议的并发值。 或者，如果网络条件和有效负载不同，请将此变量设置为单词 `AUTO` 而不是特定的数字。 这样，AzCopy 始终会运行它在基准测试中使用的相同自动优化过程。

### <a name="optimize-memory-use"></a>优化内存用量

设置 `AZCOPY_BUFFER_GB` 环境变量，以指定 AzCopy 在下载和上传文件时要使用的最大系统内存量。
请以 GB 表示此值。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>优化文件同步

[sync](storage-ref-azcopy-sync.md) 命令标识目标中的所有文件，然后在开始同步操作前比较文件名和上次修改的时间戳。 如果有大量文件，则可通过消除此前期处理来提高性能。 

若要实现此目的，请改用 [azcopy copy](storage-ref-azcopy-copy.md) 命令，并将 `--overwrite` 标志设置为 `ifSourceNewer`。 AzCopy 会在文件复制时比较文件，而不执行任何预先扫描和比较。 如果有大量文件要比较，这会提供性能优势。

[azcopy copy](storage-ref-azcopy-copy.md) 命令不会从目标中删除文件，因此，若要在源中不存在文件时删除目标中的文件，请使用 [azcopy sync](storage-ref-azcopy-sync.md) 命令，并将 `--delete-destination` 标志设置为 `true` 或 `prompt` 值。 

## <a name="troubleshoot-issues"></a>排查问题

AzCopy 为每个作业创建日志和计划文件。 可以使用日志调查并解决任何潜在问题。 

日志将包含失败状态（`UPLOADFAILED`、`COPYFAILED` 和 `DOWNLOADFAILED`）、完整路径和失败的原因。

默认情况下，日志和计划文件位于 Windows 上的 `%USERPROFILE%\.azcopy` 目录中或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目录中，但可根据需要更改此位置。

相关错误不一定是出现在文件中的第一个错误。 对于网络错误、超时和服务器繁忙错误等错误，AzCopy 将重试最多20次，重试过程通常会成功。  你看到的第一个错误可能是已成功重试的错误。  因此，请查找附近、 `UPLOADFAILED` `COPYFAILED`或`DOWNLOADFAILED`附近的错误，而不是查看文件中的第一个错误。 

> [!IMPORTANT]
> 向 Microsoft 支持部门提交请求（或解决涉及任何第三方的问题）时，请共享要执行的命令的修正版本。 这可以确保不会意外地与任何人共享 SAS。 可以在日志文件的开头找到经修订的版本。

### <a name="review-the-logs-for-errors"></a>查看日志中的错误

以下命令从 `04dc9ca9-158f-7945-5933-564021086c79` 日志中获取 `UPLOADFAILED` 状态的所有错误：

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

使用以下命令恢复失败/取消的作业。 此命令使用该作业的标识符以及 SAS 令牌，因为它不是持久性的（出于安全方面的原因）：

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> 用单引号 ('') 将路径参数（如 SAS 令牌）括起来。 在除 Windows 命令 Shell (cmd.exe) 以外的所有命令 shell 中，都请使用单引号。 如果使用 Windows 命令 Shell (cmd.exe)，请用双引号 ("") 而不是单引号 ('') 括住路径参数。

恢复某个作业时，AzCopy 会查看作业计划文件。 该计划文件列出了首次创建该作业时标识为待处理的所有文件。 恢复某个作业时，AzCopy 会尝试传输计划文件中列出的且尚未传输的所有文件。

## <a name="change-the-location-of-the-plan-and-log-files"></a>更改计划和日志文件的位置

默认情况下，计划和日志文件位于 Windows 上的 `%USERPROFILE%\.azcopy` 目录中，或 Mac 和 Linux 上的 `$HOME$\.azcopy` 目录中。 可以更改此位置。

### <a name="change-the-location-of-plan-files"></a>更改计划文件的位置

使用以下任何命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，则计划文件将写入默认位置。

### <a name="change-the-location-of-log-files"></a>更改日志文件的位置

使用以下任何命令。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空白，则日志将写入默认位置。

## <a name="change-the-default-log-level"></a>更改默认日志级别

AzCopy 日志级别默认设置为 `INFO`。 若要降低日志详细程度以节省磁盘空间，请使用 ``--log-level`` 选项覆盖此设置。 

可用的日志级别：`NONE`、`DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC` 和 `FATAL`。

## <a name="remove-plan-and-log-files"></a>删除计划和日志文件

若要从本地计算机中删除所有计划和日志文件以节省磁盘空间，请使用 `azcopy jobs clean` 命令。

若要删除只与一个作业关联的计划和日志文件，请使用 `azcopy jobs rm <job-id>`。 请将此示例中的 `<job-id>` 占位符替换为作业的作业 ID。


