---
title: 配置、 优化和故障排除与 Azure 存储 AzCopy |Microsoft Docs
description: 配置、 优化和故障排除 AzCopy。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a160591ef0a47eed097ce8db373878f32965de9b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247119"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>配置、 优化和故障排除 AzCopy

AzCopy 是一个命令行实用工具，可用于向 / 从存储帐户复制 blob 或文件。 本文可帮助您执行高级的配置任务，并可帮助你使用 AzCopy 时，可能会出现的问题进行疑难解答。

> [!NOTE]
> 如果您正在寻找的内容来帮助你开始使用 AzCopy，请参阅以下文章之一：
> - [开始使用 AzCopy](storage-use-azcopy-v10.md)
> - [使用 AzCopy 和 blob 存储传输数据](storage-use-azcopy-blobs.md)
> - [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
> - [使用 AzCopy 和 Amazon S3 存储桶传输数据](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>配置代理设置

若要配置适用于 AzCopy 的代理设置，设置`https_proxy`环境变量。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

目前，AzCopy 不支持需要使用 NTLM 或 Kerberos 进行身份验证的代理。

## <a name="optimize-throughput"></a>优化吞吐量

设置`AZCOPY_CONCURRENCY_VALUE`环境变量配置的并发请求数以及控制吞吐量性能和资源消耗。 如果您的计算机具有少于 5 个 Cpu，则此变量的值设置为`32`。 否则，默认值等于 16 的 Cpu 数的乘积。 此变量的最大的默认值是`300`，但可以手动设置此值更高版本或更低。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

使用`azcopy env`检查此变量的当前值。  如果值为空，则`AZCOPY_CONCURRENCY_VALUE`变量设置的默认值为`300`。

## <a name="change-the-location-of-the-log-files"></a>更改日志文件的位置

默认情况下，日志文件位于`%USERPROFILE\\.azcopy`文件夹上 Windows，或在`$HOME\\.azcopy`Mac 和 Linux 上的文件夹。 如果你需要通过使用以下命令，可以更改此位置。

| 操作系统 | 命令  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

使用`azcopy env`检查此变量的当前值。 如果值为空，然后日志会写入到默认位置。

## <a name="change-the-default-log-level"></a>更改默认日志级别

默认情况下，AzCopy 日志级别设置为`INFO`。 如果你想要减少日志详细程度，以节省磁盘空间，使用来覆盖此设置``--log-level``选项。 

可用日志级别为： `DEBUG`， `INFO`， `WARNING`， `ERROR`， `PANIC`，并`FATAL`。

## <a name="troubleshoot-issues"></a>排查问题

AzCopy 将创建每个作业日志和计划的文件。 可以使用日志调查并解决任何潜在问题。 

日志将包含失败的状态 (`UPLOADFAILED`， `COPYFAILED`，和`DOWNLOADFAILED`)，完整路径和失败的原因。

默认情况下的日志和计划文件位于`%USERPROFILE\\.azcopy`在 Windows 上的文件夹或`$HOME\\.azcopy`Mac 和 Linux 上的文件夹。

> [!IMPORTANT]
> 提交到 Microsoft 支持部门 （或故障排除问题涉及任何第三方），请求时共享你想要执行的命令经过修订的版本。 这可以确保不会意外地与任何人共享 SAS。 可以在日志文件的开头找到经修订的版本。

### <a name="review-the-logs-for-errors"></a>查看日志中的错误

以下命令将获取所有的错误`UPLOADFAILED`状态从`04dc9ca9-158f-7945-5933-564021086c79`日志：

**Windows**

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

当恢复作业时，AzCopy 会查看的作业计划文件。 计划文件列出了所有的文件已标识用于处理首次创建作业时。 当恢复作业时，AzCopy 将尝试传输的所有文件中没有已传输的计划文件列出。