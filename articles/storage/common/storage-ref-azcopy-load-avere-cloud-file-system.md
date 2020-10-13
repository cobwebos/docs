---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: 本文提供有关 azcopy load clfs 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8f0a8903f90b134c35d9adb35a493d989d414b56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294028"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

将本地数据传输到容器中，并以 Microsoft 的 Avere Cloud FileSystem (CLFS) 格式存储。

## <a name="synopsis"></a>摘要

load 命令将数据复制到 Azure Blob 存储容器中，然后以 Microsoft 的 Avere Cloud FileSystem (CLFS) 格式存储这些数据。 专有的 CLFS 格式由 Azure HPC 缓存和 Avere vFXT for Azure 产品使用。

若要利用此命令，请通过以下方式安装必需的扩展：pip3 install clfsload~=1.0.23。 请确保 CLFSLoad.py 在你的路径中。 有关此步骤的详细信息，请访问 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs)。

此命令是用于将现有数据移动到云存储以供特定的 Microsoft 高性能计算缓存产品使用的简单选项。 

由于这些产品使用专有的云文件系统格式来管理数据，因此无法通过本机复制命令来加载这些数据。 

相反，必须通过缓存产品本身或通过此 load 命令加载数据，该命令使用正确的专有格式。
通过此命令可在不使用缓存的情况下传输数据。 例如，在不增加缓存负载的情况下预填充存储或将文件添加到工作集中。

目标为空的 Azure 存储容器。 传输完成后，目标容器可用于 Azure HPC 缓存实例，或 Avere vFXT for Azure 群集。

> [!NOTE] 
> 这是 load 命令的预览版。 请报告 AzCopy Github 存储库中的任何问题。

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>相关概念性文章

- [AzCopy 入门](storage-use-azcopy-v10.md)
- [使用 AzCopy 和 Blob 存储传输数据](storage-use-azcopy-blobs.md)
- [使用 AzCopy 和文件存储传输数据](storage-use-azcopy-files.md)
- [对 AzCopy 进行配置、优化和故障排除](storage-use-azcopy-configure.md)

## <a name="examples"></a>示例

使用 CLFS 格式的 SAS 将整个目录加载到容器：

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>选项

**--compression-type** 字符串 - 指定用于传输的压缩类型。 可用值为：`DISABLED`、`LZ4`。 （默认值为 `LZ4`）

**--help** - 获取 `azcopy load clfs` 命令的帮助。

**--log-level** 字符串 - 定义日志文件的日志详细程度，可用级别：`DEBUG`、`INFO`、`WARNING`、`ERROR`。 （默认值为 `INFO`）

**--max-errors uint32** - 指定允许的最大传输失败数。 如果出现足够多的错误，请立即停止作业。

**--new-session** - 启动一个新作业，而不是继续执行跟踪信息保存在 `--state-path` 的现有作业。 （默认值为 true）

**--preserve-hardlinks** - 保持硬链接关系。

**--state-path** 字符串 - 作业状态跟踪所需的本地目录路径。 此路径必须指向现有目录，才能恢复作业。 对于新作业，它必须为空。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps 浮点数|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   | 指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
