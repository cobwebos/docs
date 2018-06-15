---
title: 在 Azure Machine Learning Workbench 中查找准确度最高且持续时间最短的运行 | Microsoft Docs
description: 一个端到端用例，它使用 Azure Machine Learning Workbench 通过 CLI 查找最高准确度
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 077af8b5d3367dd2188cbd6e5d76aaf52512a1e8
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830793"
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>查找准确度最高且持续时间最短的运行
假定有多个运行，一个用例是查找具有最高准确度的运行。 一种方法是通过命令行接口 (CLI) 使用 [JMESPath](http://jmespath.org/) 查询。 有关如何在 Azure CLI 中使用 JMESPath 的详细信息，请参阅[通过 Azure CLI 2.0 使用 JMESPath 查询](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)。 在下面的示例中，创建了准确度值为 0、0.98、1 和 1 的四个运行。 当 `Threshold = .03` 时，如果运行处于范围 `[MaxAccuracy-Threshold, MaxAccuracy]` 中，则会对运行进行筛选。

## <a name="sample-data"></a>样本数据
如果没有具有 `Accuracy` 值的现有运行，则以下步骤将生成用于查询的运行。

首先，在 Azure Machine Learning Workbench 中创建一个 Python 文件，将其命名为 `log_accuracy.py`，然后将以下代码粘贴到其中：
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

接下来，创建文件 `run.py`，然后将以下代码粘贴到其中：
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

最后，通过 Workbench 打开 CLI 并运行命令 `python run.py` 来提交四个试验。 脚本完成后，在 `Run History` 选项卡中应当会看到附加的四个运行。

## <a name="query-the-run-history"></a>查询运行历史记录
第一个命令查找最大准确度值。
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

使用此最大准确度值 `1` 和阈值 `0.03`，第二个命令将使用 `Accuracy` 对运行进行筛选，然后按 `duration` 升序对运行进行排序。
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> 如果希望执行严格的上限检查，则查询格式将为 ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

如果使用 Powershell，则以下代码将使用本地变量来存储阈值和最大准确度：
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>后续步骤
有关日志记录的详细信息，请参阅[如何使用 Azure Machine Learning Workbench 中的运行历史记录和模型指标](how-to-use-run-history-model-metrics.md)。    
