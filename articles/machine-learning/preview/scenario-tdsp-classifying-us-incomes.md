---
title: "收入分类 - Team Data Science Process - Azure 机器学习 | Microsoft Docs"
description: "如何使用 Team Data Science Process 模板在 Azure 机器学习中创建对美国收入分类的项目。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: dc6279a1bac230146f4f0cebf2fbdbb6333cb7a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>使用 Team Data Science Process (TDSP) 项目进行收入分类

## <a name="introduction"></a>介绍

实现数据科学项目结构和文档的标准化，从而建立[数据科学生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)，对于促进数据科学团队的有效协作很重要。 使用 [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) 模板创建 Azure 机器学习项目可以为此类标准化提供一个框架。

我们此前已发布[适用于 TDSP 项目结构和模板的 GitHub 存储库](https://github.com/Azure/Azure-TDSP-ProjectTemplate)， 但直到现在才能够在数据科学工具中实例化 TDSP 结构和模板。 我们现在已允许用户创建通过[适用于 Azure 机器学习的 TDSP 结构和文档模板](https://github.com/amlsamples/tdsp)来实例化的 Azure 机器学习项目。 有关如何在 Azure 机器学习中使用 TDSP 结构和模板的说明，请参阅[此文](https://aka.ms/how-to-use-tdsp-in-aml)。 我们在这里提供了一个示例，说明了如何使用 TDSP 结构来创建实际的机器学习项目，并使用特定于项目 (project) 的代码、项目 (artifact) 和文档对其进行填充，然后在 Azure 机器学习中执行。

## <a name="link-to-github-repository"></a>GitHub 存储库链接
我们在[此处](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)提供有关该示例的摘要文档。 GitHub 站点上提供内容更广泛的文档。

### <a name="purpose"></a>目的
此示例的主要目的是介绍如何在 Azure 机器学习中使用 [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) 结构和模板来实例化和执行机器学习项目。 为此，我们使用众所周知的 [UCI 机器学习存储库中的 1994 年美国人口普查数据](https://archive.ics.uci.edu/ml/datasets/adult)。 建模任务是根据美国人口普查信息（例如，年龄、种族、教育程度、原籍国等）预测美国的年收入阶层。

### <a name="scope"></a>范围
 * 机器学习模型的数据浏览、训练和部署，目的是解决“用例概述”中描述的预测问题。 
 * 使用 Azure 机器学习为本项目提供的 Team Data Science Process (TDSP) 模板在 Azure 机器学习中执行本项目。 我们将根据 TDSP 生命周期来执行和报告项目。
 * 直接从 Azure 容器服务中的 Azure 机器学习实现解决方案的操作化。

 本项目凸显了 Azure 机器学习的多项功能，例如实例化和使用 TDSP 结构、执行 Jupyter Notebook 和 Python 文件中的代码，以及使用 Docker 和 Kubernetes 在 Azure 容器服务中轻松实现操作化。


## <a name="team-data-science-process-tdsp-lifecycle"></a>Team Data Science Process (TDSP) 生命周期
请参阅 [Team Data Science Process (TDSP) Lifecycle](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)（Team Data Science Process (TDSP) 生命周期）

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>先决条件
### <a name="required-subscription-hardware-software"></a>必需：订阅、硬件、软件
1. Azure [订阅](https://azure.microsoft.com)。 也可获取执行此示例所需的[免费订阅](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg)。
2. [Azure 数据科学虚拟机 (DSVM) Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm)，（VM 大小：[DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，使用 4 个虚拟 CPU 和 14-Gb RAM）。 虽然是在 Azure DSVM 上进行的测试，但应该也能在任何 Windows 10 计算机上使用。
3. 查看 Azure 机器学习及其相关服务的文档（链接见下）。
4. 确保已根据[快速入门安装指南](quickstart-installation.md)正确安装 Azure 机器学习。

此示例的数据集来自 UCI ML 存储库 [[链接]](https://archive.ics.uci.edu/ml/datasets/adult)。 该数据集取自 1994 年美国人口普查数据库，包含大约 50,000 人的人口普查和收入信息。 该数据集是具有数字特征和分类特征的结构化数据集，此外还有一个分类目标，包含两个收入类别（“>50 K”或“<=50 K”）。 

### <a name="optional-version-control-repository"></a>可选：版本控制存储库
若要保存项目及其内容并设置其版本，需要有一个版本控制存储库，在其中执行相关操作。 在 Azure 机器学习中，可以在使用 TDSP 模板创建新项目时输入 Git 存储库位置。 有关更多详细信息，请参阅[如何在 Azure 机器学习中使用 Git](using-git-ml-project.md)。

### <a name="informational-about-azure-machine-learning"></a>信息：了解 Azure 机器学习
* [常见问题解答 - 如何入门](frequently-asked-questions.md)
* [概述](overview-what-is-azure-ml.md)
* [安装](quickstart-installation.md)
* [执行](experimentation-service-configuration.md)
* [使用 TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [读取和写入文件](how-to-read-write-files.md)
* [将 Git 与 Azure 机器学习配合使用](using-git-ml-project.md)
* [将 ML 模型作为 Web 服务部署](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

将本示例用作模板，创建新的项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“美国收入分类 - TDSP 项目”并选择模板
5.  单击“创建” 

如果在创建项目的过程中（在相应的框中）提供了空的 Git 存储库位置，则在创建项目以后，系统会为该存储库填充项目结构和内容。

## <a name="use-case-overview"></a>用例概述
问题在于了解如何通过在美国人口普查中捕获的社经数据来预测美国的个人年收入。 机器学习任务是根据此类人口普查特征，预测个人收入是否高于 50,000 美元（二元分类任务）。

## <a name="data-description"></a>数据说明
有关数据的详细信息，请参阅 UCI 存储库中的[说明](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names)。 

该数据取自人口普查局数据库：https://www.census.gov/en.html。 


* 总共有 48,842 个实例（筛选之前），混合了连续实例和离散实例（训练=32,561，测试=16,281）
* 标签“>50 K”的概率：23.93%/24.78%（没有未知项）
* 标签“<=50 K”的概率：76.07%/75.22%（没有未知项）  

* 目标：收入阶层“>50 K”、“<=50 K”。 这两个阶层在数据准备阶段被分别替换为 1 和 0。
* **特征**：年龄、工作类、教育程度、种族、性别、每周工作时数等。


## <a name="project-structure-execution-and-reporting"></a>项目结构、执行和报告

### <a name="structure"></a>结构
对于本项目，我们使用遵循 [TDSP 生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)的 TDSP 文件夹结构和文档模板（见下）。 

项目是根据[此处](https://aka.ms/how-to-use-tdsp-in-aml)提供的说明创建的。 为其填充项目 (project) 的代码和项目 (artifact) 后，结构将如下所示（参见下图中用红色框住的项目结构）。


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>执行
在本示例中，我们在**本地计算环境**中执行代码。 有关[执行选项](experimentation-service-configuration.md)的更多详细信息，请参阅 Azure 机器学习文档。

在本地 Python 运行时中执行 Python 脚本很容易：

    az ml experiment submit -c local my_script.py

可以在 Azure 机器学习 UI 左侧的项目结构中双击 IPython Notebook 文件，然后让其在 Jupyter Notebook Server 中运行。


分步数据科学工作流如下所示：

* [数据采集和理解](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

数据已采用 .csv 格式从 UCI ML 存储库 [[链接]](https://archive.ics.uci.edu/ml/datasets/adult) 的 URL 下载。 特征、目标及其转换在 ProjectReport.md 文件中详细介绍。

有助于数据获取和理解的代码位于：/code/01_data_acquisition_and_understanding。

数据浏览使用 Python 3 [IDEAR（交互式数据浏览和报告）实用程序](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python)来执行，该实用程序已作为 [TDSP 数据科学工具套件](https://github.com/Azure/Azure-TDSP-Utilities)的一部分发布。 使用此实用程序，可以针对那些包含数字与分类功能和目标的数据生成标准化数据浏览报告。 下面详细介绍了如何使用 Python 3 IDEAR 实用程序。 

最终数据浏览报告位于 [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs)。 IDEAR 报告的视图如下所示：

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [建模](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

我们采用 3 重交叉验证创建了两个模型：“弹性网络”和“随机林”。 我们使用 [59 点采样](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf)作为交叉验证和模型参数优化策略进行了随机网格搜索。 模型准确性使用 AUC（曲线下面积）在测试数据集上进行了衡量。 

建模的代码位于：/code/02_modeling。

“弹性网络”和“随机林”模型的 AUC 均大于 0.85。 我们将两种模型保存在 pickled.pkl 文件中，并输出两种模型的 ROC 图。 “随机林”模型的 AUC 为 0.92，“弹性网络”模型的 AUC 为 0.90。 另外，就模型解释来说，适用于“随机林”模型的特征重要度以 .csv 文件格式输出，并以 pdf 格式绘制（仅限前 20 个预测特征）。

基于测试数据的**“随机林”模型**的 ROC 曲线如下所示。 这是已部署的模型：

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

“随机林”模型的特征重要度（前 20 个）如下所示。 下图表明，资本收益额、教育、婚姻状态等特征的特征重要度最高。

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [部署](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

我们在 [Azure 容器服务 (ACS)](https://azure.microsoft.com/services/container-service/) 的群集上部署了“随机林”模型作为 Web 服务。 操作化环境在群集中预配 Docker 和 Kubernetes 来管理 Web 服务部署。 可以在[此处](model-management-service-deploy.md)找到有关操作化过程的更多信息。 

部署代码位于：/code/03_deployment。


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[最终项目报告](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
编译版的最终项目报告 [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md) 提供了以上各部分的详细信息。 项目报告还包含更多的详细信息，包括用例、模型性能指标、部署，以及进行项目开发和部署所依赖的基础结构。

可以将项目报告以及整个项目文件夹的内容和版本控制存储库传送到客户端。


## <a name="conclusion"></a>结束语

本示例介绍了如何在 Azure 机器学习中使用 TDSP 结构和模板。 文档和项目模板用于：
1. 正确定义项目的用途和范围
2. 创建具有分布式角色和职责的项目团队
3. 按照 TDSP 生命周期阶段设置项目的结构和执行项目
4. 使用 TDSP 数据科学实用程序制定标准化报告（例如 IDEAR 数据浏览和可视化报告）。
5. 准备可以传送到客户端的数据科学项目最终报告

我们希望你使用 Azure 机器学习的此功能，加强你所在的数据科学团队的标准化和协作事项。

## <a name="next-steps"></a>后续步骤

请查看下面的入门级参考资料：

[如何在 Azure 机器学习中使用 Team Data Science Process (TDSP)](https://aka.ms/how-to-use-tdsp-in-aml)

[Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP)

[适用于 Azure 机器学习的 TDSP 项目模板](https://aka.ms/tdspamlgithubrepo)

[UCI ML 存储库中的美国收入数据集](https://archive.ics.uci.edu/ml/datasets/adult)