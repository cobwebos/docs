---
title: 将 Powershell 与 Data Lake Store 配合使用的性能优化指南 | Microsoft Docs
description: 有关如何在将 Azure PowerShell 与 Data Lake Store 配合使用时提高性能的建议
services: data-lake-store
documentationcenter: ''
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 7b19972ed4a75ac899a4b78b28ab36ba305a5a64
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>将 PowerShell 与 Azure Data Lake Store 配合使用的性能优化指南

本文列出一些可调整的属性，以便在使用 PowerShell 操作 Data Lake Store 时可获得更佳性能：

## <a name="performance-related-properties"></a>性能相关属性

| 属性            | 默认 | 说明 |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | 使用此参数可以选择用于上传或下载每个文件的并行线程数。 此数字表示可以针对每个文件分配的最大线程数，但根据具体的方案，获得的线程可能少于此数目（例如，如果只是上传 1 KB 的文件，则即使请求了 20 个线程，也只能获得 1 个线程）。  |
| ConcurrentFileCount | 10      | 此参数专门为上传或下载文件夹。 此参数确定可上传或下载的并发文件数。 此数字表示一次性可上传或下载的最大并发文件数，但根据具体的方案，上传或下载的并发文件数可能少于此数目（例如，如果只是上传 2 个文件，则即使请求上传 15 个并发文件，也只能上传 2 个）。 |

**示例**

此命令针对每个文件使用 20 个线程以及 100 个并发文件，将 Azure Data Lake Store 中的文件下载到用户的本地驱动器。

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>如何确定要为这些属性设置的值？

接下来你可能会疑惑如何确定应为性能相关属性提供的值。 请参考下面的指导。

* **步骤 1：确定线程总数** - 首先，请计算要使用的线程总数。 一般指导原则是，应为每个物理核心使用 6 个线程。

        Total thread count = total physical cores * 6

    **示例**

    假设在包含 16 个核心 D14 VM 中运行 PowerShell 命令

        Total thread count = 16 cores * 6 = 96 threads


* **步骤 2：计算 PerFileThreadCount** - 根据文件的大小计算 PerFileThreadCount。 对于小于 2.5 GB 的文件，没有必要更改此参数，因为默认值 10 就已足够。 对于大于 2.5 GB 的文件，应该为前 2.5 GB 使用 10 个线程作为基础，文件大小每增加 256 MB，就多使用 1 个线程。 如果要复制文件大小有很大变化的文件夹，请考虑根据类似的文件大小将这些文件分组。 文件大小有差异可能会导致性能不佳。 如果无法将类似大小的文件分组，应该根据最大文件大小设置 PerFileThreadCount。

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **示例**

    假设有 100 个文件，其大小范围为 1 GB 到 10 GB，可以在公式中使用 10 GB 作为最大文件大小，如下所示。

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **步骤 3：计算 ConcurrentFilecount** - 根据以下公式，使用线程总数和 PerFileThreadCount 计算 ConcurrentFileCount：

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **示例**

    沿用前面使用的示例值

        96 = 40 * ConcurrentFileCount

    **ConcurrentFileCount** 为 **2.4**，舍入为 **2**。

## <a name="further-tuning"></a>进一步调整

由于要处理的文件大小有很大的差异，因此可能需要进一步调整。 如果所有或大多数文件都比较大，接近 10 GB，则可以有效地利用上述计算公式。 但是，如果文件大小差异很大，许多文件都比较小，则可以减小 PerFileThreadCount。 减小 PerFileThreadCount 即可增大 ConcurrentFileCount。 假设大多数文件都比较小，在 5 GB 以内，则可以重新计算：

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

所以 ConcurrentFileCount 是 96/20，即 4.8，舍入为 4。

可以根据文件大小的分布，通过调大或调小 **PerFileThreadCount** 来继续调整这些设置。

### <a name="limitation"></a>限制

* **文件数小于 ConcurrentFileCount**：如果要上传的文件数小于计算得出的 **ConcurrentFileCount** ，应该减小 **ConcurrentFileCount** ，使其等于文件数。 可以使用所有剩余线程来增大 **PerFileThreadCount**。

* **线程过多**：如果在不增加群集大小的情况下大幅增加线程计数，会面临性能下降的风险。 在 CPU 上执行上下文切换时，可能会出现资源争用的问题。

* **并发性不足**：如果并发性不足，可能表示群集太小。 可在群集中增加节点数目，这样可以提高并发性。

* **限制错误**：并发性过高时，可能会出现限制错误。 如果看到限制错误，应该降低并发性，或者与我们联系。

## <a name="next-steps"></a>后续步骤
* [使用 Azure Data Lake Store 满足大数据要求](data-lake-store-data-scenarios.md) 
* [保护 Data Lake Store 中的数据](data-lake-store-secure-data.md)
* [配合使用 Azure Data Lake Analytic 和 Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [配合使用 Azure HDInsight 和 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

