---
title: 配置、优化 AzCopy 和排查 Azure 存储空间问题 |Microsoft Docs
description: 配置、优化 AzCopy 并对其进行故障排除。
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 3843eb2e906e3fb8d390e509e17117b7849ac220
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244702"
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

| 操作系统 | Command  |
|--------|-----------|
| **Windows** | 在命令提示符处使用 `set https_proxy=<proxy IP>:<proxy port>`<br> 在 PowerShell 中使用 `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支持需要通过 NTLM 或 Kerberos 进行身份验证的代理。

## <a name="optimize-throughput"></a>优化吞吐量

你可以使用 `cap-mbps` 标志将上限设置为吞吐量数据速率。 例如，以下命令的大写吞吐量为每秒 `10` 兆位（MB）。

```azcopy
azcopy cap-mbps 10
```

传输小文件时吞吐量可能会下降。 可以通过设置 @no__t 环境变量来提高吞吐量。 此变量指定可能发生的并发请求数。  如果计算机的 Cpu 少于5个，则将此变量的值设置为 `32`。 否则，默认值等于16乘以 Cpu 数。 此变量的最大默认值为 `300`，但你可以手动将此值设置为更高或更低。

| 操作系统 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用 `azcopy env` 检查此变量的当前值。  如果该值为空，则 @no__t 的变量设置为 `300` 的默认值。

## <a name="change-the-location-of-the-log-files"></a>更改日志文件的位置

默认情况下，日志文件位于 Windows 的 `%USERPROFILE%\.azcopy` 目录中，或位于 Mac 和 Linux 上的 @no__t 目录中。 如果需要使用这些命令，可以更改此位置。

| 操作系统 | Command  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用 `azcopy env` 检查此变量的当前值。 如果该值为空，则将日志写入默认位置。

## <a name="change-the-default-log-level"></a>更改默认日志级别

默认情况下，AzCopy 日志级别设置为 `INFO`。 如果要减少日志详细级别以节省磁盘空间，请使用 ``--log-level`` 选项覆盖此设置。 

可用的日志级别为： `DEBUG`、`INFO`、`WARNING`、`ERROR`、`PANIC` 和 @no__t。

## <a name="troubleshoot-issues"></a>排查问题

AzCopy 为每个作业创建日志和计划文件。 可以使用日志调查并解决任何潜在问题。 

日志将包含失败状态（`UPLOADFAILED`、`COPYFAILED` 和 `DOWNLOADFAILED`）、完整路径和失败的原因。

默认情况下，日志和计划文件位于 Windows 上的 `%USERPROFILE\\.azcopy` 目录中或 Mac 和 Linux 上的 `$HOME\\.azcopy` 目录。

> [!IMPORTANT]
> 向 Microsoft 支持部门提交请求（或解决涉及任何第三方的问题）时，请共享要执行的命令的修正版本。 这可以确保不会意外地与任何人共享 SAS。 可以在日志文件的开头找到经修订的版本。

### <a name="review-the-logs-for-errors"></a>查看日志中的错误

以下命令将从 `04dc9ca9-158f-7945-5933-564021086c79` 日志中获取 @no__t 状态的所有错误：

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

恢复作业时，AzCopy 会查看作业计划文件。 计划文件列出了在首次创建作业时标识为要处理的所有文件。 恢复作业时，AzCopy 将尝试传输尚未传输的计划文件中列出的所有文件。
