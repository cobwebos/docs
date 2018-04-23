---
title: 生物医学实体识别 - Team Data Science Process - Azure 机器学习 | Microsoft 文档
description: Team Data Science Process 项目快速入门，在 Azure Machine Learning Workbench 中使用深度学习了解生物医学实体识别。
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 421af12da6d207a50d12dcbf64aac51aba06b196
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>使用 Team Data Science Process (TDSP) 模板的生物医学实体识别

实体提取是信息提取的一项子任务（又称为[命名实体识别 (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition)、实体分块和实体识别）。 这个真实场景旨在重点介绍如何使用 Azure 机器学习工作台来解决复杂的自然语言处理 (NLP) 任务，如非结构化文本的实体提取：

1. 如何使用 [Spark Word2Vec 实现](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec)在大约有 1,800 万条 PubMed 摘要的文本语料库中培训一个神经词嵌入模型。
2. 如何在 Azure 中支持 GPU 的 Azure 数据科学虚拟机 (GPU DS VM) 上构建用于实体提取的深度长短期记忆 (LSTM) 循环神经网络模型。
2. 演示特定于域的词嵌入模型在实体识别任务中的性能可以优于一般的词嵌入模型。 
3. 演示如何使用 Azure Machine Learning Workbench 训练和实施深度学习模型。

4. 在 Azure Machine Learning Workbench 中演示以下功能：

    * 实例化 [Team Data Science Process (TDSP) 结构和模板](how-to-use-tdsp-in-azure-ml.md)
    * 对项目依赖项的自动管理，包括下载和安装
    * 在不同计算环境中执行 Python 脚本
    * 对 Python 脚本运行历史记录跟踪
    * 使用 HDInsight Spark 2.1 群集在远程 Spark 计算上下文中执行作业
    * 在 Azure 的远程 GPU VM 中执行作业
    * 在 Azure 容器服务 (ACS) 中轻松将深度学习模型实施为 Web 服务

## <a name="use-case-overview"></a>用例概述
生物医学命名实体识别是复杂生物医学 NLP 任务的关键步骤，如： 
* 从电子医疗或健康记录中提取命名实体的有关记录，例如疾病、药物、化学品和症状等。
* 药品开发
* 了解不同实体类型之间的相互作用，如药物间相互作用、药物与疾病的关系，以及基因与蛋白质的关系。

我们的用例情景集中在如何分析大量非结构化数据语料库（如 Medline PubMed 摘要），以训练词嵌入模型。 然后，输出嵌入被认为是自动生成的特征来训练神经实体提取器。

我们的结果表明，针对特定于域的词嵌入功能的生物医学实体提取模型训练优于通用特征类型训练的模型。 特定于域的模型可以正确检测 7012 个实体（共 9475 个），F1 分数为 0.73，而通用模型的 5274 个实体的 F1 分数为 0.61。

下图显示了用于处理数据和训练模型的体系结构。

![体系结构](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>数据说明

### <a name="1-word2vec-model-training-data"></a>1.Word2Vec 模型训练数据
我们首先从 [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html) 下载了原始 MEDLINE 摘要数据。 数据在其 [FTP 服务器](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline)上以 XML 文件的形式公开提供。 服务器上有 892 个 XML 文件，每个 XML 文件都包含 30,000 篇文章的信息。 有关数据收集步骤的更多详细信息，请参阅“项目结构”部分。 每个文件中存在的字段 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2.LSTM 模型训练数据

神经实体提取模型已针对公开可用的数据集进行训练和评估。 要获得有关这些数据集的详细说明，可以参考以下来源：
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)（BioNLP/NLPBA 2004 中的生物实体识别任务）
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)（BioCreative V CDR 任务语料库）
 * [Semeval 2013 - Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)（Semeval 2013 - 任务 9.1 (药物识别)）

## <a name="link-to-the-azure-gallery-github-repository"></a>链接到 Azure 库 GitHub 存储库
以下是指向包含代码和更详细说明的真实场景的公共 GitHub 存储库的链接： 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>先决条件 

