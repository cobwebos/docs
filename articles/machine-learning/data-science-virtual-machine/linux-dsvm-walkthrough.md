---
title: Azure 上的 Linux 数据科学虚拟机中的数据科学 | Microsoft Docs
description: 如何通过 Linux 数据科研 VM 执行几个常见的数据科学任务。
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 055d8b1c9884c9525ba15ea9508ab00a5f48a048
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Azure 上的 Linux 数据科学虚拟机中的数据科学
本演练显示如何通过 Linux 数据科研 VM 执行几个常见的数据科学任务。 Linux 数据科研虚拟机 (DSVM) 是 Azure 提供的虚拟机映像，其中预安装了一组常用于执行数据分析和机器学习的工具。 [预配 Linux 数据科研虚拟机](linux-dsvm-intro.md)主题中逐项列出了主要的软件组件。 VM 映像允许在几分钟之内轻松开始执行数据科学任务，而无需逐个安装和配置每个工具。 如有必要，可以轻松扩展 VM，并在不使用时停止 VM。 因此，此资源既具有弹性，又具有成本效益。

在本演练中演示的数据科学任务遵循了 [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) 中所概述的步骤。 此过程针对数据科学任务提供了系统的方法，允许数据科学家团队在构建智能应用程序的生命周期内有效地协作。 数据科学过程还为数据科学提供了可供个人遵循迭代框架。

