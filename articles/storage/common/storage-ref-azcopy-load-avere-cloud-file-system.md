---
title: azcopy load clfs |Microsoft Docs
titleSuffix: Azure Storage
description: 本文提供 azcopy load clfs 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8f0a8903f90b134c35d9adb35a493d989d414b56
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294028"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

将本地数据传输到容器，并将其存储在 Microsoft 的 Avere Cloud FileSystem （CLFS）格式中。

## <a name="synopsis"></a>摘要

Load 命令将数据复制到 Azure Blob 存储容器，然后将该数据存储在 Microsoft 的 Avere Cloud FileSystem （CLFS）格式中。 适用于 Azure 产品的 Azure HPC 缓存和 Avere vFXT 使用专有 CLFS 格式。

若要利用此命令，请通过以下方式安装必需的扩展： pip3 install clfsload ~ = 1.0.23。 请确保 CLFSLoad.py 在您的路径中。 有关此步骤的详细信息，请访问 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) 。

此命令是一个简单的选项，用于将现有数据移至云存储，以与特定的 Microsoft 高性能计算缓存产品一起使用。 

由于这些产品使用专用的云文件系统格式来管理数据，无法通过 "本机复制" 命令加载这些数据。 

相反，必须通过缓存产品本身或通过此 load 命令加载数据，该命令使用正确的专用格式。
此命令可让你在不使用缓存的情况下传输数据。 例如，用于预先填充存储或将文件添加到工作集，而无需增加缓存负载。

目标为空的 Azure 存储容器。 传输完成后，目标容器可用于 azure HPC 缓存实例，或用于 Azure 群集的 Avere vFXT。

> [!NOTE] 
> 这是 load 命令的预览版本。 请报告 AzCopy Github 存储库中的任何问题。

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

**--compression 类型**字符串指定用于传输的压缩类型。 可用值为： `DISABLED` 、 `LZ4` 。 （默认值 `LZ4` ）

**--** 命令的帮助帮助 `azcopy load clfs` 。

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：，， `DEBUG` `INFO` `WARNING` ， `ERROR` 。 （默认值 `INFO` ）

**--最大错误**uint32 指定可容忍的传输失败最大次数。 如果发生了足够的错误，请立即停止该作业。

**--新建-会话**  启动新的作业，而不是将跟踪信息保留在其中的现有作业 `--state-path` 。 （默认值为 true）

**--hardlinks**   保留硬链接关系。

**--path**字符串需要本地目录的路径才能跟踪作业状态。 路径必须指向现有目录，才能恢复作业。 新作业必须为空。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

|选项|说明|
|---|---|
|--cap-mbps float|以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。|
|--output-type string|命令输出的格式。 选项包括：text、json。 默认值为“text”。|
|--trusted-microsoft-suffixes 字符串   | 指定可在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。|

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