* Azure [订阅](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench。 请参阅[安装指南](../service/quickstart-installation.md)。 目前，Azure Machine Learning Workbench 只能安装在以下操作系统上： 
    * Windows 10 或 Windows Server 2016
    * macOS Sierra（或更高版本）


### <a name="azure-services"></a>Azure 服务
* Linux (HDI 3.6) 上的 [HDInsight Spark 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)版本 Spark 2.1，用于横向扩展计算。 要处理下面讨论的全部 MEDLINE 摘要，需要以下项的最低配置： 
    * 头节点：[D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) 大小
    * 工作节点：至少 4 个 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/)。 我们在工作中使用了 11 个 D12_V2 大小的辅助角色节点。
* [NC6 数据科学虚拟机 (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) 面向扩展计算。

### <a name="python-packages"></a>Python 包

所有必需的依赖关系都在场景项目文件夹下的 aml_config/conda_dependencies.yml 文件中定义。 将自动预配此文件中定义的依赖关系，以针对 Docker、VM 和 HDI 群集目标运行。 有关 Conda 环境文件格式的详细信息，请参阅[此处](https://conda.io/docs/using/envs.html#create-environment-file-by-hand)。

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Azure Machine Learning (AML) Workbench 的基本说明
* [概述](../service/overview-what-is-azure-ml.md)
* [安装](../service/quickstart-installation.md)
* [使用 TDSP](how-to-use-tdsp-in-azure-ml.md)
* [如何读取和写入文件](how-to-read-write-files.md)
* [如何使用 Jupyter 笔记本](how-to-use-jupyter-notebooks.md)
* [如何使用 GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>方案结构
对于此场景，我们使用遵循 [TDSP 生命周期](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)的 TDSP 项目结构和文档模板（图 1）。 根据[此处](./how-to-use-tdsp-in-azure-ml.md)提供的说明创建项目。


![填写项目信息](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

分步数据科学工作流程如下所示：

### <a name="1-data-acquisition-and-understanding"></a>1.数据获取和理解

请参阅[数据采集和理解](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md)。

原始的 MEDLINE 语料库共有 2,700 万条摘要，其中大约 1,000 万篇文章包含空的摘要字段。 Azure HDInsight Spark 用于处理大数据，大数据无法作为 [Pandas DataFrame ](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) 加载到单个计算机的内存中。 首先，将数据下载到 Spark 群集。 然后在 [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) 上执行以下步骤： 
* 使用 Medline XML 分析程序分析 XML 文件
* 预处理摘要文本，包括句子拆分、词汇切分和大小写规范化。
* 排除摘要字段为空或具有短文本的文章 
* 基于训练摘要创建词汇表
* 训练词嵌入神经模型。 有关详细信息，请参阅 [GitHub 代码链接](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md)以开始使用。


分析 XML 文件之后，数据采用以下格式： 

![数据示例](./media/scenario-tdsp-biomedical-recognition/datasample.png)

神经实体提取模型已针对公开可用的数据集进行训练和评估。 要获得有关这些数据集的详细说明，可以参考以下来源：
 * [Bio-Entity Recognition Task at BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)（BioNLP/NLPBA 2004 中的生物实体识别任务）
 * [BioCreative V CDR task corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)（BioCreative V CDR 任务语料库）
 * [Semeval 2013 - Task 9.1 (Drug Recognition)](https://www.cs.york.ac.uk/semeval-2013/task9/)（Semeval 2013 - 任务 9.1 (药物识别)）

### <a name="2-modeling"></a>2.建模

请参阅[建模](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling)。

建模是我们展示如何使用上一节中下载的数据来培训自己的词嵌入模型并将其用于其他下游任务的阶段。 虽然我们正在使用 PubMed 数据，但生成嵌入内容的管道是通用的，可以重用于任何其他域的词嵌入。 为了使嵌入内容成为准确的数据表示，必须对 word2vec 进行大量数据的训练。
我们准备好词嵌入后，就可以训练一个使用学习嵌入的深度神经网络模型来初始化嵌入层。 我们将嵌入层标记为不可训练、但不是必需的层。 词嵌入模型的训练无需监督，因此我们可以利用未标记的文本。 但是，实体识别模型的训练是受监督的学习任务，其准确性取决于手动批注数据的数量和质量。 


#### <a name="21-feature-generation"></a>2.1. 特征生成

请参阅[特征生成](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering)。

Word2Vec 是未标记的训练语料库中训练神经网络模型的词嵌入非监督式学习算法。 它为语料库中的每个单词生成一个代表其语义信息的连续矢量。 这些模型是具有一个隐藏层的简单神经网络。 通过反向传播算法和随机梯度下降来学习词汇矢量/嵌入词。 有两种类型的 word2vec 模型，即 Skip-Gram 和连续的词袋模型（有关更多详细信息，请查看[此处](https://arxiv.org/pdf/1301.3781.pdf)）。 由于我们正在使用 MLLIB 的支持 Skip-gram 模型的 word2vec 的实现，因此我们在此做一个简要描述（从[链接](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)获得的图像）： 

![Skip Gram 模型](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

该模型使用分层 Softmax 和负采样来优化性能。 分层 SoftMax (H-SoftMax) 是受二叉树启发的近似值。 H SoftMax 基本上是将平面的 SoftMax 图层替换为将词用作叶子的分层。 这使得我们能够将计算一个词的概率分解成一系列的概率计算，这样就无需为所有词计算费用高昂的规范化。 由于平衡二叉树具有 log2(|V|)（V 表示词汇表）的深度，我们至多仅需要评估 log2(|V|) 节点以获得词的最终概率。 给定其上下文 c 的词 w 的概率仅仅是分别导致其叶节点的右转和左转的概率乘积。 我们可以根据词在数据集的频率构建霍夫曼树，以确保更频繁出现的词呈现较短的表示形式。 有关详细信息，请参阅[此链接](http://sebastianruder.com/word-embeddings-softmax/)。
从[此处](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)获得的图像。

##### <a name="visualization"></a>可视化

有了词嵌入后，我们就可以可视化它们并看到语义上相似的词之间的关系。 

![W2V 相似性](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

我们已经演示了两种不同的可视化嵌入内容的方式。 第一种使用 PCA 将高维向量投影到二维向量空间。 这导致显著的信息损失，并且可视化不是同样准确。 第二种使用 PCA 与 [t-SNE](https://distill.pub/2016/misread-tsne/)。 t-SNE 是一种非线性降维技术，非常适合于将高维数据嵌入到二维或三维空间中，然后可以在散点图中进行可视化。  它按照二维或三维点来对每个高维对象进行建模，使得相似的对象由附近的点建模，并且由不同的点对不同的对象进行建模。 它分两部分工作。 首先，它创建高维空间中的对值概率分布，其方式是类似对象具有很高的抽取概率，而不同点的抽取概率较低。 其次，它定义了低维图中点的相似概率分布，并且相对于点在图上的位置使两个分布之间的 KL 散度最小化。 通过使用梯度下降使 KL 散度最小化来获得点在低维图中的位置。 但 t-SNE 有时并不可靠。 可以在[此处](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering)找到实施详细信息。 


如下图所示，t-SNE 可视化为词矢量和潜在群集模式提供了更多的分离。 


* 使用 PCA 进行可视化

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* 使用 t-SNE 进行可视化

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* 最接近“癌症”的点（它们都是癌症的子类型）

![最接近“癌症”的点](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. 训练神经实体提取器

请参阅[训练神经实体提取器](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md)。

前馈神经网络体系结构存在的问题是，它们将每个输入和输出都视为独立于其他输入和输出。 该体系结构不能对诸如机器翻译和实体提取之类的序列到序列标签任务进行建模。 循环神经网络模型克服了这个困难，因为它们可以将到目前为止计算的信息传递到下一个节点。 该属性在网络中被认为是有记忆的，因为它能够使用以前计算的信息，如下图所示：

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Vanilla RNN 实际上会受到[梯度消失问题](https://en.wikipedia.org/wiki/Vanishing_gradient_problem)的困扰，因为它们无法利用以前发现的所有信息。 只有当需要大量的上下文进行预测时，问题才会变得明显。 但像 LSTM 这样的模型并不会遇到这样的问题，实际上它们是为了记住长期的依赖关系。 不同于具有单个神经网络的 Vanilla RNN，LSTM 每个单元都存在四个神经网络之间的相互作用。 有关 LSTM 如何工作的详细解释，请参阅[这篇帖子](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)。

![LSTM 单元](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

我们尝试将自己基于 LSTM 的循环神经网络组合在一起，并尝试从 PubMed 数据中提取药物、疾病和症状等实体类型。 第一步是获得大量的标签数据，就像你猜到的那样，这并不容易！ 大多数的医疗数据都包含许多关于患者的敏感信息，因此不是公开的。 我们依靠公开的两个不同数据集的组合。 第一个数据集来自 Semeval 2013 - 任务 9.1（药物识别），另一个来自 BioCreative V CDR 任务。 我们正在组合和自动标注这两个数据集，以便我们可以从医学文本中检测出药物和疾病，并评估我们的词嵌入。 有关实施详细信息，请参阅 [GitHub 代码链接](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation)

我们在所有代码和比较中使用的模型体系结构如下所示。 对于不同数据集，发生变化的参数是最大序列长度（这里为 613）。

![LSTM 模型](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. 模型评估
请参阅[模型评估](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md)。

我们使用共享任务 [Bio NLP/NLPBA 2004 中的生物实体识别任务](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)来评估模型的精度、记忆力和 F1 分数。 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>域内与通用词嵌入模型

以下是两种特征类型的准确性之间的比较：(1) 在 PubMed 摘要中训练的词嵌入和 (2) 在Google新闻中训练的词嵌入。 我们清楚地看到，域内模型优于通用模型。 因此，使用特定的词嵌入模型而不是通用模型更有帮助。 

* 任务 #1：药物和疾病检测

![模型比较 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

我们以类似的方式对其他数据集的词嵌入进行评估，并且发现域内模型始终都更好一些。

* 任务 #2：蛋白质、细胞系、细胞类型、DNA 和 RNA 检测

![模型比较 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* 任务 #3：化学与疾病检测

![模型比较 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* 任务 #4：药物检测

![模型比较 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* 任务 #5：基因检测

![模型比较 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow 与 CNTK
所有报告模型都使用 Keras 和作为后端的 TensorFlow 进行训练。 具有 CNTK 后端的 Keras 在完成此工作时不支持“反向”。 因此，为了比较，我们已经通过 CNTK 后端训练了单向 LSTM 模型，并将其与具有 TensorFlow 后端的单向 LSTM 模型进行了比较。 从[此处](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)安装 CNTK 2.0 for Keras。 

![模型比较 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

我们得出的结论是，CNTK 在每个时期花费的训练时间（CNTK 为 60 秒，Tensorflow 为 75 秒）和检测到的测试实体数量方面的表现与 Tensorflow 一样好。 我们将使用单向层进行评估。


### <a name="3-deployment"></a>3.部署

请参阅[部署](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment)。

我们在 [Azure 容器服务 (ACS)](https://azure.microsoft.com/services/container-service/) 的群集上部署了 Web 服务。 实施环境在群集中提供了 Docker 和 Kubernetes 来管理 Web 服务部署。 可以在[此处](model-management-service-deploy.md )找到有关实施过程的更多信息。


## <a name="conclusion"></a>结束语

我们详细介绍了如何使用 Spark 上的 Word2Vec 算法训练词嵌入模型，然后使用提取的嵌入内容作为特征来训练深度神经网络以进行实体提取。 我们在生物医学领域应用了训练管道。 但是，该管道足够通用，可用于检测任何其他领域的自定义实体类型。 你只需有足够的数据，即可轻松调整此处提供的面向不同领域的工作流程。

## <a name="references"></a>参考

* Tomas Mikolov、Kai Chen、Greg Corrado 和 Jeffrey Dean。 2013a. Efficient estimation of word representations in vector space（有效评估矢量空间中的词表示形式）。 ICLR 论文集。
* Tomas Mikolov、Ilya Sutskever、Kai Chen、Greg S Corrado 和 Jeff Dean。 2013b. Distributed representations of words and phrases and their compositionality（单词和短语的分布式表示形式及其组成）。 ICLR 论文集第 3111–3119 页。
* Billy Chiu、Gamal Crichton、Anna Korhonen 和 Sampo Pyysalo。 2016. 第十五届生物医学自然语言处理研讨会论文集第 166-174 页中的 [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf)（如何训练良好的生物医学 NLP 词嵌入模型）。
* [词的矢量表示形式](https://www.tensorflow.org/tutorials/word2vec)
* [循环神经网络](https://www.tensorflow.org/tutorials/recurrent)
* [使用 Spark ml Word2Vec 遇到的问题](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec：经验教训](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

