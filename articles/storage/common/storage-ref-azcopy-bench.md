---
title: azcopy 工作台 |Microsoft Docs
description: 本文提供 azcopy 工作台命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518299"
---
# <a name="azcopy-bench"></a>azcopy 工作台

通过将测试数据上传到指定的目标来运行性能基准测试。 自动生成测试数据。

基准命令与 "copy" 运行相同的上载过程，只不过：

  - 没有源参数。  命令只需要目标 URL。 在当前版本中，此目标 URL 必须引用 blob 容器。
  
  - 负载通过命令行参数来描述，这些参数控制自动生成的文件的数量和大小。 生成过程完全在内存中进行。 不使用磁盘。
  
  - 仅支持可用于复制命令的几个可选参数。
  
  - 还会测量和报告其他诊断信息。
  
  - 默认情况下，在测试运行结束时将删除已传输的数据。

基准模式将自动调整为提供最大吞吐量的并行 TCP 连接数。 它将在结尾处显示该数字。 若要防止自动优化，请将 AZCOPY_CONCURRENCY_VALUE 环境变量设置为特定数量的连接。

支持所有常用的身份验证类型。 然而，最方便的基准测试方法通常是使用 SAS 令牌创建一个空容器并使用 SAS 身份验证。

## <a name="examples"></a>示例

```azcopy
azcopy bench [destination] [flags]
```

使用默认参数运行基准测试（适用于基准测试，最大为 1 Gbps）：

- azcopy 工作台 "https：//[account]. .net/[容器]？<SAS> "

运行上传100个文件的基准测试，每2个 GiB 大小：（适用于快速网络上的基准测试，如 10 Gbps）：

- azcopy 工作台 "https：//[account]. .net/[容器]？<SAS> " --file-count 100--每文件大小的2G

与上面的方法相同，但使用的是50000文件，每8个 MiB 的大小和计算其 MD5 哈希（与--put-MD5 标志在复制命令中的作用相同）。 在进行基准测试时，--put 的目的是测试 MD5 计算是否会影响选定文件计数和大小的吞吐量：

- azcopy 工作台 "https：//[account]. .net/[容器]？<SAS> " --文件计数 50000--每文件大小8分钟--put-md5

## <a name="options"></a>选项

**--blob 类型**字符串定义目标中的 blob 类型。 用于允许基准不同的 blob 类型。 与复制命令中的相同命名参数相同（默认值为 "检测"）。

**--块大小-mb** float 使用此块大小（在 MiB 中指定）。 默认值是根据文件大小自动计算的。 允许使用小数，例如0.25。 与复制命令中的相同命名参数相同。

**--删除-测试-数据** 如果为 true，则基准数据将在基准运行结束时删除。  如果要将数据保留在目标位置，则将其设置为 false-例如，用于在基准模式之外进行手动测试（默认值为 true）。

**--file count** uint 要使用的自动生成的数据文件数（默认值为100）。

**-h、--help** 有关工作台的帮助

**--日志级别**字符串定义日志文件的日志详细级别，可用级别：信息（所有请求/响应）、警告（响应缓慢）、错误（仅失败的请求）和 NONE （无输出日志）。 （默认值为 "INFO"）

**--put-md5** 创建每个文件的 MD5 哈希，并将哈希保存为目标 blob/文件的 Content-MD5 属性。 （默认情况下不创建哈希。）与复制命令中的相同命名参数相同。

**--** 每个自动生成的数据文件的每个文件大小的字符串大小。 必须是一个数字，后跟 K、M 或 G。 12k 或200G （默认值为 "250M"）。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps uint32** 以兆位/秒为单位限制传输速率。 每分钟的吞吐量可能与 cap 略有不同。 如果将此选项设置为零，或省略此选项，则不会限制吞吐量。

**--输出类型**字符串格式的命令输出。 选项包括：文本、json。 默认值为 "text"。 （默认值为 "text"）。

## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
