---
title: 如何使用 Azure 机器学习工作台中的运行历史记录和模型指标 | Microsoft Docs
description: 使用 Azure 机器学习工作台的运行历史记录和模型指标功能的指南
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: dddf6af506269c7f22cea69e39b55effa2f14d63
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-run-history-and-model-metrics-in-azure-machine-learning-workbench"></a>如何使用 Azure 机器学习工作台中的运行历史记录和模型指标

Azure 机器学习工作台通过其“运行历史记录”和“模型指标”功能支持数据科学试验。
使用“运行历史记录”可以跟踪机器学习试验的输出，然后可以筛选和比较其结果。
可从任何脚本点记录“模型指标”，从而跟踪在数据科学试验中所有最重要的值。
本文介绍如何有效利用这些功能来提高数据科学试验的速度和质量。

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
* [创建和安装 Azure 机器学习](../service/quickstart-installation.md)
- [创建项目](../service/quickstart-installation.md)


## <a name="azure-ml-logging-api-overview"></a>Azure ML 日志记录 API 概述
通过 Python 中的 azureml.logging 模块（通过 Azure ML 工作台安装）可以使用 [Azure ML 日志记录 API](reference-logging-api.md)。导入此模块后，可以使用 **get_azureml_logger** 方法实例化“记录器”对象。
然后，可以使用记录器的 **log** 方法存储 Python 脚本产生的键/值对。
如下所示，当前支持标量和列表类型的日志记录模型指标。

```Python
# create a logger instance in already set up environment 
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# log scalar (any integer or floating point type is fine)
logger.log("simple value", 7)


# log list
logger.log("all values", [5, 6, 7])
```
在 Azure ML 工作台项目中使用记录器很容易，本文介绍了如何执行此操作。

## <a name="create-a-project-in-azure-ml-workbench"></a>在 Azure ML 工作台中创建项目
如果尚未有项目，可以在[创建和安装快速入门](../service/quickstart-installation.md)中创建一个项目。在“项目仪表板”中，打开 **iris_sklearn.py** 脚本（如下所示）。

![从“文件”选项卡中访问脚本](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-01b.png)

可以将此脚本用作在 Azure ML 中预期实现模型指标日志记录的指南。

## <a name="parameterize-and-log-model-metrics-from-script"></a>参数化并记录脚本中的模型指标
在 **iris_sklearn.py** 脚本中，在 Python 中导入和构建记录器的预期模式可以缩短为以下代码行。

```Python
from azureml.logging import get_azureml_logger
run_logger = get_azureml_logger()
```

创建后，可以通过任何名称/值对调用 **log** 方法。

完成开发后，参数化脚本通常很有用，因为这样值就可以通过命令行传入。
下面的示例显示如何使用标准 Python 库接受命令行参数（如果存在）。
此脚本为正则化速率（“reg”）采用了单个参数，用于拟合分类模型，以在不过度拟合的情况下提升*准确性*。
随后，这些变量将记录为“正则化速率”和“准确性”，以便可以轻松确定具有最佳结果的模型。

```Python
# change regularization rate and you will likely get a different accuracy.
reg = 0.01
# load regularization rate from argument if present
if len(sys.argv) > 1:
    reg = float(sys.argv[1])

print("Regularization rate is {}".format(reg))

# log the regularization rate
run_logger.log("Regularization Rate", reg)

# train a logistic regression model on the training set
clf1 = LogisticRegression(C=1/reg).fit(X_train, Y_train)
print (clf1)

# evaluate the test set
accuracy = clf1.score(X_test, Y_test)
print ("Accuracy is {}".format(accuracy))

# log accuracy
run_logger.log("Accuracy", accuracy)
```

在脚本中采取这些步骤可以使它们充分利用“运行历史记录”。

## <a name="launch-runs-from-project-dashboard"></a>从项目仪表板启动运行
返回到“项目仪表板”后，可以通过选择“iris_sklearn.py”脚本并在“参数”编辑框中输入“正则化速率”参数，启动“跟踪的运行”。

