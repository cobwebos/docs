---
title: azcopy bench | Microsoft Docs
description: 本文提供有关 azcopy bench 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 40ff6c6c76e255945681e678ef296ffcf9978f61
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485177"
---
# <a name="azcopy-benchmark"></a>azcopy 基准

通过将测试数据上传到指定目标来运行性能基准测试。 测试数据是自动生成的。

基准测试命令运行与“copy”命令相同的上传过程，区别在于：

  - 没有源参数。  此命令只需要目标 URL。 
  
  - 负载通过命令行参数来描述，这些参数控制自动生成的文件的数量和大小。 生成过程完全在内存中进行。 不使用磁盘。
  
  - 仅支持几个可用于 copy 命令的可选参数。
  
  - 将会度量和报告其他诊断信息。
  
  - 默认情况下，在测试运行结束时会删除已传输的数据。

基准模式会自行调整为能够提供最大吞吐量的并行 TCP 连接数。 它会在结尾处显示该数字。 若要防止自动优化，请将 AZCOPY_CONCURRENCY_VALUE 环境变量设置为特定数量的连接。

支持所有常用的身份验证类型。 但通常情况下，进行基准测试最方便的方法是创建一个包含 SAS 令牌的空容器并使用 SAS 身份验证。

## <a name="examples"></a>示例

```azcopy
azcopy benchmark [destination] [flags]
```

使用默认参数运行基准测试（适用于对高达 1 Gbps 的网络进行基准测试）：

- azcopy 工作台 "https：//[account] .../[container]？ <SAS> "

运行上传100个文件的基准测试，每两个 GiB 大小：（适用于快速网络上的基准测试，如 10 Gbps）：

- azcopy 工作台 "https：//[account] .../[container]？ <SAS> "--file-count 100--每文件大小的2G

运行基准测试，但使用50000文件，其中每8个 MiB 的大小和计算其 MD5 哈希值（与在 `--put-md5` 复制命令中标记相同的方式相同）。 `--put-md5`基准测试的目的是测试 MD5 计算是否会影响选定文件计数和大小的吞吐量：

- azcopy 工作台 "https：//[account] .../[container]？ <SAS> " --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>选项

**--blob 类型**字符串定义目标中的 blob 类型。 用于允许对不同的 Blob 类型进行基准测试。 与 copy 命令中具有相同名称的参数相同（默认为“Detect”）。

**--block-size-mb** 浮点数  使用此块大小（以 MiB 为单位）。 默认值根据文件大小自动计算。 允许使用小数，例如 0.25。 与 copy 命令中具有相同名称的参数相同。

**--delete-test-data**  如果为 true，则会在基准测试运行结束时删除基准数据。  将它设置为 false 的前提是需在目标中保留数据 - 例如，将它用于基准模式之外的手动测试（默认为 true）。

**--file-count** uint  要使用的自动生成的数据文件数（默认为 100）。

**-h, --help**  bench 命令的帮助

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为“INFO”）

**--put-md5**  创建每个文件的 MD5 哈希，并将该哈希另存为目标 Blob/文件的 Content-MD5 属性。 （默认情况下不创建哈希。）与复制命令中的相同命名参数相同。

**--size-per-file** string   每个自动生成的数据文件的大小。 必须是一个数字，后面紧跟 K、M 或 G。例如， 12k 或 200G（默认为“250M”）。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32** 以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--输出类型**字符串格式的命令输出。 选项包括：text、json。 默认值为“text”。 （默认值为“text”）。

**--trusted-microsoft 后缀**string 指定可以在其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
