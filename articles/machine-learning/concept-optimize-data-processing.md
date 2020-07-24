---
title: 优化数据处理
titleSuffix: Azure Machine Learning
description: 了解优化数据处理速度的最佳实践，以及 Azure 机器学习在大规模数据处理方面支持哪些集成。
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: b0badd92a3156f76f99bf1f48fca2093a2bca2f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012631"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>使用 Azure 机器学习优化数据处理

本文介绍可帮助优化本地和大规模的数据处理速度的最佳实践。

Azure 机器学习与用于数据处理的开放源代码包和框架进行了集成。 通过使用这些集成并应用本文中的最佳做法建议，可以提高本地和大规模数据处理的速度。

## <a name="parquet-and-csv-file-formats"></a>Parquet 和 CSV 文件格式

逗号分隔值 (csv) 文件是用于数据处理的常见文件格式。 但是，建议将 parquet 文件格式用于机器学习任务。

[Parquet 文件](https://parquet.apache.org/)以二进制纵栏格式存储数据。 如果需要将数据拆分为多个文件，此格式会很有用。 此外，使用此格式可将机器学习试验的相关字段设定为目标。 可以通过只选择训练 ML 模型时所必需的列来减少数据加载量，而不必读取全部 20 GB 的数据文件。 还可以对 Parquet 文件进行压缩，以最大程度减小所需的处理能力和占用的空间。

CSV 文件通常用于导入和导出数据，因为它们易于在 Excel 中进行编辑和读取。 CSV 中的数据采用基于行的格式以字符串形式存储，并且可以对这类文件进行压缩以减少数据传输负载。 未经压缩的 CSV 大小可增加约 2-10 倍，经过压缩的 CSV 甚至可以增加更多。 因此，内存中的 5-GB CSV 可扩展至超过计算机具有的 8 GB RAM。 这种压缩行为可能会增加数据传输延迟，在需要处理大量数据的情况下，此行为可能产生不好的效果。 

## <a name="pandas-dataframe"></a>Pandas 数据帧

[Pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html)常见于数据操作和分析。 `Pandas` 适用于小于 1 GB 的数据大小，但当文件大小超过 1 GB 时，`pandas` 数据帧的处理时间会变慢。 这种减慢是因为存储中的数据大小不同于数据帧中的数据大小。 例如，CSV 文件中的数据在数据帧中最多可以扩展到 10 倍，因此，1 GB 的 CSV 文件在数据帧中可能会变成 10 GB。

`Pandas` 是单线程的，也就是说，在单个 CPU 上每次只能执行一个操作。 借助使用分布式后端来包装 `Pandas` 的 [Modin](https://modin.readthedocs.io/en/latest/) 等包，可以很容易地让工作负载在单个 Azure 机器学习计算实例上的多个虚拟 CPU 中并行运行。

要使用 `Modin` 和 [Dask](https://dask.org) 并行运行任务，只需将这行代码 `import pandas as pd` 更改为 `import modin.pandas as pd`。

## <a name="dataframe-out-of-memory-error"></a>数据帧：内存不足错误 

通常，当数据帧扩展至超过计算机上的可用 RAM 时，会发生内存不足错误。 此概念还适用于分布式框架，如 `Modin` 或 `Dask`。  也就是说，你的操作尝试在群集的每个节点上的内存中加载数据帧，但没有足够的 RAM 来执行此操作。

一个解决方案是增加 RAM 以适应内存中的数据帧。 建议将计算大小和处理能力设置为包含 2 倍大小的 RAM。 因此，如果数据帧为 10 GB，请使用至少具有 20 GB RAM 的计算目标，以确保可以将数据帧恰当地置于内存中并对其进行处理。 

对于多个虚拟 CPU (vCPU)，请记住，所设置的分区应拟合计算机上的每个 vCPU 所能拥有的 RAM 容量。 也就是说，如果具有 16 GB RAM 4 个 vCPU，则每个 vCPU 需要大约 2-GB 的数据帧。

### <a name="minimize-cpu-workloads"></a>最小化 CPU 工作负载

如果无法将更多 RAM 添加到计算机，则可以通过以下技巧来帮助最小化 CPU 工作负载和优化处理时间。 单个系统和分布式系统均适用以下建议。

方法 | 说明
----|----
压缩 | 为数据使用其他表示形式，该形式应使用较少内存且不会对计算结果产生显著影响。<br><br>*示例：* 不要将条目存储为每个条目包含 10 个字节或更多字节的字符串，而是将它们存储为布尔值 True 或 False（可使用 1 个字节存储）。
分块 | 将数据以子集（区块）的形式加载到内存中，每次处理一个子集或并行处理多个子集。 如果需要处理所有数据，但不需要同时将所有数据加载到内存中，则此方法最有效。 <br><br>*示例：* 一次加载和处理一个月的数据，而不是一次处理整整一年的数据。
索引 | 应用并使用索引，即用于指示在何处查找所需数据的摘要。 当只需要使用数据的一个子集而不是完整集时，索引很有用<br><br>*示例：* 如果有按月份排序的全年销售数据，可借助索引快速搜索要处理的所需月份的数据。

## <a name="scale-data-processing"></a>改变数据处理的规模

如果前面的建议不够用，且无法找到适合容纳你的数据的虚拟机，可以： 

* 使用像 `Spark` 或 `Dask` 这样的框架来处理“超出内存”的数据。 通过这种方式，数据帧按分区加载到 RAM 中并进行处理，最后收集最终结果。  

* 使用分布式框架横向扩展到群集。 采用这种方式，将拆分数据处理负载并在多个 CPU 上并行运行，最后收集最终结果。


### <a name="recommended-distributed-frameworks"></a>建议的分布式框架

下表根据代码偏好或数据大小推荐与 Azure 机器学习集成的分布式框架。

经验或数据大小 | 建议
------|------
如果你熟悉 `Pandas`| `Modin` 或 `Dask` 数据帧
如果偏好 `Spark` | `PySpark`
适用于小于 1 GB 的数据 | 在本地或对远程 Azure 机器学习计算实例执行 `Pandas`
适用于大于 10 GB 的数据| 使用 `Ray`、`Dask` 或 `Spark` 移动到群集

可以 `Dask` 在具有[dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure)包的 Azure ML 计算群集上创建群集。 或者，你可以 `Dask` 在计算实例上本地运行。

## <a name="next-steps"></a>后续步骤

* [Azure 机器学习的数据引入选项](concept-data-ingestion.md)。
* [使用 Azure 数据工厂进行数据引入](how-to-data-ingest-adf.md)。