![输入参数和启动运行](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-05.png)

由于启动跟踪的运行不会阻止 Azure ML 工作台，所以可以并行启动多个运行。
如下所示，每个跟踪运行的状态在“作业面板”中都可见。

![在作业面板中跟踪运行](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-06.png)

这可以充分利用资源，无需连续运行每个作业。

## <a name="view-results-in-run-history"></a>在运行历史记录中查看结果
跟踪运行的进度和结果可供在 Azure ML 工作台的“运行历史记录”中分析。
“运行历史记录”提供三个不同的视图：
- 仪表板
- 详细信息
- 比较

“仪表板”视图显示给定脚本所有运行的数据，并以图形和表格两种形式呈现。
“详细信息”视图显示从给定脚本的特定运行中生成的所有数据，包括记录的指标和输出文件（例如呈现的绘图）。“比较”视图可以并排显示两个或三个运行的结果，也包括记录的指标和输出文件。

在 **iris_sklearn.py** 的八个跟踪运行中，已记录“正则化速率”参数和“准确性”结果的值，用于解释如何使用“运行历史记录”视图。

### <a name="run-history-dashboard"></a>运行历史记录仪表板
所有八个运行的结果都在“运行历史记录仪表板”中可见。
因为 **iris_sklearn.py** 记录“正则化速率”和“准确性”，所以“运行历史记录仪表板”在默认情况下显示关于这些值的图表。

![运行历史记录仪表板](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-07.png)

“运行历史记录仪表板”可进行自定义，以使记录的值也显示在网格中。  如下所示，单击“自定义”图标可显示“列表视图自定义”对话框。

![自定义运行历史记录仪表板网格](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-08.png)

在跟踪运行期间记录的任何值均可显示，并且选择“正则化速率”和“准确性”可将它们添加到网格。

![自定义网格中的记录的值](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-09.png)

悬停在图表中各点的上方可以很容易地找到关注的运行。  在此情况下，运行 7 已产生良好的准确性，并且持续时间低。

![查找关注的运行](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-10.png)

单击任何图表中与运行 7 关联的点或单击网格中指向运行 7 的链接，都可显示“运行历史记录详细信息”。

### <a name="run-history-details"></a>运行历史记录详细信息
在此视图中，将显示运行 7 的完整结果以及运行 7 产生的任何项目。

![运行历史记录详细信息](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-11.png)

“运行历史记录详细信息”视图还提供“下载”任何写入“./outputs”文件夹的文件的功能（Azure ML 工作台的云存储为“运行历史记录”备份这些文件，这是另一篇文章的主题）。

最后，使用“运行历史记录详细信息”在此运行时还原项目的状态。
如下所示，单击“还原”按钮可显示确认对话框。

![还原运行确认](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-13.png)

如果已确认，文件可能会被覆盖或删除，因此请谨慎使用此功能。

### <a name="run-history-comparison"></a>运行历史记录比较
通过在“运行历史记录仪表板”中选择两个或三个运行并单击“比较”，可转到“运行历史记录比较”视图。
通过单击“比较”并在“运行历史记录详细信息”视图中选择一个运行，也可以转到“运行历史记录比较”视图。
在任何一种情况下，使用“运行历史记录比较”视图都可以并排查看两个或三个运行的记录结果和项目。

![运行历史记录比较](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-12.png)

此视图在比较绘图时尤为有用，但通常情况下，运行的任何属性都可在此进行比较。

### <a name="command-line-interface"></a>命令行接口
Azure 机器学习工作台还可以通过其“命令行接口”提供对“运行历史记录”的访问权限。
若要访问“命令行接口”，请单击“打开命令提示符”菜单，如下所示。

![打开命令提示符](media/how-to-use-run-history-model-metrics/how-to-use-run-history-model-metrics-14.png)

