---
title: azcopy bench | Microsoft Docs
description: 本文提供有关 azcopy bench 命令的参考信息。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282001"
---
# <a name="azcopy-benchmark"></a>azcopy benchmark

通过向或从指定的目标上上传或下载测试数据，来运行性能基准测试。 对于上载，会自动生成测试数据。

基准命令与 "copy" 运行相同的过程，只不过： 

  - 基准测试只需使用源和目标参数，而无需使用源和目标参数。 这是要上传到或从中下载的 blob 容器、Azure 文件共享或 Azure Data Lake Storage Gen2 文件系统。

  - "Mode" 参数描述 AzCopy 应测试是否应上载到给定目标或从给定目标下载。 有效值为 "上载" 和 "下载"。 默认值为 "上传"。

  - 对于上传基准测试，负载通过命令行参数描述，这些参数控制自动生成的文件数量以及文件的数量。 生成过程完全在内存中进行。 不使用磁盘。

  - 对于下载，负载包含源中已存在的任何文件。 （有关如何根据需要生成测试文件的示例，请参阅下面的示例）。
  
  - 仅支持几个可用于 copy 命令的可选参数。
  
  - 将会度量和报告其他诊断信息。
  
  - 对于上载，默认行为是在测试运行结束时删除传输的数据。  对于下载，数据永远不会保存在本地。

基准模式会自行调整为能够提供最大吞吐量的并行 TCP 连接数。 它会在结尾处显示该数字。 若要防止自动优化，请将 AZCOPY_CONCURRENCY_VALUE 环境变量设置为特定的连接数。 

支持所有常用的身份验证类型。 不过，最方便的基准测试方法通常是使用 SAS 令牌创建一个空容器并使用 SAS 身份验证。 （下载模式要求目标容器中存在一组测试数据。）

## <a name="examples"></a>示例

```azcopy
azcopy benchmark [destination] [flags]
```

使用默认参数运行基准测试（适用于对高达 1 Gbps 的网络进行基准测试）：

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
运行一个上传 100 个文件的基准测试，每个文件的大小为 2 GiB：（适用于在快速网络（例如 10 Gbps 的网络）上进行基准测试）：

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
运行基准测试，但使用 50,000 个文件，每个文件的大小为 8 MiB，并计算其 MD5 哈希值（使用的方式与 `--put-md5` 标记在 copy 命令中使用的相同）。 进行基准测试时使用 `--put-md5` 的目的是测试 MD5 计算是否会影响所选文件计数和大小的吞吐量：

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

运行从目标下载现有文件的基准测试

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

运行不会删除传输的文件的上传。 （这些文件可用作下载测试的负载）

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>选项

**--blob-type** 字符串  定义目标中的 Blob 类型。 用于允许对不同的 Blob 类型进行基准测试。 与 copy 命令中具有相同名称的参数相同（默认为“Detect”）。

**--block-size-mb** 浮点数  使用此块大小（以 MiB 为单位）。 默认值根据文件大小自动计算。 允许使用小数部分，例如0.25。 与 copy 命令中具有相同名称的参数相同。

**--检查长度** 传输后检查目标上的文件的长度。 如果源与目标之间不匹配，则将传输标记为失败。 （默认值为 true）

**--delete-test-data**  如果为 true，则会在基准测试运行结束时删除基准数据。  如果要将数据保留在目标上，则将其设置为 false-例如，用于在基准模式之外进行手动测试（默认值为 true）。

**--文件计数**uint。  要使用的自动生成的数据文件数（默认值为100）。

**--帮助** 有关工作台的帮助

**--log-level** 字符串 定义日志文件的日志详细程度，可用级别：INFO（所有请求/响应）、WARNING（响应缓慢）、ERROR（仅限失败的请求）和 NONE（无输出日志）。 （默认值为“INFO”）

**--mode** String 用于定义 Azcopy 是否应测试此目标的上传或下载。 有效值为 "上载" 和 "下载"。 默认选项为 "上传"。 （默认值为 "上载"）

**--文件夹值**uint 大于0时，创建文件夹来划分数据。

**--put-md5**  创建每个文件的 MD5 哈希，并将该哈希另存为目标 Blob/文件的 Content-MD5 属性。 （默认不会创建哈希。）与 copy 命令中具有相同名称的参数相同。

**--** 每个自动生成的数据文件的每个文件大小的字符串大小。 必须是一个数字，后跟 K、M 或 G。 12k 或 200G（默认为“250M”）。

## <a name="options-inherited-from-parent-commands"></a>从父命令继承的选项

**--cap-mbps float** 以兆位/秒为单位限制传输速率。 瞬间吞吐量可能与上限略有不同。 如果此选项设置为零，或者省略，则吞吐量不受限制。

**--output-type** 字符串  命令输出的格式。 选项包括：text、json。 默认值为“text”。 （默认值为“text”）。

--trusted-microsoft-suffixes string 指定可向其中发送 Azure Active Directory 登录令牌的其他域后缀。  默认值为“.core.windows.net;.core.chinacloudapi.cn;.core.cloudapi.de;.core.usgovcloudapi.net” 。 此处列出的任何内容都会添加到默认值。 为安全，你只应在此处放置 Microsoft Azure 域。 用分号分隔多个条目。


## <a name="see-also"></a>另请参阅

- [azcopy](storage-ref-azcopy.md)