在本演练中，我们对 [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 数据集进行了分析。 这一组电子邮件被标记为 spam 或 ham（即它们不是垃圾邮件），并且还包含电子邮件内容的一些统计信息。 包含的统计信息会在下一节中讨论。

## <a name="prerequisites"></a>先决条件
在可以使用 Linux 数据科研虚拟机之前，必须具备以下条件：

* **Azure 订阅帐户**。 如果没有，请参阅[立即创建免费的 Azure 帐户](https://azure.microsoft.com/free/)。
* [**Linux 数据科学 VM**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm)。 有关预配此 VM 的信息，请参阅[预配 Linux 数据科研虚拟机](linux-dsvm-intro.md)。
* 计算机上安装了 [X2Go](http://wiki.x2go.org/doku.php) 且 XFCE 会话处于打开状态。 若要深入了解安装和配置 **X2Go 客户端**的方法，请参阅[安装和配置 X2Go 客户端](linux-dsvm-intro.md#installing-and-configuring-x2go-client)。
* 为了获得更平滑的滚动体验，请在 VM FireFox 浏览器中切换 about:config 中的 gfx.xrender.enabled 标志。 [在此处了解详细信息](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/)。 另请考虑将 *mousewheel.enable_pixel_scrolling* 切换为 False。 请参阅[此处的说明](https://support.mozilla.org/en-US/questions/981140)。
* **AzureML 帐户**。 如果还没有帐户，请在 [AzureML 主页](https://studio.azureml.net/)中注册一个新帐户。 里面有免费的使用等级可帮助用户开始使用。

## <a name="download-the-spambase-dataset"></a>下载 spambase 数据集
[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) 数据集是相对较小，仅包含 4601 个示例的一组数据。 在演示数据科学 VM 的一些主要功能时，这是一个很方便的大小，因为它使资源需求保持适中。

> [!NOTE]
> 本演练是在 D2 v2 大小的 Linux 数据科学虚拟机上进行的。 此大小的 DSVM 能够处理本演练中的过程。
>
>

如果需要更多存储空间，可以创建额外的磁盘，然后将它们附加到 VM。 这些磁盘使用 Azure 持久存储，这样即使是在服务器被重新调整大小或关闭的情况下，其数据仍会被保留。 要添加磁盘并将其附加到 VM，请遵循[将磁盘添加到 Linux VM](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中的说明进行操作。 这些步骤使用 Azure 命令行界面 (Azure CLI)，界面已安装在 DSVM 上。 因此完全可以从虚拟机自身完成这些过程。 另一个增加存储的选项是使用 [Azure 文件](../../storage/files/storage-how-to-use-files-linux.md)。

若要下载数据，打开终端窗口并运行以下命令：

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

下载的文件并没有标头行，因此，让我们创建另一个有标头的文件。 运行以下命令来创建具有适当标头的文件：

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

然后执行以下命令将两个文件相连接：

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

数据集内有多种关于每封电子邮件的统计信息：

* 列 ***word\_freq\_WORD*** 指明电子邮件中的单词与 *WORD* 的匹配百分比。 例如，如果 *word\_freq\_make* 为 1，那么电子邮件中所有单词的 1% 都为 *make*。
* 列 ***char\_freq\_CHAR*** 指明电子邮件的所有字符中 *CHAR* 所占的百分比。
* ***capital\_run\_length\_longest*** 是一连串大写字母的最大长度。
* ***capital\_run\_length\_average*** 是所有连串大写字母的平均长度。
* ***capital\_run\_length\_total*** 是所有连串大写字母的合计长度。
* ***spam*** 指示是否将电子邮件视为垃圾邮件（1 = 垃圾邮件，0 = 不是垃圾邮件）。

## <a name="explore-the-dataset-with-microsoft-r-open"></a>使用 Microsoft R Open 探索数据集
让我们使用 R 来检查数据并执行一些基本的机器学习。数据科研 VM已预安装了 [Microsoft R Open](https://mran.revolutionanalytics.com/open/) 。 此版本的 R 中的多线程数学库提供了优于不同单线程版本的性能。 通过使用 CRAN 程序包存储库的快照，Microsoft R Open 还提供了再现功能。

若要获取本演练中使用的代码示例的副本，请使用 VM 上已预装的 git 克隆 **Azure-Machine-Learning-Data-Science** 存储库。 从 git 命令行运行：

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

打开终端窗口，使用 R 交互式控制台启动一个新的 R 会话。

> [!NOTE]
> 还可以针对以下过程使用 RStudio。 若要安装 RStudio，在终端执行此命令：`./Desktop/DSVM\ tools/installRStudio.sh`
>
>

若要导入数据并设置环境，请运行：

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

若要查看有关每列的汇总统计信息：

    summary(data)

有关数据的不同视图：

    str(data)

这会显示数据集中每个变量的类型以及开始的几个值。

*spam* 列作为一个整数被读取，但实际上它是一个分类变量 （或系数）。 若要设置其类型：

    data$spam <- as.factor(data$spam)

若要进行一些探索性分析，请使用 [ggplot2](http://ggplot2.org/) 程序包 - 一个适用于 R 的常用绘图库，已安装在 VM 上。 请注意，从前面显示的摘要数据，我们已经获得有关感叹号字符的频率的汇总统计信息。 让我们使用以下命令来绘制这些频率：

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

由于零轴会影响绘图的准确性，让我们将其删除：

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

在 1 上面有看起来很有意思的不寻常密度。 让我们只看该数据：

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

然后按垃圾邮件与非垃圾邮件来拆分它：

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

这些示例应使用户能够为其他列绘制类似图形来探索它们所包含的数据。

## <a name="train-and-test-an-ml-model"></a>训练和测试 ML 模型
现在，让我们来对几个机器学习模型进行定型，按照包含垃圾邮件或非垃圾邮件对数据集中的电子邮件进行分类。 在本部分中，我们将训练决策树模型和随机森林模型，然后测试其预测的准确性。

> [!NOTE]
> 数据科学 VM 上已安装了以下代码中使用的 rpart（递归分区和回归树）包。
>
>

首先，让我们将该数据集拆分为训练集和测试集：

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

然后创建决策树，将电子邮件分类。

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

结果如下：

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

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

让我们也尝试一个随机林模型。 随机森林模型训练大量的决策树并输出一个类，该类是来自所有单个决策树的分类模式。 它们提供了更强大的机器学习方法，因为他们会校正决策树模型过拟合 (overfit) 数据集的趋势。

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>将模型部署到 Azure ML
[Azure 机器学习工作室](https://studio.azureml.net/) (AzureML) 是一种云服务，可用来轻松地生成和部署预测分析模型。 AzureML 出色的功能之一是能够将任何 R 函数发布为 Web 服务。 AzureML R 程序包使得从 DSVM 上的 R 会话执行部署更加简便直接。

要部署前一部分中的决策树代码，需要登录 Azure 机器学习工作室。 需要使用工作区 ID 和授权令牌进行登录。 若要查找这些值并使用它们初始化 AzureML 变量：

在左侧菜单上选择“**设置**”。 记下“**工作区 ID**”。 ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

从顶部的菜单选择“**授权令牌**”并记下“**主授权令牌**”。![3](./media/linux-dsvm-walkthrough/workspace-token.png)

加载 **AzureML** 包，并使用 DSVM 上 R 会话中的令牌和工作区 ID 设置变量的值：

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


让我们简化模型，使此演示更易实现。 在离根最近的决策树中选取三个变量，然后只使用这三个变量构建新的树：

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

我们需要一个预测函数将这些功能作为输入并返回预测值：

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

使用 **publishWebService** 函数将 predictSpam 函数发布到 AzureML：

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

此函数会采用 **predictSpam** 函数创建一个名为 **spamWebService** 并且定义了输入和输出的 web 服务，并返回有关新的终结点的信息。

使用以下命令查看已发布的 web 服务的信息，包括其 API 终结点和访问密钥：

    spamWebService[[2]]

若要尝试测试集的前 10 行：

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>使用其他可用工具
其余部分会显示如何使用 Linux 数据科研虚拟机上安装的一些工具。以下是所讨论工具的列表：

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL 和 Squirrel SQL
* SQL Server 数据仓库

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) 是一种可提供快速、 准确地提升树实现的工具。

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

还可以从 python 或命令行调用 XGBoost。

## <a name="python"></a>Python
对于使用 Python 进行的开发，DSVM 上已安装了 Anaconda Python 分发版 2.7 和 3.5。

> [!NOTE]
> Anaconda 分发版包含 [Condas](http://conda.pydata.org/docs/index.html)，可用来为 Python 建立已安装不同版本和 (或) 封装的自定义环境。
>
>

让我们读入部分 spambase 数据集，并使用 scikit-learn 中的支持向量机 (support vector machine) 对电子邮件进行分类：

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

若要进行预测:

    clf.predict(X.ix[0:20, :])

若要显示如何发布 AzureML 终结点，让我们使用三个变量创建一个更简单的模型，与之前发布 R 模型的方法相同。

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

要将模型发布到 AzureML：

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
> 此功能仅适用于 python 2.7， 在 3.5 上尚不支持。 请使用 **/anaconda/bin/python2.7** 来运行。
>
>

## <a name="jupyterhub"></a>Jupyterhub
在 DSVM 中的 Anaconda 分发版包含 Jupyter notebook - 一个共享 Python、R 或 Julia 代码和分析的跨平台环境。 可通过 JupyterHub 访问 Jupyter notebook。 使用本地 Linux 用户名和密码在 ***https://\<VM DNS 名称 或 IP 地址\>:8000/*** 中进行登录。 JupyterHub 的所有配置文件可以在 **/etc/jupyterhub** 目录中找到。

VM 上已安装了几个示例 Notebook：

* 请参阅 [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) 以了解示例 Python notebook。
* 有关示例 **R** Notebook ，请参阅 [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb)。
* 请参阅 [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) 以了解另一个示例 **Python** notebook。

> [!NOTE]
> Linux 数据科科研 VM 上的命令行也提供了 Julia 语言。
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html)（易于学习的 R 分析工具） 是用于数据挖掘的图形式 R 工具。 它具有直观的界面，可方便地加载、 浏览和转换数据，生成和评估模型。  [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf)（Rattle：R 的数据挖掘 GUI）一文提供了演练来演示其功能。

使用以下命令安装并启动 Rattle：

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> DSVM 不需要安装 Rattle。 但 Rattle 在加载时可能会提示安装其他程序包。
>
>

Rattle 使用一个基于选项卡的接口。 大部分选项卡与 [Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/) 中的步骤相对应，如加载数据或浏览数据等。 数据科学过程从左到由通过选项卡流动。 但是，最后一个选项卡包含 Rattle 所运行的 R 命令日志。

要加载和配置数据集：

* 要加载文件，选择”**数据**“选项卡，然后
* 选择 **Filename** 旁边的选择器，并选择 **spambaseHeaders.data**。
* 要加载文件， 在按钮的首行中，选择“执行”。 应看到每列的摘要，包括其标识的数据类型，是输入、目标还是其他类型的变量，以及唯一值的数量。
* Rattle 已经正确将”**spam**“列作为目标。 选择垃圾邮件列，然后将”**目标数据类型**“设置为 ”**类别**“。

要浏览数据：

* 选择”**浏览**“选项卡。
* 依次单击“摘要”和“执行”，然后会显示变量类型的相关信息和一些摘要统计信息。
* 若要查看每个变量其他类型的统计信息，请选择其他选项，如“描述”或“基本信息”。

”**浏览**“选项卡还允许生成多个具有洞察力的图形。 若要绘制数据直方图：

* 选择”**分布**“。
* 为 **word_freq_remove** 和 **word_freq_you** 勾选“**直方图**”。
* 选择”**执行**“。 应看到一个图形窗口中的两个密度图，很显然在电子邮件中 "you"一词的出现比 "remove" 频繁得多。

”关联“图形也很有意思。 要创建一个关联图形：

* 选择“关联”作为“类型”，然后
* 选择”**执行**“。
* Rattle 会发出警告，建议最多使用 40 个变量。 选择”**是**“以查看该绘图。

有一些很有意思的关联性：例如，"technology" 与 "HP" 和 "labs" 密切关联。 它还与 "650" 密切关联，因为数据集捐赠者的区号是 650。

”资源管理器“窗口中提供了单词之间的关联性数值。 有意思的是，"technology" 很少与 "your" 和 "money" 关联。

Rattle 可以转换数据集，以处理一些常见问题。 例如，它允许重新调整功能、 输入缺失值、处理离群值，并删除缺少数据的变量或观察值。 Rattle 还可以识别观察值和/或变量之间的关联规则。 这些选项卡不在此介绍性演练的范围。

Rattle 还可以执行群集分析。 让我们来排除某些功能，以使输出更易于理解。 在”**数据**“选项卡上，选择每个变量旁边的”**忽略**“，以下 10 个变量除外：

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

然后返回“群集”选项卡，选择“KMeans”，将“群集数”设置为 4。 然后“执行”。 结果会显示在输出窗口中。 一个群集具有高频率的 "george" 和 "hp"，可能合法的商业电子邮件。

构建简单的决策树机器学习模型：

* 选择”**模型**“选项卡，
* 选择”**树**“作为”**类型**“。
* 选择“执行”在输出窗口中以文本形式显示树。
* 选择”**绘制**“按钮，以查看图形版本。 这看起来非常类似于我们早前使用 *rpart* 获取的树。

Rattle 的一个不错的功能是它能够运行多个计算机学习方法，并快速对它们进行评估。 下面是该过程：

* 选择”**所有**“作为”**类型**“。
* 选择”**执行**“。
* 完成后，可以单击任意单个”**类型**“（如 **SVM**）并查看结果。
* 还可以使用“评估”选项卡，在验证集上比较模型的性能。例如，”**错误矩阵**“选项显示验证集上每个模型的混淆矩阵、整体错误和平均类错误。
* 此外还可以绘制 ROC 曲线，执行区分大小写分析，并执行其他类型的模型评估。

成功生成模型后，可选择“日志”选项卡查看会话期间 Rattle 运行的 R 代码。 可选择“导出”按钮来保存它。

> [!NOTE]
> 当前版本的 Rattle 中存在一个 Bug。 若要修改脚本或稍后使用它来重复步骤，必须在日志文本中的*导出此日志...*前面插入 # 字符。
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL 和 Squirrel SQL
DSVM 已预安装 PostgreSQL。 PostgreSQL 是一个复杂的开源关系数据库。 本部分显示如何将垃圾邮件数据集加载到 PostgreSQL，然后对其进行查询。

在可以加载数据之前，需要允许来自 localhost 的密码身份验证。 在命令提示符：

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

配置文件的底部附近的几行详细说明了允许的连接数：

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

更改 "IPv4 local connections"，以便使用 md5，而不是 ident，这样我们可以使用用户名和密码登录：

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

然后重启 postgres 服务：

    sudo systemctl restart postgresql

若要作为内置 postgres 用户启动 psql（PostgreSQL 的交互式终端）， 在提示符下运行以下命令：

    sudo -u postgres psql

创建新的用户帐户，使用当前登录所使用的相同用户名作为 Linux 帐户，并为其提供密码：

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

然后作为用户登录到 psql：

    psql

将数据导入新的数据库：

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

现在，让我们来浏览数据并使用 **Squirrel SQL** 运行一些查询 - Squirrel SQL 是一种图形工具，允许通过 JDBC 驱动程序与数据库交互。

若要开始，从应用程序菜单启动 Squirrel SQL。 若要设置驱动程序：

* 选择”**Windows**“，并选择”**查看驱动程序**“。
* 右键单击 **PostgreSQL**，并选择”**修改驱动程序**“。
* 选择“额外类路径”，并选择“添加”。
* 在“文件名”中输入 ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar***，并
* 选择“打开”。
* 选择”列出驱动程序“，在”**类名**“中选择 **org.postgresql.Driver**，并选择”**确定**“。

设置与本地服务器的连接：

* 选择“Windows”，并选择“查看别名”。
* 选择“+”按钮创建新的别名。
* 将其命名为*垃圾邮件数据库*，在“驱动程序”下拉列表中，选择“PostgreSQL”。
* 将 URL 设置为 *jdbc:postgresql://localhost/spam*。
* 输入*用户名*和*密码*。
* 单击“确定”。
* 若要打开“连接”窗口，请双击***垃圾邮件数据库***别名。
* 选择“连接”。

运行一些查询：

* 选择“SQL”选项卡。
* 在“SQL”选项卡顶部的查询文本框中输入一个简单查询，例如 `SELECT * from data;`。
* 按 **Ctrl-Enter** 运行它。 默认情况下，Squirrel SQL 将从查询中返回前 100 行的内容。

可以运行更多查询来浏览此数据。 例如，在垃圾邮件和非垃圾邮件之间单词 *make* 的频率存在何种差异？

    SELECT avg(word_freq_make), spam from data group by spam;

或，经常包含 *3d* 的电子邮件有什么特征？

    SELECT * from data order by word_freq_3d desc;

大多数 *3d* 出现率较高的电子邮件很明显是垃圾邮件，因此它可能是一个有用的特征，可用于构建预测模型来对电子邮件进行分类。

如果希望使用 PostgreSQL 数据库中存储的数据执行机器学习，请考虑使用 [MADlib](http://madlib.incubator.apache.org/)。

## <a name="sql-server-data-warehouse"></a>SQL Server 数据仓库
Azure SQL 数据仓库是一种基于云的向外扩展数据库，可以处理大量数据（关系数据和非关系数据）。 有关详细信息，请参阅[什么是 Azure SQL 数据仓库？](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

若要连接到数据仓库并创建表，请从命令提示符运行以下命令：

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

然后在 sqlcmd 提示符处：

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

使用 bcp 复制数据：

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> 下载的文件中的行尾为 Windows 样式，但 bcp 期望 UNIX 样式，因此我们需要告诉 bcp 使用-r 标志。
>
>

使用 sqlcmd 查询：

    select top 10 spam, char_freq_dollar from spam;
    GO

还可以使用 Squirrel SQL 查询。 按照类似于 PostgreSQL 的步骤，使用 Microsoft MSSQL Server JDBC 驱动程序（可在 ***/usr/share/java/jdbcdrivers/sqljdbc42.jar*** 中找到该驱动程序）。

## <a name="next-steps"></a>后续步骤
获取主题概述，了解包含在 Azure 中的数据科学过程的任务，请参阅[团队数据科学过程](http://aka.ms/datascienceprocess)。

有关针对特定方案，演示团队数据科学过程中的步骤的其他端到端演练的说明，请参阅[团队数据科学过程演练](../team-data-science-process/walkthroughs.md)。 该演练还展示了如何将云、本地工具以及服务结合到一个工作流或管道中，以创建智能应用程序。