适用于“运行历史记录”的命令可通过 `az ml history` 访问，添加 `-h` 标记可以使用在线帮助。
```
$ az ml history -h

Group
    az ml history: View run history of machine learning experiments.

Commands:
    compare : Compare two runs.
    download: Download all the artifacts from a run into the destination path.
    info    : Details about one run.
    last    : Get detail about most recent run.
    list    : List runs.
    promote : Promote Artifacts.
```
这些命令提供与在“运行历史记录视图”显示的相同结果并返回相同数据。
例如，最后一个运行的结果可以显示为 JSON 对象。
```
$ az ml history last
{
  "Accuracy": 0.6792452830188679,
  "Regularization Rate": 0.078125,
  "attachments": "control_log, control_log.txt, driver_log, driver_log.txt, pid.txt, dataprep/backgroundProcess.log, dataprep/backgroundProcess_Engine.log, dataprep/backgroundProcess_EngineHost.log, dataprep/backgroundProcess_ProjectProvider.log, dataprep/backgroundProcess_Sampling.log, dataprep/backgroundProcess_Telemetry.log, outputs/cm.png, outputs/model.pkl, outputs/sdk_debug.txt, outputs/roc.png",
  "created_utc": "2017-09-08T00:58:01.611105+00:00",
  "description": null,
  "duration": "0:00:04.892389",
  "end_time_utc": "2017-09-08T00:58:07.951403+00:00",
  "experiment_id": "ce92d0a9-3e2c-4d51-85de-93ef22249ce1",
  "heartbeat_enabled": true,
  "hidden": false,
  "name": null,
  "parent_run_id": null,
  "properties": {
    "CommitId": "e77a5f0df2af1a482bbe39b70bfbb16b62228cb3"
  },
  "run_id": "IrisDemo_1504832281116",
  "run_number": 8,
  "script_name": "iris_sklearn.py",
  "start_time_utc": "2017-09-08T00:58:03.059014+00:00",
  "status": "Completed",
  "target": "local",
  "user_id": "e9fafe06-b0e4-4154-8374-aae34f9977b2"
}
```
此外，所有运行的列表可以表格格式显示。
```
$ az ml history list -o table
  Accuracy    Regularization Rate  Duration        Run_id                  Script_name      Start_time_utc                    Status
----------  ---------------------  --------------  ----------------------  ---------------  --------------------------------  ---------
  0.679245               0.078125  0:00:04.892389  IrisDemo_1504832281116  iris_sklearn.py  2017-09-08T00:58:03.059014+00:00  Completed
  0.679245               0.15625   0:00:04.734956  IrisDemo_1504832255198  iris_sklearn.py  2017-09-08T00:57:38.507196+00:00  Completed
  0.660377               0.3125    0:00:04.913762  IrisDemo_1504832234904  iris_sklearn.py  2017-09-08T00:57:16.849881+00:00  Completed
  0.660377               0.625     0:00:06.107764  IrisDemo_1504832210767  iris_sklearn.py  2017-09-08T00:56:54.602813+00:00  Completed
  0.641509               1.25      0:00:04.742727  IrisDemo_1504832171373  iris_sklearn.py  2017-09-08T00:56:13.879280+00:00  Completed
  0.660377               2.5       0:00:04.915401  IrisDemo_1504832148526  iris_sklearn.py  2017-09-08T00:55:52.937083+00:00  Completed
  0.641509               5         0:00:04.730627  IrisDemo_1504832127172  iris_sklearn.py  2017-09-08T00:55:29.612382+00:00  Completed
  0.641509              10         0:00:06.059082  IrisDemo_1504832109906  iris_sklearn.py  2017-09-08T00:55:14.739806+00:00  Completed

```
“命令行接口”是访问 Azure 机器学习工作台强大功能的备用路径。

## <a name="next-steps"></a>后续步骤
这些功能均可用于协助开展数据科学试验的过程。
我们希望你能发现它们的用处，并对你的反馈深表感谢。
这只是我们的初始实现，我们计划添加大量的增强功能。
我们期待不断将它们交付到 Azure 机器学习工作台中。 
