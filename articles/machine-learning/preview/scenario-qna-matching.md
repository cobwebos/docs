---
title: "使用 Azure Machine Learning Workbench 进行问答匹配 | Microsoft Docs"
description: "如何使用各种有效的机器学习方法，以便将开放式查询与预先存在的常见问题解答的问答对进行匹配。"
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
ms.manager: tihazen
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8edc21fb8f42ee5897c4e938045cc1f42aedb3ce
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>使用 Azure Machine Learning Workbench 进行问答匹配
回答开放式问题很难，通常需要行业专家 (SME) 提供人力帮助。 公司通常会创建常见问题解答 (FAQ) 的列表，作为一种协助用户的手段，同时也可以降低对内部 SME 的需求。 本示例介绍了各种有效的机器学习方法，将开放式查询与预先存在的常见问题解答的问答对进行匹配。 本示例演示了如何通过简单的开发过程，使用 Azure Machine Learning Workbench 生成此类解决方案。 

## <a name="link-to-the-gallery-github-repository"></a>库 GitHub 存储库的链接
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>概述

本示例解决的问题是，如何将用户问题映射到预先存在的问答对。这些问答对通常在常见问题解答（简称 FAQ）列表或网站（例如 [Stack Overflow](https://stackoverflow.com/)）上存在的问答对中提供。 可以通过多种方法将问题与正确的答案进行匹配，例如查找与问题最相似的答案。 但在本示例中，将开放式问题与以前提出的问题进行匹配时有一个假设，即常见问题解答中的每个解答可以解答多个在语义上等效的问题。

交付此解决方案所需的关键步骤如下：

1. 清除和处理文本数据。
2. 学习信息性短语。此类短语是包含多个单词的序列，其在按顺序解读时提供的信息多于单个对待时提供的信息。
3. 从文本数据中提取特征。
4. 训练文本分类模型并评估模型性能。


## <a name="prerequisites"></a>先决条件

运行本示例的先决条件如下：

1. [Azure 帐户](https://azure.microsoft.com/free/)（提供免费试用版）。
2. 按照[快速入门安装指南](./quickstart-installation.md)安装 [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) 的副本，以安装程序并创建工作区。
3. 本示例可以在任何计算上下文中运行。 但是，建议在内存至少为 16 GB 且磁盘空间至少为 5 GB 的多核计算机中运行本示例。

## <a name="create-a-new-workbench-project"></a>创建新的 Workbench 项目

将本示例用作模板，创建新的项目：
1.  打开 Azure Machine Learning Workbench
2.  在“项目”页上单击 + 号，然后选择“新建项目”
3.  在“新建项目”窗格中，填写新项目的信息
4.  在“搜索项目模板”搜索框中，键入“问答匹配”并选择模板
5.  单击“创建” 

## <a name="data-description"></a>数据说明

本示例使用的数据集为 Stack Exchange Data Dump，存储在 [archive.org](https://archive.org/details/stackexchange) 中。该数据为匿名转储，包含用户在 [Stack Exchange 网络](https://stackexchange.com/)上贡献的所有内容。 网络中的每个站点都被格式化为一个单独的存档，其中包含通过 7-zip 使用 bzip2 压缩的 XML 文件。 每个站点存档都包括帖子、用户、投票、评论、PostHistory 和 PostLink。 

### <a name="data-source"></a>数据源

本示例使用 Stack Overflow 站点提供的[帖子数据 (10 GB)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) 和 [PostLink 数据 (515 MB)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z)。 有关完整的架构信息，请参阅 [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt)。 

帖子数据中的 `PostTypeId` 字段指示帖子是 `Question` 还是 `Answer`。 PostLink 数据中的 `PostLinkTypeId` 字段指示两个帖子是关联的还是重复的。 问题帖子通常包括一些标记，这些标记是关键字，用于将某个问题与其他类似/重复的问题分门别类。 某些标记（例如 `javascript`、`java`、`c#`、`php` 等）的出现频率较高，包含的问题帖子的数量更大。 本示例提取了一部分标记为 `javascript` 的问答对。

另外，一个问题帖子可能与多个解答帖子或重复的问题帖子相关。 若要构造一个列表，其中包含这两个数据集中的常见问题解答，需考虑一些数据收集条件。 三组已编译数据是使用 SQL 脚本选择的，本示例中不包括该脚本。 生成的数据说明如下：

- `Original Questions (Q)`：在 Stack Overflow 站点上以问题帖子的形式提出和解答问题。
- `Duplications (D)`：问题帖子与其他预先存在的问题 (`PostLinkTypeId = 3`) 重复，后者为原始问题。 重复是指在语义上等效于原始问题，即针对原始问题提供的解答也可解答新的重复问题。
- `Answers (A)`：每个原始问题及其重复问题可能与多个解答帖子相关联。 本示例仅选择那些被原始作者接受的解答帖子（按 `AcceptedAnswerId` 筛选），或者在原始问题中得分最高的解答帖子（按 `Score` 排名）。 

将这三个数据集组合在一起即可创建问答对，其中，一个解答 (A) 映射到一个原始问题 (Q) 和多个重复问题 (D)，如以下数据图所示：

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>数据结构

这三个数据集的数据架构和直接下载链接见下表：

| 数据集 | 字段 | 类型 | 说明
| ----------|------------|------------|--------
| [问题](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | String | 唯一的问题 ID（主键）
|  | AnswerId | String | 每个问题的唯一解答 ID
|  | Text0 | String | 原始文本数据，包括问题的标题和正文
|  | CreationDate | Timestamp | 表示提问时间的时间戳
| [重复项](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | String | 唯一的重复项 ID（主键）
|  | AnswerId | String | 与重复项关联的解答 ID
|  | Text0 | String | 原始文本数据，包括重复项的标题和正文
|  | CreationDate | Timestamp | 表示重复项提问时间的时间戳
| [解答](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | String | 唯一解答 ID（主键）
|  | text0 | String | 解答的原始文本数据


## <a name="scenario-structure"></a>方案结构

问答匹配示例通过三种类型的文件提供。 第一种类型是一系列 Jupyter Notebook，显示整个工作流的分步说明。 第二种类型是一组 Python 文件，其中包含用于短语学习和特征提取的 Python 模块。 这些 Python 模块很通用，既能用于本示例，也能用于其他用例。 第三种类型是一组 Python 文件，使用 Azure Machine Learning Workbench 来优化超参数和跟踪模型性能。

本示例中的文件按如下方式组织。

| 文件名 | 类型 | 说明
| ----------|------------|--------
| `Image` | 文件夹 | 用于保存自述文件的图像的文件夹
| `notebooks` | 文件夹 | Jupyter Notebooks 文件夹
| `modules` | 文件夹 | Python 模块文件夹
| `scripts` | 文件夹 | Python 文件文件夹
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter 笔记本 | 访问示例数据、预处理文本，以及准备训练和测试数据集
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter 笔记本 | 学习信息性短语，并将文本标记化为词袋 (BOW) 表示形式。
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter 笔记本 | 提取特征、训练文本分类模型并评估模型性能
| `modules/__init__.py` | Python 文件 | Python 包 init 文件
| `modules/phrase_learning.py` | Python 文件 | Python 模块，用于转换原始数据和学习信息性短语
| `modules/feature_extractor.py` | Python 文件 | Python 模块提取用于模型训练的特征
| `scripts/naive_bayes.py` | Python 文件 | Python 脚本，用于在 Naive Bayes 模型中优化超参数
| `scripts/random_forest.py` | Python 文件 | Python 脚本，用于在“随机林”模型中优化超参数
| `README.md` | Markdown 文件 | 自述 Markdown 文件

> [!NOTE]
> 此系列的 Notebook 是在 Python 3.5 下生成的。
> 

### <a name="data-ingestion-and-transformation"></a>数据引入和转换

这三个编译的数据集在 Blob 存储中存储，在 `Part_1_Data_Preparation.ipynb` Notebook 中检索。  

在训练文本分类模型之前，会对问题中的文本进行清理和预处理，排除代码片段。 将会对训练材料应用非监督式短语学习，学习包含多个单词的信息性序列。 在文本分类模型所使用的下游词袋 (BOW) 特征化中，这些短语表示为单个复合单词单位。

有关文本预处理和短语学习的详细分步说明，可分别参阅 Notebook `Part_1_Data_Preparation.ipynb` 和 `Part_2_Phrase_Learning.ipynb`。

### <a name="modeling"></a>建模

本示例旨在根据预先存在的问答对对新问题评分，方法是训练文本分类模型。这些模型中每个预先存在的问答对都是一个唯一的类，而每个问答对的一部分重复问题则作为训练材料提供。 在示例中，原始问题和 75% 的重复问题保留为训练之用，而 25% 的重复问题（最近发布）则作为评估数据保留。

分类模型使用集成方法来聚合三个基分类器，其中包括“Naive Bayes”、“支持矢量机”、“随机林”。 在每个基分类器中，`AnswerId` 用作类标签，BOW 表示形式用作特征。

模型训练过程如 `Part_3_Model_Training_and_Evaluation.ipynb` 中所示。

### <a name="evaluation"></a>计算

使用两个不同的指标来评估性能。 
1. `Average Rank (AR)`：表示正确解答位于已检索问答对的列表中（总共 103 个解答类）时的平均排名。 
2. `Top 3 Percentage`：表示正确解答可以在已返回排名表的头三个选择中检索到的测试问题的百分比。 

评估在 `Part_3_Model_Training_and_Evaluation.ipynb` 中演示。


## <a name="conclusion"></a>结束语

本示例重点介绍了如何使用已知的文本分析技术（例如短语学习和文本分类）来生成可靠的模型， 同时还演示了如何借助 Azure Machine Learning Workbench 进行交互式解决方案开发和模型性能跟踪。 

本示例的部分要点概括如下：

- 可以通过短语学习和文本分类模型有效地解决问答匹配问题。
- 使用 Azure Machine Learning Workbench 和 Jupyter Notebook 演示交互式模型开发。
- Azure Machine Learning Workbench 可以记录用于比较的评估指标，从而管理运行历史记录和已学习的模型。 可以借助这些特征来快速优化超参数，并确定性能最佳的模型。


## <a name="references"></a>参考

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) in IEEE Transactions on Audio, Speech, and Language Processing, vol. 19, no. 8, pp. 2451-2460, Nov. 2011.

