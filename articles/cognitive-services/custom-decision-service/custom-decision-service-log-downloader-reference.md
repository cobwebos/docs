---
title: LogDownloader - 自定义 AI 决策服务
titlesuffix: Azure Cognitive Services
description: 下载 Azure 自定义 AI 决策服务生成的日志文件。
services: cognitive-services
author: marco-rossi29
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: marossi
ms.openlocfilehash: 8a8f669c33f40fb80dc826ec04203880dee74d82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829984"
---
# <a name="logdownloader"></a>LogDownloader

下载由 Azure 自定义 AI 决策服务生成的日志文件，并生成试验使用的 *.gz* 文件。

## <a name="prerequisites"></a>必备组件

- Python 3：已安装并在你的路径上。 我们建议使用 64 位版本来处理大文件。
- Microsoft/mwt-ds 存储库：[克隆存储库](https://github.com/Microsoft/mwt-ds)。
- *azure-storage-blob* 包：有关安装的详细信息，请转到[用于 Python 的 Microsoft Azure 存储库](https://github.com/Azure/azure-storage-python#option-1-via-pypi)。
- 在 *mwt-ds/DataScience/ds.config* 中输入 Azure 存储连接字符串：请按照 *my_app_id: my_connectionString* 模板进行操作。 可以指定多个 `app_id`。 运行 `LogDownloader.py` 时，如果在 `ds.config` 中找不到输入 `app_id`，则 `LogDownloader.py` 使用 `$Default` 连接字符串。

## <a name="usage"></a>使用情况

转到 `mwt-ds/DataScience` 并使用相关参数运行 `LogDownloader.py`，如以下代码中所述：

```cmd
python LogDownloader.py [-h] -a APP_ID -l LOG_DIR [-s START_DATE]
                        [-e END_DATE] [-o OVERWRITE_MODE] [--dry_run]
                        [--create_gzip] [--delta_mod_t DELTA_MOD_T]
                        [--verbose] [-v VERSION]
```

### <a name="parameters"></a>parameters

| 输入 | 描述 | 默认 |
| --- | --- | --- |
| `-h`、`--help` | 显示帮助消息并退出。 | |
| `-a APP_ID`、`--app_id APP_ID` | 应用程序 ID（即 Azure 存储 blob 容器名称）。 | 需要 |
| `-l LOG_DIR`、`--log_dir LOG_DIR` | 用于下载数据的基目录（创建子文件夹）。  | 需要 |
| `-s START_DATE`、`--start_date START_DATE` | 下载开始日期（包括），采用 *YYYY-MM-DD* 格式。 | `None` |
| `-e END_DATE`、`--end_date END_DATE` | 下载结束日期（包括），采用 *YYYY-MM-DD* 格式。 | `None` |
| `-o OVERWRITE_MODE`、`--overwrite_mode OVERWRITE_MODE` | 要使用的覆盖模式。 | |
| | `0`：从不覆盖；询问用户当前是否使用了 blob。 | 默认 |
| | `1`：当文件具有不同的大小或当前正在使用 blob 时，询问用户如何继续。 | |
| | `2`：始终覆盖；下载当前使用的 blob。 | |
| | `3`：从不覆盖，如果大小较大，则不询问直接追加；下载当前使用的 blob。 | |
| | `4`：从不覆盖，如果大小较大，则不询问直接追加；跳过当前使用的 blob。 | |
| `--dry_run` | 输出本该下载但却未下载的 blob。 | `False` |
| `--create_gzip` | 为 Vowpal Wabbit 创建 *gzip* 文件。 | `False` |
| `--delta_mod_t DELTA_MOD_T` | 用于检测文件当前是否正在使用的时间窗口（以秒为单位）。 | `3600` 秒（`1` 小时） |
| `--verbose` | 输出更多详细信息。 | `False` |
| `-v VERSION`、`--version VERSION` | 要使用的日志程序下载程序版本。 | |
| | `1`：用于未处理的日志（仅为了向后兼容）。 | 已放弃 |
| | `2`：用于已处理的日志。 | 默认 |

### <a name="examples"></a>示例

若要试运行下载 Azure 存储 blob 容器中的所有数据，请使用以下代码：
```cmd
python LogDownloader.py -a your_app_id -l d:\data --dry_run
```

若要使用 `overwrite_mode=4` 仅下载自 2018 年 1 月 1 日以来创建的日志，请使用以下代码：
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4
```

若要创建合并所有下载文件的 *gzip* 文件，请使用以下代码：
```cmd
python LogDownloader.py -a your_app_id -l d:\data -s 2018-1-1 -o 4 --create_gzip
```
