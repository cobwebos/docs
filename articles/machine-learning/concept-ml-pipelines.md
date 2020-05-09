---
title: 什么是 Azure 机器学习管道
description: 了解机器学习（ML）管道如何帮助您构建、优化和管理机器学习工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 79e9551650866e5f5fc28b123c1259e2a5fb6f20
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983442"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>什么是 Azure 机器学习管道？

在本文中，可以了解 Azure 机器学习管道如何帮助您构建、优化和管理机器学习工作流。 这些工作流具备多个优点： 

+ 简单
+ Speed
+ 可重复
+ 灵活性
+ 版本控制和跟踪
+ 模块化 
+ 质量保证
+ 成本控制

在机器学习项目超越单纯的探索并进入迭代阶段后，这些优点就变得非常明显。 即使是简单的单步骤管道也很有价值。 机器学习项目通常处于一种复杂的状态，将精确完成一个工作流变成一个非常简单的过程可能会让人感到宽慰。

了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。

![Azure 机器学习中的机器学习管道](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>我应该使用哪种 Azure 管道技术？

Azure 云提供多种其他管道，每种管道都有不同的用途。 下表列出了各种不同的管道及其用途：

| 方案 | 主要角色 | Azure 产品 | OSS 产品 | 规范管道 | 优势 | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| 模型业务流程（机器学习） | 数据科学家 | Azure 机器学习管道 | Kubeflow 管道 | 数据 > 模型 | 分布，缓存，代码优先，重用 | 
| 数据业务流程（数据准备） | 数据工程师 | [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Apache Airflow | 数据 > 数据 | 强类型移动。 以数据为中心的活动。 |
| 代码 & 应用程序业务流程（CI/CD） | 应用开发人员/Ops | [Azure DevOps 管道](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | 代码 + 模型-> 应用/服务 | 最开放和灵活的活动支持，批准队列，具有门的阶段 | 

## <a name="what-can-azure-ml-pipelines-do"></a>Azure ML 管道有哪些用途？

Azure 机器学习管道是整个机器学习任务的可独立执行的工作流。 子任务封装为管道中的一系列步骤。 Azure 机器学习管道可以像调用 Python 脚本一样简单，因此几乎可以执行任何操作__。 管道应专注于机器学习任务，例如__：

+ 数据准备，包括导入、验证、清理、修整、转换、规范化和暂存
+ 训练配置，包括参数化自变量、文件路径和日志记录/报告配置
+ 高效重复地进行培训和验证。 效率可能来自指定特定的数据子集、不同的硬件计算资源、分布式处理和进度监视
+ 部署，包括版本控制、缩放、预配和访问控制 

由于各个步骤相互独立，因此多个数据科学家可以同时处理同一个管道，且不会过度占用计算资源。 由于各个步骤相互分离，因此可以轻松地针对每个步骤使用不同的计算类型/大小。

管道设计好以后，通常会有更多围绕着管道的训练循环进行的优化。 重新运行某个管道时，运行会跳到需要重新运行的步骤（例如，更新后的训练脚本）。 不需要重新运行的步骤则会跳过。 同一分析适用于未更改的用于完成此步骤的脚本。 这种重用功能有助于避免在基础数据未发生更改的情况下运行成本高昂且十分耗时的步骤，如数据引入和转换。

借助 Azure 机器学习，可以针对管道中的每个步骤使用各种工具包和框架，例如 PyTorch 或 TensorFlow。 Azure 协调会你使用的各种[计算目标](concept-azure-machine-learning-architecture.md)，因此中间数据可与下游计算目标共享。

可以直接在 Azure 门户中或在[工作区登陆页面（预览）](https://ml.azure.com)[跟踪管道试验的指标](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)。 发布管道之后，可以配置 REST 终结点，这样就能够从任何平台或堆栈重新运行管道。

简而言之，可以通过管道处理机器学习生命周期的所有复杂任务。 其他 Azure 管道技术有其自身的优势。 [Azure 数据工厂管道](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities)transact-sql 处理数据，而[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)是用于持续集成和部署的正确工具。 但如果关注点是机器学习，则 Azure 机器学习管道可能是满足工作流需求的最佳选择。 

## <a name="what-are-azure-ml-pipelines"></a>什么是 Azure ML 管道？

Azure ML 管道按步骤顺序执行完整的逻辑工作流。 每个步骤都是一个单独的处理操作。 管道在 Azure 机器学习[试验](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)环境中运行。

在 ML 项目的早期阶段，使用 Jupyter 笔记本或 Python 脚本就可以完成 Azure 工作区和资源配置、数据准备、运行配置、训练和验证的所有工作。 但正如函数和类很快就会胜过单个命令式代码块一样，ML 工作流很快就会胜过单个笔记本或脚本。 

通过模块化处理 ML 任务，管道可支持计算机科学命令，即组件应“（仅）做好一件事情”。 在团队编程过程中，模块化显然对于项目能否取得成功至关重要，但就算是个人独自工作，即使是很小的 ML 项目也会涉及单独的任务，每个任务都具有很高的复杂性。 任务包括：工作区配置和数据访问、数据准备、模型定义和配置以及部署。 虽然一项或多项任务的输出构成了另一项任务的输入，但是任何一项任务的具体实现细节在下一项任务中充其量都是无关紧要的事情。 最糟糕的是，一项任务的计算状态可能导致在另一项任务中出现 bug。 

### <a name="analyzing-dependencies"></a>分析依赖项

许多编程生态系统都安装了用于协调资源、库或编译依赖项的工具。 通常，这些工具使用文件时间戳来计算依赖项。 文件发生更改时，只更新（下载、重新编译或打包）文件及其依赖项。 Azure ML 管道大大丰富了此概念。 与传统的生成工具一样，管道会计算各个步骤之间的依赖项，并且只执行必要的重新计算。 

不过，Azure ML 管道中的依赖项分析比简单的时间戳更为复杂。 每个步骤都可以在不同的硬件和软件环境中运行。 数据准备可能是一个耗时的过程，但不需要在具有强大 GPU 的硬件上运行，某些步骤可能需要特定于操作系统的软件，因此建议使用分布式训练等等。 尽管优化资源节省的成本可能非常可观，但是手动同时处理硬件和软件资源中的所有不同变化可能让人不堪重负。 要做到所有这些，而又不会在各步骤之间传输的数据中犯任何错误，就更困难了。 

管道解决了这一问题。 Azure 机器学习可自动协调管道步骤之间的所有依赖项。 此业务流程可能包括启动和关闭 Docker 映像、附加和拆离计算资源，以及以一致且自动的方式在各步骤之间移动数据。

### <a name="reusing-results"></a>重用结果

此外，如果你选择的话，可能会重用步骤的输出。 如果将重用指定为可能性，并且没有上游依赖项触发重新计算，则管道服务将使用该步骤结果的缓存版本。 此类重用大幅缩短了开发时间。 如果有复杂的数据准备任务，则重新运行频率可能会高于确实所需的频率。 若使用管道，则无需担心这一点：如果需要，步骤就会运行；否则，步骤将不会运行。

实例化 [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py) 对象，将对象传递给 `Experiment` 并调用 `submit()` 时，所有这些依赖项分析、业务流程和激活都由 Azure 机器学习处理。 

### <a name="coordinating-the-steps-involved"></a>协调涉及的步骤

创建和运行 `Pipeline` 对象时，将执行以下概要步骤：

+ 对于每个步骤，服务将计算以下各项的要求：
    + 硬件计算资源
    + OS 资源（Docker 映像）
    + 软件资源（Conda/virtualenv 依赖项）
    + 数据输入 
+ 该服务确定步骤之间的依赖项，以生成动态执行图
+ 当执行图中的每个节点运行时：
    + 该服务配置必要的硬件和软件环境（可能重用现有资源）
    + 步骤将运行，从而向其包含的 `Experiment` 对象提供日志记录和监视信息
    + 步骤完成后，其输出将准备作为下一步的输入和/或写入存储
    + 最终确定并分离不再需要的资源

![管道步骤](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>使用 Python SDK 生成管道

在 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 中，管道是 `azureml.pipeline.core` 模块中定义的 Python 对象。 [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) 对象包含一个或多个 [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) 对象的有序序列。 `PipelineStep` 类是抽象类，而实际步骤属于子类，如 [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py)、[PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py) 或 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)。 [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) 类包含一系列可重用的步骤，这些步骤可在管道之间共享。 `Pipeline` 作为 `Experiment` 的一部分运行。

Azure ML 管道与 Azure 机器学习工作区相关联，而管道步骤与该工作区中提供的计算目标相关联。 有关详细信息，请参阅[在 Azure 门户中创建和管理 Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)或[什么是 Azure 机器学习中的计算目标？](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)。

在 Azure 机器学习中，计算目标是 ML 阶段发生的环境。 软件环境可以是远程 VM、Azure 机器学习计算、Azure Databricks、Azure Batch 等。 硬件环境也可能会有很大差异，具体取决于 GPU 支持、内存、存储等。 你可以为每个步骤指定计算目标，这样可以精细地控制成本。 可以使用功能更强或功能更弱的资源来满足项目的特定操作、数据量和性能需求。 

## <a name="building-pipelines-with-the-designer"></a>使用设计器生成管道

偏好可视化设计图面的开发人员可以使用 Azure 机器学习设计器创建管道。 可以从工作区主页上的“设计器”选项使用此工具  。  通过设计器可以将步骤拖放到设计图面上。 若要进行快速开发，可以在 ML 任务范围内使用现有模块；现有模块涵盖了从数据转换到算法选择、训练和部署等所有内容。 或者，可以通过组合在 Python 脚本中定义的步骤来创建完全自定义管道。

在直观地设计管道时，步骤的输入和输出显示为可见。 通过拖放数据连接，可以快速了解和修改管道的数据流。
 
![Azure 机器学习设计器示例](./media/concept-designer/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>了解执行图

管道内的步骤可能依赖于其他步骤。 Azure ML 管道服务执行分析和协调这些依赖项的工作。 生成的“执行图”中的节点是处理步骤。 每个步骤都可能涉及创建或重用硬件和软件的特定组合、重用缓存的结果等。 此执行图中服务的协调和优化可显著加快 ML 阶段并降低成本。 

由于各个步骤独立运行，因此必须在外部定义用于保存在步骤之间流动的输入和输出数据的对象。 这是[数据集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)和[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)对象的角色。 这些数据对象与封装其存储配置的 [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) 对象关联。 始终使用 `PipelineStep` 字符串、`name` 列表和 `inputs` 列表创建 `outputs` 基类。 通常情况下，该基类还具有一个 `arguments` 列表，并且通常具有一个 `resource_inputs` 列表。 子类通常还具有其他参数（例如，`PythonScriptStep` 需要待运行脚本的文件名和路径）。 

执行图是非循环的，但管道可以按定期计划运行，并且可以运行可将状态信息写入文件系统的 Python 脚本，从而能够创建复杂的配置文件。 如果设计自己的管道，以使某些步骤可以并行或异步运行，则 Azure 机器学习会以透明方式处理扇出和扇入的依赖项分析和协调。 通常无需亲自关注执行图的细节，但可通过 [Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes) 属性了解相关细节。 


### <a name="a-simple-python-pipeline"></a>简单的 Python 管道

此代码片段显示了创建和运行基本 `Pipeline` 所需的对象和调用：

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

output_data = PipelineData("output_data", datastore=blob_store)

input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

代码片段以常用 Azure 机器学习对象（`Workspace`、`Datastore`、[ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py) 和 `Experiment`）开头。 然后，该代码将创建用于保存 `input_data` 和 `output_data` 的对象。 数组 `steps` 保存一个元素，即：将使用数据对象并在 `PythonScriptStep` 上运行的 `compute_target`。 然后，代码将实例化 `Pipeline` 对象本身，并将其传入工作区和步骤数组。 对 `experiment.submit(pipeline)` 的调用开始 Azure ML 管道运行。 在管道完成之前，对 `wait_for_completion()` 的调用会被阻止。 

若要了解有关将管道连接到数据的详细信息，请参阅文章[Azure 机器学习中的数据访问](concept-data.md)，以及如何将[数据移入和移入和移入 ML 管道步骤（Python）](how-to-move-data-in-out-of-pipelines.md)。 

## <a name="best-practices-when-using-pipelines"></a>使用管道的最佳做法

如你所见，创建 Azure ML 管道比启动脚本要复杂一些。 管道需要配置和创建一些 Python 对象。 

在以下情况下，建议使用管道：

* 在团队环境中：将 ML 任务划分为多个独立的步骤，以便开发人员可以独立地工作和开发程序。 

* 在部署过程中或接近部署时：确定配置并使用计划的操作和事件驱动的操作来掌握更改的数据。

* 在 ML 项目的早期阶段或单独工作时：使用管道自动化生成。 如果开始担心在实现新想法之前重新创建配置和计算状态，则说明可以考虑使用管道自动化工作流。 

虽然很容易喜欢上重用缓存结果、精细控制计算成本和分离流程，但是管道也会产生成本。 某些反模式包括：

* 使用管道作为分离关注点的唯一手段。 Python 的内置函数、对象和模块可以有效避免混淆编程状态！ 管道步骤的成本比函数调用高得多。

* 管道步骤之间会出现重度耦合。 如果重构从属步骤经常需要修改上一步骤的输出，那么单独步骤的成本一般会大过收益。 步骤过度耦合的另一个迹象是，步骤的参数不是数据，而是控制处理的标记。 

* 提前优化计算资源。 例如，数据准备通常涉及多个阶段，并且你经常可以看到“哦，在此阶段我可以使用 `MpiStep` 进行并行编程，但在另一个阶段我可以使用 `PythonScriptStep` 完成配置要求不高的计算目标”等等。 也许从长远来看，创建像这样的精细步骤可能是值得的，尤其在有可能使用缓存的结果而不是总是重新计算时。 但管道并不旨在代替 Python 的本机`multiprocessing`模块。 

在项目规模变大或接近部署之前，管道应该更粗糙而不是更精细。 如果你认为 ML 项目同样涉及多个阶段，而管道同样为你提供了一个完整的工作流来引导你完成特定阶段，那么你的做法就是正确的  。 

## <a name="key-advantages"></a>主要优点

为机器学习工作流使用管道的主要优点包括：

|主要优点|说明|
|:-------:|-----------|
|**无人参与&nbsp;运行**|对步骤进行计划，使之以可靠且无人参与的方式并行或按顺序运行。 数据准备和建模可以持续几天或几周，而管道可帮助你在进程运行时将精力集中在其他任务上。 |
|**异类计算**|使用多个管道，这些管道已跨异类的可缩放计算资源和存储位置进行可靠的协调。 通过在不同的计算目标（例如 HDInsight、GPU Data Science VM 和 Databricks）上运行单独的管道步骤，可以有效地利用可用的计算资源。|
|**可重用性**|创建适用于特定方案（例如，重新训练和批量评分）的管道模板。 触发器通过简单的 REST 调用从外部系统发布管道。|
|**跟踪和版本控制**|可以使用管道 SDK 显式对数据源、输入和输出进行命名和版本控制，而不是在循环访问时手动跟踪数据和结果路径。 还可以将脚本和数据分开管理以提高工作效率。|
| **模块化** | 分离关注区域和隔离更改可加快软件开发速度并提高质量。 | 
|**协作**|使用管道，数据科学家能够在机器学习设计过程的所有方面进行协作，同时能够并行处理管道步骤。|

### <a name="choosing-the-proper-pipelinestep-subclass"></a>选择适当的 PipelineStep 子类

`PythonScriptStep` 是抽象 `PipelineStep` 中最灵活的子类。 其他子类（如 `EstimatorStep` 子类和 `DataTransferStep`）可以用更少代码完成特定任务。 例如，只需`EstimatorStep`传入步骤、 `Estimator`和计算目标的名称便可创建。 或者，可以重写输入和输出、管道形式参数和实际参数。 有关详细信息，请参阅[通过估算器使用 Azure 机器学习训练模型](how-to-train-ml-models.md)。 

使用 `DataTransferStep` 可轻松地在数据源和接收器之间移动数据。 手动执行此传输的代码非常简单，但却是重复的。 取而代之的是，你可以只使用名称、对数据源和数据接收器的引用以及计算目标创建一个 `DataTransferStep`。 笔记本[使用 DataTransferStep 的 Azure 机器学习管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-data-transfer.ipynb)演示了这种灵活性。

## <a name="modules"></a>模块

虽然管道步骤允许重复使用以前的运行的结果，但在许多情况下，该步骤的构造假定所需的脚本和依赖文件必须在本地可用。 如果数据科学家希望在现有代码的基础上进行构建，则通常必须从单独的存储库克隆脚本和依赖项。

模块在使用上与管道步骤相似，但提供通过工作区加速的版本控制，因此可以实现大规模协作和可重用性。 模块设计为在多个管道中重复使用，并且可以发展，使特定的计算适应不同的用例。 用户可以通过工作区执行以下任务，无需使用外部存储库：

* 创建新模块并发布现有模块的新版本
* 弃用现有版本
* 将版本标为禁用可防止使用者使用该版本
* 指定默认版本
* 从工作区按版本检索模块，确保团队使用相同的代码

请参阅[笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)中的代码示例，了解如何在 Azure 机器学习管道中创建、连接和使用模块。

## <a name="next-steps"></a>后续步骤

Azure ML 管道是一个功能强大的工具，可在早期开发阶段开始产生价值。 价值随着团队和项目的发展而增加。 本文介绍了如何通过 Azure 机器学习 Python SDK 指定管道，并在 Azure 上进行协调。 你已了解一些基本的源代码和一些可用的 `PipelineStep` 类。 你应了解何时使用 Azure ML 管道以及 Azure 如何运行这些管道。 


+ 了解如何[创建你的第一个管道](how-to-create-your-first-pipeline.md)。

+ 了解如何[对大数据运行批量预测](tutorial-pipeline-batch-scoring-classification.md )。

+ 若要了解[管道核心](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py)和[管道步骤](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)，请参阅 SDK 参考文档。

+ 试用展示了 [Azure 机器学习管道](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)的示例 Jupyter 笔记本。 了解如何[运行笔记本以了解此服务](samples-notebooks.md)。
