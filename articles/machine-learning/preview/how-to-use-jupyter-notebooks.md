---
title: "如何在 Azure 机器学习工作台中使用 Jupyter Notebook | Microsoft Docs"
description: "Azure 机器学习工作台的 Jupyter Notebook 功能使用指南"
services: machine-learning
author: rastala
ms.author: roastala
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/09/2017
ms.openlocfilehash: 80cdd07bff865776a68897a7b8c1b3fe66b76b18
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="how-to-use-jupyter-notebook-in-azure-machine-learning-workbench"></a>如何在 Azure 机器学习工作台中使用 Jupyter Notebook

Azure 机器学习工作台通过 Jupyter Notebook 的集成支持交互式数据科学试验。 本文介绍如何有效利用此功能来提高交互式数据科学实验的速度和质量。

## <a name="prerequisites"></a>先决条件
- [安装和创建 Azure 机器学习](/machine-learning/preview/quickstart-installation.md)。
- 熟悉 [Jupyter Notebook](http://jupyter.org/)，因为本文不讲授如何使用 Jupyter。

## <a name="jupyter-notebook-architecture"></a>Jupyter Notebook 体系结构
概括而言，Jupyter Notebook 体系结构包括三个组件，每个组件可以在不同的计算环境中运行：

- **客户端**：接收用户输入并显示呈现的输出
- **服务器**：承载笔记本文件（.ipynb 文件）的 Web 服务器
- **内核**：实际执行笔记本单元的运行时环境

有关更多详细信息，请参阅官方 [Jupyter 文档](http://jupyter.readthedocs.io/en/latest/architecture/how_jupyter_ipython_work.html)。 下图描绘了该客户端、服务器和内核体系结构如何映射到 Azure ML 中的组件。

![笔记本体系结构](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-architecture.png)

## <a name="kernels-in-azure-ml-workbench-notebook"></a>Azure ML 工作台笔记本中的内核
配置运行配置并计算项目中 `aml_config`文件夹中的目标可以访问 Azure ML 工作台中的很多不同内核。 通过发出 `az ml computetarget attach` 命令添加新计算目标相当于添加新内核。

>[!NOTE]
>查看[配置执行](experimentation-service-configuration.md)以了解更多有关运行配置和计算目标的详细信息。

### <a name="kernel-naming-convention"></a>内核命名约定
通常以“\<项目名称> \<运行配置名称>”的格式命名内核。 例如，如果在名为 _myIris_ 的项目中有一个名为 _docker python_ 的运行配置，那么当打开 Jupyter Notebook 时可以在内核列表中找到名为“myIris docker-python”的内核。

目前，该工作台支持以下类型的内核。

### <a name="local-python-kernel"></a>本地 Python 内核
此类 Python 内核支持在本地计算机上执行。 它与 Azure 机器学习的运行历史记录支持集成。 该内核的名称通常为“my_project_name local”。

>[!NOTE]
>不要使用“Python 3”内核。 它是 Jupyter 默认提供的独立内核。 它没有与 Azure 机器学习功能集成。

### <a name="python-kernel-in-docker-local-or-remote"></a>Docker 中的 Python 内核（本地或远程）
此类 Python 内核在本地计算机或远程 Linux 虚拟机中的 Docker 容器中运行。 该内核的名称通常为“my_project docker”。 相关的 `docker.runconfig` 文件将字段 `Framework` 设置为 `Python`。

### <a name="pyspark-kernel-in-docker-local-or-remote"></a>Docker 中的 PySpark 内核（本地或远程）
此类 PySpark 内核在 Docker 容器（在本地计算机或远程 Linux 虚拟机上）中运行的 Spark 上下文中执行脚本。 该内核的名称通常为“my_project docker”。 相关的 `docker.runconfig` 文件将字段 `Framework` 设置为 `PySpark`。

### <a name="pyspark-kernel-on-hdinsight-cluster"></a>HDInsight 群集上的 PySpark 内核
此类内核在远程 HDInsight 集群（可将其附加为项目的计算目标）中运行。 该内核的名称通常为“my_project my_hdi”。 

>[!IMPORTANT]
>在 HDI 计算目标的 `.compute` 文件中，必须将 `yarnDeployMode` 字段更改为 `client`（默认值是 `cluster`）才能使用此内核。 

## <a name="start-jupyter-server-from-the-workbench"></a>从工作台启动 Jupyter 服务器
在 Azure 机器学习工作台中，可通过该工作台的“Notebook”选项卡访问笔记本。_分类 Iris_ 示例项目包括 `iris.ipynb` 示例笔记本。

![笔记本选项卡](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-01.png)

当在 Azure 机器学习工作台中打开笔记本时，它在**预览模式**下显示在自己的文档选项卡中。 这是不需要运行 Jupyter 服务器和内核的只读视图。

![笔记本预览](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-02.png)

单击“启动 Notebook 服务器”按钮启动 Jupyter 服务器，并将笔记本切换到**编辑模式**。 熟悉的 Jupyter Notebook 用户界面看起来嵌入在工作台中。 现在可以在“内核”菜单中设置内核，并启动交互式笔记本会话。 

>[!NOTE]
>请注意对于非本地内核，如果是第一次使用，可能需要一或两分钟时间进行启动。 您可以在 CLI 窗口中执行 `az ml experiment prepare` 命令来准备计算目标，以便在准备好计算目标后可以更快地启动内核。

![编辑模式](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-04.png)

这是完全交互式的 Jupyter Notebook 体验。 此窗口支持所有常规笔记本操作和键盘快捷方式，但某些文件操作除外，因为它们可以通过工作台“Notebook”选项卡和“文件”选项卡完成。

## <a name="start-jupyter-server-from-command-line"></a>从命令行启动 Jupyter 服务器
也可以从命令行窗口发出 `az ml notebook start` 命令来启动笔记本会话：
```
$ az ml notebook start
[I 10:14:25.455 NotebookApp] The port 8888 is already in use, trying another port.
[I 10:14:25.464 NotebookApp] Serving notebooks from local directory: /Users/johnpelak/Desktop/IrisDemo
[I 10:14:25.465 NotebookApp] 0 active kernels 
[I 10:14:25.465 NotebookApp] The Jupyter Notebook is running at: http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.465 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[C 10:14:25.466 NotebookApp] 
    
    Copy/paste this URL into your browser when you connect for the first time,
    to login with a token:
        http://localhost:8889/?token=1f0161ab88b22fc83f2083a93879ec5e8d0ec18490f0b953
[I 10:14:25.759 NotebookApp] Accepting one-time-token-authenticated connection from ::1
[I 10:16:52.970 NotebookApp] Kernel started: 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
[I 10:16:53.854 NotebookApp] Adapting to protocol v5.1 for kernel 7f8932e0-89b9-48b4-b5d0-e8f48d1da159
```
默认浏览器将自动启动，而且 Jupyter 服务器指向项目主目录。 还可以使用 CLI 窗口中显示的 URL 和令牌在本地启动其他浏览器窗口。 

![项目仪表板](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-07.png)

现在可以单击 `.ipynb` 笔记本文件，打开它并设置内核（如果尚未设置），然后启动交互式会话。

![项目仪表板](media/how-to-use-jupyter-notebooks/how-to-use-jupyter-notebooks-08.png)

## <a name="use-magic-commands-to-manage-experiments"></a>使用 magic 命令管理试验

可以在笔记本单元中使用 [magic 命令](http://ipython.readthedocs.io/en/stable/interactive/magics.html)来跟踪运行历史记录和保存输出，例如模型或数据集。

若要跟踪单个笔记本单元的运行，可使用“%azureml history on”magic 命令。 启用历史记录后，每个单元运行将作为条目显示在运行历史记录中。

```
%azureml history on
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()
logger.log("Cell","Load Data")
```

若要禁用单元运行跟踪，可使用“%azureml history off”magic 命令。

可以使用“%azureml upload”magic 命令保存运行中的模型和数据文件。 保存的对象作为输出显示在给定运行的运行历史记录视图中。

```
modelpath = os.path.join("outputs","model.pkl")
with open(modelpath,"wb") as f:
    pickle.dump(model,f)
%azureml upload outputs/model.pkl
```

>[!NOTE]
>必须将输出保存到名为“outputs”的文件夹

## <a name="next-steps"></a>后续步骤
- 若要了解如何使用 Jupyter Notebook，请访问 [Jupyter 官方文档](http://jupyter-notebook.readthedocs.io/en/latest/)。    
- 若要更深入地了解 Azure ML 试验执行环境，请查看 [Azure 机器学习试验服务概述](experimentation-service-configuration.md)

