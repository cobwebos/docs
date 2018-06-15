---
title: 有关如何使用 Azure 机器学习数据准备执行 API 的深入指南 | Microsoft Docs
description: 本文档提供了有关执行以前设计的数据源和数据准备包的详细信息
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: dac13d36477dccce3bce2ecb9fe2326c36d9ef59
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830589"
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>从 Python 执行数据源和数据准备包

若要在 Python 中使用 Azure 机器学习数据源或 Azure 机器学习数据准备包，请执行以下操作：

1. 转到项目的“数据”选项卡。

2. 右键单击相应的源。

3. 选择“生成数据访问代码文件”。

此操作创建一个简短的 Python 脚本，用于执行包并返回数据帧。

## <a name="data-sources"></a>数据源

`azureml.dataprep.datasource` 模块包含一个函数，用于执行数据源并返回数据帧：`load_datasource(path, secrets=None, spark=None)`。
- `path` 是数据源（.dsource 文件）的路径。
- `secrets` 是一个可选字典，用于将密钥映射到机密。
- `spark` 是一个可选布尔值，用于指定是返回 Spark 数据帧还是返回 Pandas 数据帧。 默认情况下，Azure Machine Learning Workbench 会根据上下文确定运行时返回哪种数据帧。

## <a name="data-preparations-packages"></a>数据准备包

`azureml.dataprep.package` 模块包含从数据准备包执行数据流的三个函数。

### <a name="execution-functions"></a>执行函数

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` 提交要执行的指定数据流，但不返回数据帧。
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` 运行指定的数据流并将结果作为数据帧返回。
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` 基于内存中数据源运行指定的数据流，并将结果作为数据帧返回。 `user_config` 参数是一个字典，它将数据源（.dsource 文件）的绝对路径映射到以一列列表的形式表示的内存中数据源。

### <a name="common-arguments"></a>常用参数

- `package_path` 是数据准备包（.dprep 文件）的路径。
- `dataflow_idx` 是包中要执行的数据流的从零开始的索引。 如果指定的数据流引用其他数据流或数据源，也会执行这些数据流或数据源。
- `secrets` 是一个可选字典，用于将密钥映射到机密。
- `spark` 是一个可选布尔值，用于指定是返回 Spark 数据帧还是返回 Pandas 数据帧。 默认情况下，Workbench 会根据上下文确定运行时返回哪种数据帧。
