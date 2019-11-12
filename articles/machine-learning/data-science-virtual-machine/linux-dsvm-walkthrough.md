---
title: 探索 Linux
titleSuffix: Azure Data Science Virtual Machine
description: 了解如何使用 Linux Data Science Virtual Machine 完成几个常见的数据科学任务。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 07/16/2018
ms.openlocfilehash: b073c4244d2a7abc7c2c066c3fad036f0caa5faa
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73929546"
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-in-azure"></a>使用 Azure 中的 Linux Data Science Virtual Machine 进行数据科学

本演练演示如何使用 Linux Data Science Virtual Machine （DSVM）完成几个常见的数据科学任务。 Linux DSVM 是 Azure 中提供的虚拟机映像，预安装了一系列用于数据分析和机器学习的工具。 [预配 Linux Data Science Virtual Machine](linux-dsvm-intro.md)中详细列出了重要软件组件。 利用 DSVM 映像，可以在几分钟内轻松开始执行数据科学，无需单独安装和配置每个工具。 如果需要，你可以轻松地扩展 DSVM，并且可以在不使用时将其停止。 DSVM 资源既灵活又具有成本效益。

本演练中演示的数据科学任务遵循在[什么是团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)中所述的步骤？ 团队数据科学过程是数据科学的系统化方法，可帮助数据科学家团队有效地在生成智能应用程序的生命周期内进行协作。 数据科学过程还为数据科学提供了可供个人遵循迭代框架。

