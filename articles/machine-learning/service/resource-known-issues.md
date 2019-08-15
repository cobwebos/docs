---
title: 已知问题与故障排除
titleSuffix: Azure Machine Learning service
description: 获取 Azure 机器学习服务的已知问题、解决方法和故障排除的列表。
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 74d345249e1cbaeb45a1a35d3c3d2f61a4c0b9cf
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032980"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Azure 机器学习服务的已知问题和故障排除

本文可帮助你查找和更正使用 Azure 机器学习服务时遇到的错误或失败。

## <a name="visual-interface-issues"></a>视觉对象接口问题

机器学习服务问题的可视化界面。

### <a name="long-compute-preparation-time"></a>长计算准备时间

创建新的计算或调用它保留计算需要时间, 可能需要几分钟甚至更长时间。 团队正在努力优化。


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>无法运行只包含数据集的试验 

你可能想要运行一个试验, 只包含数据集来可视化数据集。 但是, 不允许运行仅包含数据集的试验。 我们正在积极地解决此问题。
 
在修复之前, 可以将数据集连接到任何数据转换模块 (选择数据集中的列, 编辑元数据, 拆分数据等) 并运行试验。 然后, 可以可视化数据集。 

下图显示了如何![: visulize](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK 安装问题

**错误消息：无法卸载 'PyYAML'**

适用于 Python 的 Azure 机器学习 SDK：PyYAML 是 distutils 安装的项目。 因此，在部分卸载的情况下，我们无法准确确定哪些文件属于它。 若要在忽略此错误的同时继续安装 SDK，请使用：

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**错误消息:`ERROR: No matching distribution found for azureml-dataprep-native`**

Anaconda 的 Python 3.7.4 分发有一个破坏 azureml-sdk 安装的缺陷。 此[GitHub 问题](https://github.com/ContinuumIO/anaconda-issues/issues/11195)中讨论了此问题, 可通过使用此命令创建新的 Conda 环境来解决此问题:
```bash
conda create -n <env-name> python=3.7.3
```
这将使用 Python 3.7.3 创建 Conda 环境, 而3.7.4 中不存在安装问题。

## <a name="trouble-creating-azure-machine-learning-compute"></a>创建 Azure 机器学习计算时发生故障

如果用户在 GA 发布之前已通过 Azure 门户创建了自己的 Azure 机器学习工作区，则他们很可能无法在该工作区中创建 Azure 机器学习计算。 可对服务提出支持请求，也可通过门户或 SDK 创建新的工作区以立即解除锁定。

## <a name="image-building-failure"></a>映像生成失败

部署 Web 服务时映像生成失败。 解决方法是将“pynacl==1.2.1”作为 pip 依赖项添加到 Conda 文件以进行映像配置。

## <a name="deployment-failure"></a>部署失败

如果观察到 `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`，请将部署中使用的 VM 的 SKU 更改为具有更多内存的 SKU。

## <a name="fpgas"></a>FPGA

你将无法在 FPGA 上部署模型，直到已请求并获得 FPGA 配额批准为止。 若要请求访问权限，请填写配额请求表单： https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>自动机器学习

Tensor Flow 自动化机器学习当前不支持 Tensor 流版本1.13。 安装此版本将导致包依赖关系停止工作。 我们正在努力解决此问题。 

### <a name="experiment-charts"></a>试验图

自4/12 以来, 自动 ML 试验迭代中显示的二进制分类图表 (精度召回、ROC、增益曲线等) 在用户界面中无法正确呈现。 图表绘图当前显示的是反转结果, 在这种情况下, 较低的结果显示更好的模型。 正在调查解决方案。

## <a name="databricks"></a>Databricks

Databricks 和 Azure 机器学习问题。

### <a name="failure-when-installing-packages"></a>安装包时失败

安装更多包时, Azure 机器学习 SDK 安装在 Azure Databricks 上失败。 某些包（如 `psutil`）可能会导致冲突。 若要避免安装错误, 请通过冻结库版本来安装包。 此问题与 Databricks 相关, 而不是与 Azure 机器学习服务 SDK 相关。 其他库也可能会遇到此问题。 例如：

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

或者, 如果在 Python 库中保留了饰面安装问题, 则可以使用 init 脚本。 此方法并不正式支持。 有关详细信息, 请参阅[群集范围的初始化脚本](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts)。

### <a name="cancel-an-automated-machine-learning-run"></a>取消自动机器学习运行

在 Azure Databricks 上使用自动机器学习功能时, 若要取消运行并启动新的试验运行, 请重新启动 Azure Databricks 群集。

### <a name="10-iterations-for-automated-machine-learning"></a>自动机器学习 > 10 次迭代

在自动机器学习设置中, 如果有10个以上的迭代, `show_output`请`False`在提交运行时将设置为。

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Azure 机器学习 SDK/自动机器学习的小组件

Databricks 笔记本不支持 Azure 机器学习 SDK 小组件, 因为笔记本无法解析 HTML 小组件。 可以通过在 Azure Databricks 笔记本单元中使用此 Python 代码在门户中查看小组件:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>导入错误:没有名为 "pandas" 的模块

如果你在使用自动机器学习时看到此错误:

1. 运行以下命令, 在 Azure Databricks 群集中安装两个包: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. 分离群集, 然后将其重新连接到笔记本。 

如果这些步骤不能解决问题, 请尝试重新启动群集。

### <a name="failtosendfeather"></a>FailToSendFeather

如果读取 Azure Databricks 群集上的数据时出现错误,请参阅以下解决方案:`FailToSendFeather`

* 将`azureml-sdk[automl_databricks]`包升级到最新版本。
* 添加`azure-dataprep`版本1.1.8 或更高版本。
* 添加`pyarrow`版本0.11 或更高版本。

## <a name="azure-portal"></a>Azure 门户

如果直接通过 SDK 或门户的共享链接查看工作区，则将无法在扩展程序中查看包含订阅信息的常规概述页。 也将无法切换到另一个工作区。 如果需要查看其他工作区，解决方法是直接转到 [Azure 门户](https://portal.azure.com)并搜索工作区名称。

## <a name="diagnostic-logs"></a>诊断日志

如果在请求帮助时可以提供诊断信息，有时会很有帮助。 若要查看某些日志, 请访问[Azure 门户](https://portal.azure.com)并访问工作区, 并选择 "**工作区" > 试验 > 运行 > 日志**。

> [!NOTE]
> Azure 机器学习服务在定型期间记录来自各种来源的信息, 例如 AutoML 或运行训练作业的 Docker 容器。 其中许多日志没有记录。 如果遇到问题, 请与 Microsoft 支持部门联系, 他们可以在故障排除过程中使用这些日志。

## <a name="activity-logs"></a>活动日志

Azure 机器学习工作区中的某些操作不会将信息记录到__活动日志__。 例如, 启动定型运行或注册模型。

其中的一些操作显示在工作区的 "__活动__" 区域中, 但并不表示启动了活动的人员。

## <a name="resource-quotas"></a>资源配额

了解使用 Azure 机器学习时可能遇到的[资源配额](how-to-manage-quotas.md)。

## <a name="authentication-errors"></a>身份验证错误

如果通过远程作业对某个计算目标执行管理操作，会收到以下错误之一：

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

例如，如果尝试通过一个为实施远程执行操作而提交的机器学习管道创建或附加计算目标，会收到错误。

## <a name="overloaded-azurefile-storage"></a>重载的 AzureFile 存储

如果收到错误`Unable to upload project files to working directory in AzureFile because the storage is overloaded`, 请应用以下解决方法。

如果对其他工作负荷 (如数据传输) 使用文件共享, 则建议使用 blob, 以便可以自由地使用文件共享来提交运行。 你还可以在两个不同的工作区之间拆分工作负荷。
