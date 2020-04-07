---
title: 探索 Linux
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何使用 Linux Data Science Virtual Machine 执行多种常见数据科学任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 1298d5fe1d7407e836f454c2130a913dde6f8eec
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755146"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>Azure 上的 Linux Data Science Virtual Machine 中的数据科学

本演练介绍如何使用 Linux Data Science Virtual Machine (DSVM) 执行多种常见数据科学任务。 Linux DSVM 是 Azure 提供的虚拟机映像，其中预安装了一组常用于执行数据分析和机器学习的工具。 [预配 Linux Data Science Virtual Machine](linux-dsvm-intro.md) 中逐项列出了主要的软件组件。 DSVM 映像允许在几分钟之内轻松开始执行数据科学任务，而无需逐个安装和配置每个工具。 可以根据需要轻松扩展 DSVM，还可以在不使用时将其停用。 DSVM 资源既具有弹性，又具有成本效益。

在本演练中演示的数据科学任务遵循了[什么是团队数据科学过程？](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)中所概述的步骤 团队数据科学过程是数据科学的系统性方法，允许数据科学家团队在构建智能应用程序的生命周期内有效地协作。 数据科学过程还为数据科学提供了可供个人遵循迭代框架。

在本演练中，我们对 [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 数据集进行了分析。 Spambase 是一组标记为 spam 或 ham（非 spam）的电子邮件。 Spambase 还包含有关电子邮件内容的一些统计信息。 我们稍后将在本演练中讨论这些统计信息。

## <a name="prerequisites"></a>先决条件

必须具备以下先决条件，才能使用 Linux DSVM：

* **Azure 订阅**。 若要获取 Azure 订阅，请参阅[立即创建免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* [**Linux数据科学虚拟机**](https://azure.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu)。 有关预配虚拟机的信息，请参阅[预配 Linux Data Science Virtual Machine](linux-dsvm-intro.md)。
* 计算机上安装了 [X2Go](https://wiki.x2go.org/doku.php) 且 XFCE 会话处于打开状态****。 有关详细信息，请参阅[安装和配置 X2Go 客户端](linux-dsvm-intro.md#x2go)。
* 为获得更流畅的滚动体验，请在 DSVM 的 Firefox Web 浏览器中切换 `about:config` 中的 `gfx.xrender.enabled` 标记。 [了解详细信息](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 此外，考虑将 `mousewheel.enable_pixel_scrolling` 设置为 `False`。 [了解详细信息](https://support.mozilla.org/questions/981140)。
* Azure 机器学习帐户****。 如果没有帐户，请在 [Azure 机器学习主页](https://azure.microsoft.com/free/services/machine-learning//)上注册新帐户。

## <a name="download-the-spambase-dataset"></a>下载 spambase 数据集

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 数据集是一组相对较小的数据，仅包含 4,601 个示例。 该数据集的大小很适合演示 DSVM 的一些主要功能，因为它对资源的要求适中。

> [!NOTE]
> 本演练是使用 D2 v2 大小 Linux DSVM（Ubuntu 18.04 版本）创建的。 可以使用此大小的 DSVM 来执行本演练中所演示的过程。

如果需要更多存储空间，可以创建额外的磁盘，然后将它们附加到 DSVM。 这些磁盘使用 Azure 持久存储，这样即使是在服务器被重新调整大小或关闭的情况下，其数据仍会被保留。 若要添加磁盘并将其附加到 DSVM，请执行[将磁盘添加到 Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中的步骤。 添加磁盘的步骤会使用已安装在 DSVM 上的 Azure CLI。 可以完全从 DSVM 中执行这些步骤。 增加存储的另一个选项是使用[Azure 文件](../../storage/files/storage-how-to-use-files-linux.md)。

若要下载数据，打开终端窗口，然后运行以下命令：

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

下载的文件没有标头行。 接下来创建另一个具有标头的文件。 运行以下命令来创建具有适当标头的文件：

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

然后将两个文件相连接：

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

数据集内有多种关于每封电子邮件的统计信息：

* 像**单词\_freq\_WORD**这样的列指示电子邮件中与*WORD*匹配的单词的百分比。 例如，如果**freq\_\_make 单词**为**1**，则电子邮件中所有单词的 1%*都已制作*。
* 列 char\_freq\__CHAR_ 指明电子邮件的所有字符中 CHAR 所占的百分比******。
* **capital\_run\_length\_longest** 是一连串大写字母的最大长度。
* **capital\_run\_length\_average** 是所有连串大写字母的平均长度。
* **capital\_run\_length\_total** 是所有连串大写字母的合计长度。
* **spam** 指示是否将电子邮件视为垃圾邮件（1 = 垃圾邮件，0 = 不是垃圾邮件）。

## <a name="explore-the-dataset-by-using-r-open"></a>使用 R Open 探索数据集

让我们使用 R 来检查数据并执行一些基本的机器学习。DSVM 已预安装了 [Microsoft R Open](https://mran.revolutionanalytics.com/open/)。 预安装版的 R 中的多线程数学库提供了优于单线程版本的性能。 通过使用 CRAN 程序包存储库的快照，R Open 还提供了再现功能。

若要获取本演练中使用的代码示例的副本，请使用 git 克隆 Azure-Machine-Learning-Data-Science 存储库。 Git 已预装在 DSVM 上。 从 git 命令行运行：

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

打开终端窗口，在 R 交互式控制台中启动一个新的 R 会话。 此外还可以使用 DSVM 上预安装的 RStudio。

若要导入数据并设置环境：

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

若要查看有关每列的汇总统计信息：

```R
summary(data)
```

有关数据的不同视图：

```R
str(data)
```

此视图会显示数据集中每个变量的类型以及开始的几个值。

**spam** 列作为一个整数被读取，但实际上它是一个分类变量 （或系数）。 若要设置其类型：

```R
data$spam <- as.factor(data$spam)
```

若要进行一些探索性分析，请使用 [ggplot2](https://ggplot2.tidyverse.org/) 程序包，它是 DSVM 上预安装的适用于 R 的常用绘图库。 根据前面显示的摘要数据，我们已经获得有关感叹号字符的频率的汇总统计信息。 让我们运行以下命令来绘制这些频率：

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

由于零轴会影响绘图的准确性，让我们将其删除：

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

在 1 上面有看起来很有意思的不寻常密度。 让我们只看该数据：

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

然后按垃圾邮件与非垃圾邮件来拆分它：

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

这些示例应有助于绘制类似的绘图和探索其他列中的数据。

## <a name="train-and-test-a-machine-learning-model"></a>训练和测试机器学习模型

让我们来对几个机器学习模型进行定型，按照包含垃圾邮件或非垃圾邮件对数据集中的电子邮件进行分类。 在本部分中，我们会训练决策树模型和随机林模型。 然后，测试预测的准确性。

> [!NOTE]
> DSVM 上已安装了以下代码中使用的 rpart（递归分区和回归树）包**。

首先，让我们将该数据集拆分为训练集和测试集：

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

然后创建决策树，将电子邮件分类：

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

结果如下：

![创建的决策树的关系图](./media/linux-dsvm-walkthrough/decision-tree.png)

若要确定它对训练集的执行情况，请使用下面的代码：

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

若要确定它对测试集的执行情况，请使用下面的代码：

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

让我们也尝试一个随机林模型。 随机森林模型训练大量的决策树并输出一个类，该类是来自所有单个决策树的分类模式。 它们提供了更强大的机器学习方法，因为他们会校正决策树模型过拟合 (overfit) 数据集的趋势。

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>深度学习教程和演练

除了基于框架的示例外，我们还提供了一套综合演练。 这些演练可帮助你快速开始开发域中的深度学习应用程序，如图像和文本/语言理解等。

- [跨不同框架运行神经网络](https://github.com/ilkarman/DeepLearningFrameworks)：一个全面的演练，演示如何将代码从一个框架迁移到另一个框架。 它还演示了如何跨框架比较模型和运行时性能。 

- 构建端到端[解决方案以检测图像中的产品的方法指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：图像检测是一种可以定位图像中的对象并进行分类的技术。 此技术有可能在多个实际生活业务域中带来巨大回报。 例如，零售商可以使用此技术确定客户已从货架上选取哪个产品。 从而，此信息可帮助商店管理产品库存。 

- [音频深度学习](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：本教程演示如何在城市[声音数据集](https://urbansounddataset.weebly.com/)上训练音频事件检测的深层学习模型。 此教程提供有关如何处理音频数据的概述。

- [文本文档分类](https://github.com/anargyri/lstm_han)：本演练演示如何构建和训练两种不同的神经网络体系结构：分层关注网络和长期短期内存 （LSTM）。 这些神经网络使用用于深度学习的 Keras API 对文本文档进行分类。 Keras 是以下三个最受欢迎的深度学习框架的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="other-tools"></a>其他工具

其他部分介绍如何使用 Linux DSVM 上安装的一些工具。 我们将讨论以下工具：

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL 和 SQuirreL SQL
* SQL Server 数据仓库

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) 可提供快速、准确的提升树实现。

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

还可以从 Python 或命令行调用 XGBoost。

### <a name="python"></a>Python

对于 Python 开发，Anaconda Python 分发版 3.5 和 2.7 安装在 DSVM 上。

> [!NOTE]
> Anaconda 分发版包括 [Conda](https://conda.pydata.org/docs/index.html)。 可以使用 Conda 创建自定义的 Python 环境，该环境中安装了不同版本或程序包。

让我们读入部分 spambase 数据集，并使用 scikit-learn 中的支持向量机 (support vector machine) 对电子邮件进行分类：

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

若要进行预测:

```Python
clf.predict(X.ix[0:20, :])
```

为演示如何发布 Azure 机器学习终结点，接下来创建更基本的模型。 我们将使用之前发布 R 模型时使用的三个变量：

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

若要将模型发布到 Azure 机器学习：

```Python
# Publish the model.
workspace_id = "<workspace-id>"
workspace_token = "<workspace-token>"
from azureml import services
@services.publish(workspace_id, workspace_token)
@services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
@services.returns(int) # 0 or 1
def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
    inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
    return clf.predict(inputArray)

# Get some info about the resulting model.
predictSpam.service.url
predictSpam.service.api_key

# Call the model
predictSpam.service(1, 1, 1)
```

> [!NOTE]
> 此选项仅适用于 Python 2.7。 Python 3.5 尚不支持。 若要运行，请使用 /anaconda/bin/python2.7****。

### <a name="jupyterhub"></a>JupyterHub

在 DSVM 中的 Anaconda 分发版包含 Jupyter Notebook - 一个共享 Python、R 或 Julia 代码和分析的跨平台环境。 可通过 JupyterHub 访问 Jupyter notebook。 使用本地 Linux 用户名和密码登录 https://\<DSVM DNS name or IP address\>:8000/。 JupyterHub 的所有配置文件可以在 /etc/jupyterhub 中找到。

> [!NOTE]
> 若要在当前内核中从 Jupyter Notebook 使用 Python 包管理器（通过 `pip` 命令），可以在代码单元中使用以下命令：
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 若要在当前内核中从 Jupyter Notebook 使用 Conda 安装程序（通过 `conda` 命令），可以在代码单元中使用以下命令：
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 上已安装了几个示例 Notebook：

* 示例 Python 笔记本：
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 示例 R 笔记本：
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Linux DSVM 上的命令行也提供了 Julia 语言。

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html)（易于学习的 R 分析工具）是用于数据挖掘的图形式 R 工具** **********。 Rattle 具有直观的界面，可方便地加载、浏览和转换数据，生成和评估模型。 [Rattle：R 的数据挖掘 GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)提供了演示拉特功能的演练。

运行以下命令安装和启动 Rattle：

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> 无需在 DSVM 上安装 Rattle。 但 Rattle 打开时，系统可能会提示安装其他程序包。

Rattle 使用一个基于选项卡的接口。 大部分选项卡与[团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)中的步骤相对应，如加载数据或浏览数据等。 数据科学过程从左到由通过选项卡流动。 最后一个选项卡包含 Rattle 所运行的 R 命令日志。

要加载和配置数据集：

1. 要加载文件，选择“数据”选项卡****。
1. 选择 Filename 旁边的选择器，然后选择 spambaseHeaders.data********。
1. 要加载文件， 选择“执行”。  应看到每列的摘要，包括其标识的数据类型，是输入、目标还是其他类型的变量，以及唯一值的数量。
1. Rattle 已经正确将”**spam**“列作为目标。 选择“垃圾邮件”列，然后将“目标数据类型”设置为“类别”************。

要浏览数据：

1. 选择”**浏览**“选项卡。
1. 要查看有关变量类型和一些摘要统计信息的信息，请选择 **"摘要** > **执行**"。
1. 要查看有关每个变量的其他类型的统计信息，请选择其他选项，如 **"描述"** 或 **"基本"。**

此外还可以使用“浏览”选项卡生成富有见解的绘图****。 若要绘制数据直方图：

1. 选择”**分布**“。
1. 对于 word_freq_remove 和 word_freq_you，请选择“直方图”************。
1. 选择 **"执行**"。 在一个图形窗口应可以看到两个密度绘图，很明显，电子邮件中单词 you 比 remove 出现更频繁____。

“关联”绘图也值得关注****。 若要创建绘图：

1. 对于“类型”，请选择“关联”********。
1. 选择 **"执行**"。
1. Rattle 会发出警告，建议最多使用 40 个变量。 选择”**是**“以查看该绘图。

有一些有趣的关联出现：例如 _，技术_与_惠普_和_实验室_密切相关。 它还与 650 密切关联，因为数据集捐赠者的区号是 650__。

单词之间相关性的数值在 **"浏览"** 窗口中可用。 有意思的是，technology 很少与 your 和 money 关联______。

Rattle 可以转换数据集，以处理一些常见问题。 例如，它可以重新调整功能、输入缺失值、处理离群值，并删除缺少数据的变量或观察值。 Rattle 还可以识别观察值和变量之间的关联规则。 本介绍性演练不涉及这些选项卡。

Rattle 还可以运行群集分析。 让我们来排除某些功能，以使输出更易于理解。 在“数据”选项卡上，选择每个变量旁边的“忽略”，以下 10 个变量除外********：

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

返回到 **"群集"** 选项卡。选择**K表示**，然后将**群集数**设置为**4**。 选择 **"执行**"。 结果会显示在输出窗口中。 一个集群有高频率_的乔治_和_hp，_ 可能是一个合法的商业电子邮件。

若要构建基本决策树机器学习模型：

1. 选择 **"模型"** 选项卡，
1. 对于“类型”，选择“树”********。
1. 选择“执行”**** 在输出窗口中以文本形式显示树。
1. 选择”**绘制**“按钮，以查看图形版本。 决策树看起来类似于我们早前使用 rpart 获取的树。

Rattle 的一个不错的功能是它能够运行多个计算机学习方法，并快速对它们进行评估。 下面是相关步骤：

1. 对于“类型”，请选择“全部”********。
1. 选择 **"执行**"。
1. Rattle 运行结束后，可以选择 SVM 等任意“类型”值，然后查看结果********。
1. 您还可以使用 **"评估"** 选项卡比较验证集中模型的性能。例如，"**错误矩阵"** 选择显示验证集中每个模型的混淆矩阵、总体误差和平均类误差。 此外还可以绘制 ROC 曲线，运行区分大小写分析，并执行其他类型的模型评估。

成功生成模型后，可选择“日志”选项卡查看会话期间 Rattle 运行的 R 代码****。 可选择“导出”**** 按钮来保存它。

> [!NOTE]
> 当前版本的 Rattle 中存在一个 Bug。 若要修改脚本或使用它稍后重复步骤，必须在日志文本中插入**#***"导出此日志"* 前面的字符。

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL 和 SQuirreL SQL

DSVM 已预安装 PostgreSQL。 PostgreSQL 是一个复杂的开源关系数据库。 本部分介绍如何将 spambase 数据集加载到 PostgreSQL，然后对其进行查询。

在可以加载数据之前，必须允许来自 localhost 的密码身份验证。 在命令提示符处运行以下命令：

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

配置文件的底部附近的几行详细说明了允许的连接数：

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

更改 IPv4 local connections 行，以便使用 md5，而不是 ident，这样我们可以使用用户名和密码登录************：

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

然后重启 PostgreSQL 服务：

```Bash
sudo systemctl restart postgresql
```

若要作为内置 postgres 用户启动 psql（PostgreSQL 的交互式终端），请运行以下命令**：

```Bash
sudo -u postgres psql
```

使用登录所用的 Linux 帐户的用户名新建用户帐户。 创建密码：

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

登录 psql：

```Bash
psql
```

将数据导入新的数据库：

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

现在，让我们来浏览数据并使用 Squirrel SQL 运行一些查询 - Squirrel SQL 是一种图形工具，可用于通过 JDBC 驱动程序与数据库交互。

若要开始，从“应用程序”菜单打开 Squirrel SQL****。 若要设置驱动程序：

1. 选择**Windows** > **视图驱动程序**。
1. 右键单击“PostgreSQL”，然后选择“修改驱动程序”********。
1. 选择 **"额外类路径** > **添加**"。
1. 对于“文件名”，请输入 /usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar********。
1. 选择“打开”。
1. 选择“列出驱动程序”****。 对于“类名称”，选择 org.postgresql.Driver，然后选择“确定”************。

设置与本地服务器的连接：

1. 选择**Windows** > **查看别名。**
1. 选择按钮**+** 以创建新别名。 对于“新别名名称”，请输入“垃圾邮件数据库”****。 
1. 对于“驱动程序”，选择 PostgreSQL********。
1. 将 URL 设置为 **jdbc:postgresql://localhost/spam**。
1. 输入用户名和密码。
1. 选择“确定”  。
1. 若要打开“连接”**** 窗口，请双击**垃圾邮件数据库**别名。
1. 选择 **"连接**"。

运行一些查询：

1. 选择“SQL”**** 选项卡。
1. 在 SQL 选项卡顶部的查询框中，输入 `SELECT * from data;` 之类的基本查询****。
1. 按 Ctrl+Enter 运行查询。 默认情况下，Squirrel SQL 将从查询中返回前 100 行的内容。

可以运行更多查询来浏览此数据。 例如，在垃圾邮件和非垃圾邮件之间单词 *make* 的频率存在何种差异？

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

或者，经常包含*3d*的电子邮件有哪些特征？

```SQL
SELECT * from data order by word_freq_3d desc;
```

3d 出现率高的电子邮件显然大都是垃圾邮件**。 此信息可能有助于构建预测模型来对电子邮件进行分类。

如果希望使用 PostgreSQL 数据库中存储的数据执行机器学习，请考虑使用 [MADlib](https://madlib.incubator.apache.org/)。

### <a name="sql-data-warehouse"></a>SQL 数据仓库

Azure SQL 数据仓库是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。 有关详细信息，请参阅[什么是 Azure SQL 数据仓库？](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

若要连接到数据仓库并创建表，请从命令提示符运行以下命令：

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

在 sqlcmd 提示符下，运行以下命令：

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

使用 bcp 复制数据：

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> 下载的文件包含 Windows 样式的行尾。 bcp 工具需要 Unix 样式的行尾。 使用 -r 标记来区分 bcp。

然后，使用 sqlcmd 进行查询：

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

还可以使用 SQuirreL SQL 进行查询。 使用 SQL Server JDBC 驱动程序执行类似于 PostgreSQL 的步骤。 JDBC 驱动程序位于 /usr/share/java/jdbcdrivers/sqljdbc42.jar 文件夹中。

## <a name="next-steps"></a>后续步骤

获取文章概述，了解包含在 Azure 中的数据科学过程的任务，请参阅[团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)。

有关针对特定方案，演示团队数据科学过程中的步骤的端到端演练的说明，请参阅[团队数据科学过程演练](../team-data-science-process/walkthroughs.md)。 该演练还展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。