在本演练中，我们将分析[spambase](https://archive.ics.uci.edu/ml/datasets/spambase)数据集。 Spambase 是一组标记为垃圾邮件或 ham （而不是垃圾邮件）的电子邮件。 Spambase 还包含有关电子邮件内容的一些统计信息。 我们稍后将在本演练中讨论统计信息。

## <a name="prerequisites"></a>先决条件

使用 Linux DSVM 之前，必须具备以下先决条件：

* **Azure 订阅**。 若要获取 Azure 订阅，请参阅[立即创建免费 Azure 帐户](https://azure.microsoft.com/free/)。
* [**Linux Data Science Virtual Machine**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm)。 有关设置虚拟机的信息，请参阅[预配 Linux Data Science Virtual Machine](linux-dsvm-intro.md)。
* 使用打开的 XFCE 会话在计算机上安装[**X2Go**](https://wiki.x2go.org/doku.php) 。 有关详细信息，请参阅[安装和配置 X2Go 客户端](linux-dsvm-intro.md#x2go)。
* 为了获得更流畅的滚动体验，请在 DSVM 的 Firefox web 浏览器中切换 `about:config`中的 `gfx.xrender.enabled` 标志。 [了解详细信息](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 还要考虑将 `mousewheel.enable_pixel_scrolling` 设置为 `False`。 [了解详细信息](https://support.mozilla.org/questions/981140)。
* **Azure 机器学习帐户**。 如果还没有该帐户，请在 " [Azure 机器学习" 主页](https://azure.microsoft.com/free/services/machine-learning//)上注册一个新帐户。

## <a name="download-the-spambase-dataset"></a>下载 spambase 数据集

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase)数据集是一个相对较小的数据集，其中包含4601个示例。 数据集是一种方便的大小，可用于演示 DSVM 的部分关键功能，因为它会使资源需求适度适中。

> [!NOTE]
> 本演练是使用 D2 v2 大小的 Linux DSVM （CentOS Edition）创建的。 您可以使用此大小的 DSVM 来完成本演练中所演示的过程。

如果需要更多存储空间，可以创建更多的磁盘，并将其附加到 DSVM。 磁盘使用永久性 Azure 存储空间，因此即使由于调整大小或关闭服务器而导致服务器重新预配，也会保留其数据。 若要添加磁盘并将其附加到 DSVM，请完成[将磁盘添加到 LINUX VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的步骤。 添加磁盘的步骤使用已安装在 DSVM 上的 Azure CLI。 您可以完全从 DSVM 中完成这些步骤。 增加存储的另一个选项是使用[Azure 文件](../../storage/files/storage-how-to-use-files-linux.md)。

若要下载数据，请打开终端窗口，并运行以下命令：

    wget https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

下载的文件没有标头行。 让我们创建另一个具有标头的文件。 运行以下命令来创建具有适当标头的文件：

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

然后，将两个文件连接在一起：

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

数据集为每封电子邮件提供多种类型的统计信息：

* Word **\_\_频率** 等列指示电子邮件中与*单词*匹配的单词百分比。 例如，如果**word\_频率\_使**为**1**，则电子邮件中所有单词的 1 *% 都为1。*
* Char **\_\_频率等列** 会指示电子邮件中作为*char*的所有字符的百分比。
* **capital\_run\_length\_longest** 是一连串大写字母的最大长度。
* **capital\_run\_length\_average** 是所有连串大写字母的平均长度。
* **capital\_run\_length\_total** 是所有连串大写字母的合计长度。
* **spam** 指示是否将电子邮件视为垃圾邮件（1 = 垃圾邮件，0 = 不是垃圾邮件）。

## <a name="explore-the-dataset-by-using-r-open"></a>使用 R Open 浏览数据集

让我们通过使用 R 来检查数据并执行一些基本的机器学习。DSVM 附带了[Microsoft R Open](https://mran.revolutionanalytics.com/open/)预安装。 预安装版本 R 中的多线程数学库提供比单线程版本更好的性能。 R Open 还通过 CRAN 包存储库的快照提供可再现性。

若要获取本演练中使用的代码示例的副本，请使用 git 克隆 Azure 机器学习数据科学存储库。 Git 预安装在 DSVM 上。 在 git 命令行上，运行：

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

打开终端窗口，并在 R 交互控制台中启动新的 R 会话。 你还可以使用预安装在 DSVM 上的 RStudio。

导入数据并设置环境：

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

若要查看有关每列的汇总统计信息：

    summary(data)

有关数据的不同视图：

    str(data)

此视图显示每个变量的类型和数据集中的前几个值。

**spam** 列作为一个整数被读取，但实际上它是一个分类变量 （或系数）。 若要设置其类型：

    data$spam <- as.factor(data$spam)

若要执行一些探索性分析，请使用[ggplot2](https://ggplot2.tidyverse.org/)包，这是在 DSVM 上预安装的适用于 R 的常用绘图库。 根据先前显示的摘要数据，我们会获得有关感叹号字符的频率的汇总统计信息。 让我们通过运行以下命令在此处绘制这些频率：

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

由于零条会使绘图倾斜，因此让我们消除这一点：

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

1以上的重要密度看起来很有意思。 让我们看一下这些数据：

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

然后，通过垃圾邮件和 ham 进行拆分：

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

这些示例可帮助您创建类似的绘图并浏览其他列中的数据。

## <a name="train-and-test-a-machine-learning-model"></a>训练和测试机器学习模型

让我们训练几个机器学习模型，将数据集中的电子邮件分类为包含垃圾邮件或 ham。 在本部分中，我们训练决策树模型和随机林模型。 然后，测试预测的准确性。

> [!NOTE]
> 在以下代码中使用的*rpart* （递归分区和回归树）包已安装在 DSVM 上。

首先，让我们将数据集拆分为定型集和测试集：

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

然后，创建一个决策树来分类电子邮件：

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

结果如下：

![创建的决策树的关系图](./media/linux-dsvm-walkthrough/decision-tree.png)

若要确定它对训练集的执行情况，请使用下面的代码：

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

若要确定它对测试集的执行情况，请使用下面的代码：

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

让我们也尝试一个随机林模型。 随机林为许多决策树定型，并输出一个类，该类是所有单个决策树中的分类模式。 它们提供了更强大的机器学习方法，因为它们是为决策树模型 overfit 定型数据集的趋势而纠正的。

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-machine-learning-studio-classic"></a>将模型部署到 Azure 机器学习 Studio （经典）

[Azure 机器学习 Studio （经典）](https://studio.azureml.net/)是一种云服务，可让你轻松构建和部署预测分析模型。 Azure 机器学习 Studio 经典版的一项良好功能是能够将任何 R 函数发布为 web 服务。 Azure 机器学习 Studio R 包可让你轻松地从 DSVM 上的 R 会话进行部署。

若要从上一节中部署决策树代码，请登录到 Azure 机器学习 Studio （经典）。 需要使用工作区 ID 和授权令牌进行登录。 若要查找这些值并将 Azure 机器学习变量初始化为这些值，请完成以下步骤：

1. 在左侧菜单中，选择 "**设置**"。 请注意 "**工作区 ID**" 的值。

   ![Azure 机器学习 Studio 工作区 ID](./media/linux-dsvm-walkthrough/workspace-id.png)

1. 选择 "**授权令牌**" 选项卡。请记下**主授权令牌**的值。

   ![Azure 机器学习 Studio 主授权令牌](./media/linux-dsvm-walkthrough/workspace-token.png)
1. 加载**AzureML**包，然后在 DSVM 上的 R 会话中将变量的值设置为令牌和工作区 ID：

        if(!require("AzureML")) install.packages("AzureML")
        require(AzureML)
        wsAuth = "<authorization-token>"
        wsID = "<workspace-id>"

1. 让我们简化模型，使此演示更易实现。 选择最靠近根的决策树中的三个变量，然后仅使用这三个变量生成新树：

        colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
        smallTrainSet <- trainSet[, colNames]
        smallTestSet <- testSet[, colNames]
        model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

1. 我们需要一个预测函数将这些功能作为输入并返回预测值：

        predictSpam <- function(newdata) {
        predictDF <- predict(model.rpart, newdata = newdata)
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
        }


1. 使用**publishWebService**函数将**predictSpam**函数发布到 AzureML：

        spamWebService <- publishWebService(ws, fun = predictSpam, name="spamWebService", inputSchema = smallTrainSet, data.frame=TRUE)

1. 此函数采用**predictSpam**函数，创建名为**spamWebService**的 web 服务，该服务定义了输入和输出，然后返回有关新终结点的信息。

    使用此命令可查看最新的已发布 web 服务的详细信息，包括其 API 终结点和访问密钥：

        s<-tail(services(ws, name = "spamWebService"), 1)
        ep <- endpoints(ws,s)
        ep

1. 若要尝试测试集的前 10 行：

        consume(ep, smallTestSet[1:10, ])

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>深度学习教程和演练

除了基于框架的示例外，还提供了一组全面的演练。 这些演练可帮助你快速开始开发域中的深度学习应用程序，如图像和文本/语言理解等。

- [跨不同框架运行神经网络](https://github.com/ilkarman/DeepLearningFrameworks)：一个全面演练，演示如何将代码从一个框架迁移到另一个框架。 它还演示如何在框架之间比较模型和运行时性能。 

- [如何构建端到端解决方案来检测图像中的产品](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：图像检测是一种可以在图像中查找和分类对象的方法。 该技术可能会在许多现实业务领域中带来巨大的回报。 例如，零售商可以使用此技术确定客户已从货架上选取哪个产品。 从而，此信息可帮助商店管理产品库存。 

- [深入了解音频](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)：本教程演示如何针对 "[市内声音" 数据集](https://urbansounddataset.weebly.com/)训练深度学习模型，以便进行音频事件检测。 本教程提供了有关如何使用音频数据的概述。

- [文本文档分类](https://github.com/anargyri/lstm_han)：本演练演示如何构建和训练两种不同的神经网络体系结构：分层注意网络和长期内存（LSTM）。 这些神经网络使用用于深度学习的 Keras API 对文本文档进行分类。 Keras 是以下三个最受欢迎的深度学习框架的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="other-tools"></a>其他工具

其余部分介绍了如何使用 Linux DSVM 上安装的一些工具。 我们将讨论以下工具：

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL 和 SQuirreL SQL
* SQL Server 数据仓库

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/)提供快速且准确的提升树实现。

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

XGBoost 也可以从 Python 或命令行调用。

### <a name="python"></a>Python

对于 Python 开发，Anaconda Python 分发3.5 和2.7 安装在 DSVM 上。

> [!NOTE]
> Anaconda 分布包括[Conda](https://conda.pydata.org/docs/index.html)。 可以使用 Conda 创建在其中安装了不同版本或包的自定义 Python 环境。

接下来，请阅读一些 spambase 数据集，并在 Scikit-learn 中通过支持矢量计算机对电子邮件进行分类-了解：

    import pandas
    from sklearn import svm
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

若要进行预测:

    clf.predict(X.ix[0:20, :])

为了演示如何发布 Azure 机器学习终结点，让我们创建一个更基本的模型。 我们将使用以下三个变量，我们在前面发布 R 模型时使用了这些变量：

    X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

若要将模型发布到 Azure 机器学习：

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


> [!NOTE]
> 此选项仅适用于 Python 2.7。 Python 3.5 尚不支持此方法。 若要运行，请使用 **/anaconda/bin/python2.7**。

### <a name="jupyterhub"></a>JupyterHub

DSVM 中的 Anaconda 分布附带了一个 Jupyter Notebook，这是一个用于共享 Python、R 或 Julia 代码和分析的跨平台环境。 Jupyter Notebook 通过 JupyterHub 访问。 使用本地 Linux 用户名和密码登录到 https://\<DSVM DNS 名称或 IP 地址\>： 8000/。 JupyterHub 的所有配置文件均可在/etc/jupyterhub. 中找到。

> [!NOTE]
> 若要在当前内核中从 Jupyter Notebook 使用 Python 包管理器（通过 `pip` 命令），请在代码单元中使用以下命令：
>
>   ```python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> 若要在当前内核中从 Jupyter Notebook 使用 Conda 安装程序（通过 `conda` 命令），请在代码单元中使用以下命令：
>
>   ```python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

DSVM 上已安装了几个示例笔记本：

* 示例 Python 笔记本：
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
  * [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb)
* 示例 R 笔记本：
  * [有关 introtutorialinr](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> 还可以从 Linux DSVM 上的命令行中获取 Julia 语言。

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) （*R* *A*nalytical *t*ool *t*o *L*赢得*E*asily）是一种用于数据挖掘的图形化 R 工具。 Rattle 具有直观的界面，使您可以轻松地加载、浏览和转换数据，以及生成和评估模型。 [Rattle：用于 R 的数据挖掘 GUI](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)提供演示 Rattle 功能的演练。

通过运行以下命令安装和启动 Rattle：

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> 不需要在 DSVM 上安装 Rattle。 但是，当 Rattle 打开时，系统可能会提示您安装其他程序包。

Rattle 使用一个基于选项卡的接口。 大部分选项卡与[团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)中的步骤相对应，如加载数据或浏览数据。 数据科学过程从左到由通过选项卡流动。 最后一个选项卡包含 Rattle 运行的 R 命令的日志。

要加载和配置数据集：

1. 若要加载该文件，请选择 "**数据**" 选项卡。
1. 选择 "**文件名**" 旁边的选择器，然后选择 " **spambaseHeaders**"。
1. 要加载文件， 选择 "**执行**"。 应该会看到每个列的摘要，包括其标识的数据类型;它是输入、目标还是其他类型的变量;和唯一值的数目。
1. Rattle 已经正确将”**spam**“列作为目标。 选择**垃圾邮件**列，然后将**目标数据类型**设置为 **"类别"** 。

要浏览数据：

1. 选择”**浏览**“选项卡。
1. 若要查看有关变量类型和某些汇总统计信息的信息，请选择 "**摘要** > **执行**"。
1. 若要查看有关每个变量的其他类型的统计信息，请选择其他选项，如 "**描述**" 或 "**基本**"。

你还可以使用 "**浏览**" 选项卡生成具有深刻的绘图。 若要绘制数据直方图：

1. 选择”**分布**“。
1. 对于**word_freq_remove**和**word_freq_you**，请选择 "**直方图**"。
1. 选择”**执行**“。 你应在单个图形窗口中看到两个密度图，在这种情况下，_在电子_邮件中显示的词比_删除_更频繁。

**相关**图形也很有趣。 创建绘图：

1. 对于 "**类型**"，请选择 "**关联**"。
1. 选择”**执行**“。
1. Rattle 会发出警告，建议最多使用 40 个变量。 选择”**是**“以查看该绘图。

这会带来一些有趣的相关性：例如，_技术_与_HP_和_实验室_密切相关。 由于数据集赞助者的区号为650，因此它也强烈关联到_650_ 。

"**浏览**" 窗口中提供了单词之间的关联的数值。 例如，要注意的是，该_技术_与和_金钱_ _有负面_关联。

Rattle 可以转换数据集，以处理一些常见问题。 例如，它可以重新调整功能，归结缺失值，处理离群值，删除缺少数据的变量或观察值。 Rattle 还可以确定观察值与变量之间的关联规则。 本指南中未介绍这些选项卡。

Rattle 还可以运行群集分析。 让我们来排除某些功能，以使输出更易于理解。 在 "**数据**" 选项卡上，选择除以下10个项之外的每个变量旁的 "**忽略**"：

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

返回到 "**群集**" 选项卡。选择 " **KMeans**"，然后将 "**群集数**" 设置为**4**。 选择”**执行**“。 结果会显示在输出窗口中。 一个群集具有高频率的_george_和_hp_，可能是合法的业务电子邮件。

构建基本决策树机器学习模型：

1. 选择”**模型**“选项卡，
1. 对于 "**类型**"，选择 "**树**"。
1. 选择“执行”在输出窗口中以文本形式显示树。
1. 选择”**绘制**“按钮，以查看图形版本。 决策树类似于我们之前通过使用 rpart 获取的树。

Rattle 的一项有用功能是它能够运行多个机器学习方法并快速对它们进行评估。 步骤如下：

1. 对于 "**类型**"，请选择 "**全部**"。
1. 选择”**执行**“。
1. Rattle 完成运行后，可以选择任意**类型**值（如**SVM**），然后查看结果。
1. 您还可以通过使用 "**评估**" 选项卡来比较验证集上的模型的性能。例如，**错误矩阵**选择显示验证集上每个模型的混淆矩阵、整体错误和平均类错误。 还可以绘制 ROC 曲线、运行敏感度分析和执行其他类型的模型评估。

完成模型构建后，选择 "**日志**" 选项卡以查看在会话期间由 Rattle 运行的 R 代码。 可选择“导出”按钮来保存它。

> [!NOTE]
> Rattle 的当前版本包含 bug。 若要修改脚本或稍后使用它来重复步骤，必须在日志文本中的 "*导出此日志 ...* " 前面插入 **#** 字符。

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL 和 SQuirreL SQL

DSVM 已预安装 PostgreSQL。 PostgreSQL 是一个复杂的开源关系数据库。 本部分介绍如何将 spambase 数据集加载到 PostgreSQL 中，然后对其进行查询。

在加载数据之前，必须允许来自 localhost 的密码身份验证。 在命令提示符处运行以下命令：

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

配置文件的底部附近的几行详细说明了允许的连接数：

    # "local" is only for Unix domain socket connections:
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

将 " **IPv4 本地连接**" 行更改为使用**md5** ，而不是**ident**，以便我们可以使用用户名和密码登录：

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

然后重新启动 PostgreSQL 服务：

    sudo systemctl restart postgresql

若要启动*psql* （PostgreSQL 的交互式终端）作为内置 postgres 用户，请运行以下命令：

    sudo -u postgres psql

使用登录所用的 Linux 帐户的用户名创建新的用户帐户。 创建密码：

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

登录到 psql：

    psql

将数据导入到新数据库：

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

现在，让我们浏览数据，并使用 SQuirreL SQL （一种图形工具，可用于通过 JDBC 驱动程序与数据库进行交互）来运行一些查询。

若要开始，请在 "**应用程序**" 菜单上打开 SQuirreL SQL。 若要设置驱动程序：

1. 选择 " **Windows** > **查看驱动程序**"。
1. 右键单击**PostgreSQL** ，然后选择 "**修改驱动程序**"。
1. 选择 "**附加类路径**" > "**添加**"。
1. 对于 "**文件名**"，请输入 **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**。
1. 选择“打开”。
1. 选择 "**列出驱动程序**"。 对于 "**类名**"，请选择 " **postgresql**"，然后选择 **"确定"** 。

设置与本地服务器的连接：

1. 选择**Windows** > **查看别名。**
1. 选择 " **+** " 按钮以创建新别名。 对于 "新别名"，请输入**垃圾邮件数据库**。 
1. 对于 "**驱动程序**"，选择**PostgreSQL**。
1. 将 URL 设置为 **jdbc:postgresql://localhost/spam**。
1. 输入用户名和密码。
1. 选择“确定”。
1. 若要打开“连接”窗口，请双击**垃圾邮件数据库**别名。
1. 选择“连接”。

运行一些查询：

1. 选择“SQL”选项卡。
1. 在 " **SQL** " 选项卡顶部的 "查询" 框中，输入一个基本查询，如 `SELECT * from data;`。
1. 按 Ctrl + Enter 以运行查询。 默认情况下，SQuirreL SQL 从查询返回前100行。

您还可以运行多个查询来浏览此数据。 例如，在垃圾邮件和非垃圾邮件之间单词 *make* 的频率存在何种差异？

    SELECT avg(word_freq_make), spam from data group by spam;

或者，通常包含*三维*的电子邮件的特征是什么？

    SELECT * from data order by word_freq_3d desc;

大多数出现*三维*的电子邮件明显都是垃圾邮件。 此信息可能有助于构建预测模型来对电子邮件进行分类。

如果要使用 PostgreSQL 数据库中存储的数据进行机器学习，请考虑使用[MADlib](https://madlib.incubator.apache.org/)。

### <a name="sql-data-warehouse"></a>SQL 数据仓库

Azure SQL 数据仓库是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。 有关详细信息，请参阅[什么是 Azure SQL 数据仓库？](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

若要连接到数据仓库并创建表，请从命令提示符运行以下命令：

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

在 sqlcmd 提示符下，运行以下命令：

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

使用 bcp 复制数据：

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> 下载的文件包含 Windows 样式的行尾。 Bcp 工具需要 Unix 样式的行尾。 使用-r 标志来告知 bcp。

然后，使用 sqlcmd 进行查询：

    select top 10 spam, char_freq_dollar from spam;
    GO

还可以使用 SQuirreL SQL 进行查询。 使用 SQL Server JDBC 驱动程序执行类似于 PostgreSQL 的步骤。 JDBC 驱动程序位于/usr/share/java/jdbcdrivers/sqljdbc42.jar 文件夹中。

## <a name="next-steps"></a>后续步骤

有关指导你完成 Azure 中的数据科学过程的任务的概述，请参阅[团队数据科学过程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview)。

有关演示特定方案的团队数据科学过程中的步骤的端到端演练的说明，请参阅[团队数据科学过程演练](../team-data-science-process/walkthroughs.md)。 该演练还展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。
