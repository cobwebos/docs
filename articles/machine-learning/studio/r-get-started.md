---
title: 将 R 用于 ML Studio （经典）-Azure
description: 使用此 R 编程教程开始使用 R 中的 Azure 机器学习 Studio （经典）来创建预测解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 665bb12c91c8d6a5a60fd8f60216f30131f34915
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982184"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>R 中的 Azure 机器学习 Studio （经典）入门

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->
本教程介绍如何使用机器学习工作室（经典）来创建、测试和执行 R 代码。 最后，您将拥有一个完整的预测解决方案。  

> [!div class="checklist"]
> * 为数据清除和转换创建代码。
> * 分析数据集中几个变量之间的相关性。
> * 为牛奶生产创建季节性时序预测模型。


Azure 机器学习工作室（经典版）包含许多功能强大的机器学习和数据操作模块。 借助 R 编程语言，此组合提供了通过 R 的灵活性和深度分析实现 Studio （经典）部署的可伸缩性和易用性。

预测是一种被广泛采用且非常有用的分析方法。 常见的用途包括预测季节性商品的销售、确定最优的库存级别和预测宏观经济变量。 通常使用时序模型进行预测。 时序数据是其中的值具有时间索引的数据。 时间索引可以是定期的，例如每月或每分钟，也可以是不定期的。 时序模型基于时序数据。 R 编程语言包含针对时序数据的灵活框架和广泛分析。

## <a name="get-the-data"></a>获取数据

在本教程中，您将使用加利福尼亚奶制品的生产和定价数据，其中包括有关多个奶制品产品的生产的每月信息以及牛奶脂肪的价格（一项基准测试）。

本文中使用的数据和 R 脚本可以从 [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 下载。 文件`cadairydata.csv`中的数据最初与威斯康星大学提供的信息有关[https://dairymarkets.com](https://dairymarkets.com)。



## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>在机器学习工作室（经典版）中与 R 语言交互

本部分介绍在机器学习工作室（经典版）环境中与 R 编程语言交互的一些基础知识。 R 语言提供了功能强大的工具，可用于在 Azure 机器学习工作室（经典版）环境中创建自定义的分析和数据操作模块。

我将使用 RStudio 开发、测试和调试小规模的 R 代码。 之后将此代码剪切和粘贴到随时可在 Azure 机器学习工作室（经典版）中运行的[执行 R 脚本][execute-r-script]模块。  

### <a name="the-execute-r-script-module"></a>执行 R 脚本模块

在机器学习工作室（经典版）中，R 脚本在[执行 R 脚本][execute-r-script]模块中运行。 图 1 显示了机器学习工作室（经典版）中的[执行 R 脚本][execute-r-script]模块的一个示例。

 ![R 编程语言：在机器学习 Studio 中选择的执行 R 脚本模块（经典）](./media/r-quickstart/fig1.png)

*图1。显示所选执行 R 脚本模块的机器学习 Studio （经典）环境。*

参考图 1，让我们看一些使用[执行 R 脚本][execute-r-script]模块的机器学习工作室（经典版）环境的主要部分。

* 用于试验的模块在中间窗格中显示。
* 右窗格的上半部分包含一个窗口可查看和编辑 R 脚本。  
* 右窗格的下半部分显示[执行 R 脚本][execute-r-script]的某些属性。 可以通过选择此窗格中的适当位置查看错误和输出日志。

当然，我们会在本文的其余部分更详细地讨论[执行 R 脚本][execute-r-script]。

使用复杂的 R 函数时，建议在 RStudio 中编辑、测试和调试。 与任何软件开发一样，逐步扩展代码，并在小型简单的测试用例上测试代码。 然后将函数剪切并粘贴到[执行 R 脚本][execute-r-script]模块的 R 脚本窗口中。 此方法允许利用 RStudio 集成开发环境 (IDE) 和 Azure 机器学习工作室（经典版）的强大功能。  

#### <a name="execute-r-code"></a>执行 R 代码

选择“运行”按钮运行此试验时，将执行[执行 R 脚本][execute-r-script]模块中的任何 R 代码。**** 执行完成后，在[执行 R 脚本][execute-r-script]图标上会显示一个对勾。

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>适用于 Azure 机器学习的防御性 R 编码

如果使用 Azure 机器学习工作室（经典版）为 Web 服务开发 R 代码，必须对代码如何处理意外的数据输入和异常进行规划。 为了保持代码的清晰度，在显示的大部分代码示例中未包含太多检查或异常处理方面的代码。 但是，后面我会提供几个使用 R 的异常处理功能的函数示例。  

如果需要 R 异常处理的更全面的内容，我建议阅读后面的[延伸阅读](#appendixb)中列出的由 Wickham 创作的书籍中的相关部分。

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>在机器学习工作室（经典版）中调试和测试 R

重申一下，我建议在 RStudio 中测试和调试小规模的 R 代码。 但是，在某些情况下需要跟踪[执行 R 脚本][execute-r-script]本身的 R 代码问题。 此外，在机器学习工作室（经典版）中检查结果是一个不错的做法。

在 Azure 机器学习工作室（经典版）平台上执行 R 代码的输出结果主要位于 output.log 文件中。 其他一些信息可在 error.log 中看到。  

如果运行 R 代码时机器学习工作室（经典版）发生错误，那么首先应该做的是查看 error.log 文件。 此文件包含有用的错误消息，可帮助你了解和纠正错误。 若要查看 error.log 文件，请选择包含错误的[执行 R 脚本][execute-r-script]的“属性”窗格上的“查看错误日志”。********

例如，我在[执行 R 脚本][execute-r-script]模块中运行下面的 R 代码，其中 y 为未定义变量：

```R
x <- 1.0
z <- x + y
```

此代码无法执行，导致出现错误。 选择“属性”窗格上的“查看错误日志”，弹出图 2 中显示的内容。********

  ![弹出错误消息](./media/r-quickstart/fig2.png)

*图2。错误消息弹出窗口。*

看来我们需要打开 output.log 文件查看此 R 错误消息。 选择[执行 R 脚本][execute-r-script]后，选择右侧的“属性”窗格上的“查看 output.log”。******** 打开了新的浏览器窗口，我看到以下内容。

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

此错误消息没有意料之外的内容，明确标识了问题所在。

要检查 R 中的任何对象的值，可以将这些值打印到 output.log 文件。 检查对象的值的规则本质上与交互式 R 会话中的相同。 例如，如果在行上键入一个变量名，则该对象的值将打印到 output.log 文件。  

#### <a name="packages-in-machine-learning-studio-classic"></a>机器学习工作室（经典版）中的包

工作室附带了超过 350 个预安装的 R 语言包。 可以在[执行 R 脚本][execute-r-script]模块中使用下面的代码来检索预安装的包列表。

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

如果此时不了解此代码的最后一行，请继续阅读。 本文剩余部分将全面讨论如何在工作室（经典版）环境中使用 R。

### <a name="introduction-to-rstudio"></a>RStudio 简介

RStudio 是广泛使用的针对 R 语言的集成开发环境 (IDE)。我会使用 RStudio 编辑、测试和调试本指南中使用的一些 R 代码。 测试并准备好 R 代码后，可以简单地将代码从 RStudio 编辑器剪切并粘贴到机器学习工作室（经典版）的[执行 R 脚本][execute-r-script]模块中。  

如果还没有在台式计算机上安装 R 编程语言，我建议立即安装。 在[https://www.r-project.org/](https://www.r-project.org/)的综合性 r 存档网络（CRAN）上提供了开源 r 语言的免费下载。 该网站提供了适用于 Windows、Mac OS 和 Linux/UNIX 的 R 语言下载。 请选择附近的网站镜像，并按照下载说明进行操作。 此外，CRAN 包含大量有用的分析和数据操作包。

如果是 RStudio 的新手，应下载并安装桌面版。 可以在 http://www.rstudio.com/products/RStudio/ 找到适用于 Windows、Mac OS 和 Linux/UNIX 的 RStudio 下载。 按照提供的说明在台式计算机上安装 RStudio。  

[使用 RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio) 中提供了 RStudio 的教程介绍。

我在下面的 [RStudio 指南文档](#appendixa)中提供了有关使用 RStudio 的更多信息。  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>获取执行 R 脚本模块中的输入和输出数据

本节我们讨论如何在[执行 R 脚本][execute-r-script]模块中输入和输出数据。 我们将复习如何在[执行 R 脚本][execute-r-script]模块中处理各种数据类型的输入和输出。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整代码。

### <a name="load-and-check-data"></a>加载和检查数据 

#### <a name="load-the-dataset"></a><a id="loading"></a>加载数据集

我们首先将 **csdairydata.csv** 文件载入 Azure 机器学习工作室（经典版）。

1. 启动 Azure 机器学习工作室（经典版）环境。
1. 选择屏幕左下方的“+ 新建”****，并选择“数据集”****。
1. 选择“从本地文件”****，并单击“浏览”**** 以选择文件。
1. 请确保选择了**具有标题 (.csv) 的通用 CSV 文件**作为数据集的类型。
1. 选择勾选标记。
1. 上传数据集后，可选择“数据集”选项卡查看新数据集。****  

#### <a name="create-an-experiment"></a>创建试验

现在，机器学习工作室（经典版）中已有一些数据，我们需要创建一个试验来执行分析。  

1. 选择左下方的“+ 新建”****，并依次选择“试验”**** 和“空白试验”****。
1. 可以命名试验，方法是选择和修改页面顶部的“在...创建的试验”**** 标题。 例如，将其改为“CA 乳品分析”****。
1. 在试验页面的左侧，展开“保存的数据集”****，并选择“我的数据集”****。 可看到之前上传的 **cadairydata.csv**。
1. 将 **csdairydata.csv 数据集**拖放到试验。
1. 在左窗格顶部的“搜索试验项”**** 框中，键入[执行 R 脚本][execute-r-script]。 可以看到模块在搜索列表中显示。
1. 将[执行 R 脚本][execute-r-script]拖放到托盘上。  
1. 将 **csdairydata.csv 数据集**的输出连接到[执行 R 脚本][execute-r-script]最左侧输入 (**Dataset1**)。
1. **别忘了选择“保存”！**  

此时试验如图 3 所示。

![具有数据集和执行 R 脚本模块的 CA 乳品分析试验](./media/r-quickstart/fig3.png)

*图3：CA 奶制品分析会试验数据集和执行 R 脚本模块。*

#### <a name="check-on-the-data"></a>查看数据

让我们看看已加载到试验中的数据。 在该试验中，选择 **cadairydata.csv 数据集**的输出，并选择“可视化”****。 可看到类似图 4 的内容。  

![cadairydata.csv 数据集摘要](./media/r-quickstart/fig4.png)

*图4：Cadairydata.csv 数据集的摘要。*

在此视图中可看到大量有用的信息。 可以看到该数据集的前几个行。 如果选择一列，“统计信息”部分显示有关该列的详细信息。 例如，“特征类型”行显示了 Azure 机器学习工作室（经典版）分配给列的数据类型。 在开始执行任何重要的工作之前，像这样快速浏览数据是一种很好的数据完好性检查方式。

### <a name="first-r-script"></a>第一个 R 脚本

让我们创建第一个简单的 R 脚本，以便在 Azure 机器学习工作室（经典版）中进行试验。 我已在 RStudio 中创建并测试了以下脚本。  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

现在，需要将此脚本移到 Azure 机器学习工作室（经典版）。 我只需要剪切并粘贴。 但是，在该例中，我通过 zip 文件传输 R 脚本。

### <a name="data-input-to-the-execute-r-script-module"></a>输入到执行 R 脚本模块的数据

让我们看看[执行 R 脚本][execute-r-script]模块的输入。 在本示例中，我们向[执行 R 脚本][execute-r-script]模块读入加利福尼亚州乳品数据。  

[执行 R 脚本][execute-r-script]模块可能有三个输入。 可以使用任何一个或所有输入，具体取决于应用程序。 使用没有任何输入的 R 脚本也是非常合理的。  

让我们从左到右查看每个输入。 将光标置于输入上并阅读工具提示可看到每个输入的名称。  

#### <a name="script-bundle"></a>脚本包

脚本包输入允许将 zip 文件的内容传递到[执行 R 脚本][execute-r-script]模块。 可以使用以下任一命令将 zip 文件的内容读入 R 代码。

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure 机器学习工作室（经典版）将 zip 中的文件视为位于 src/ 目录中，因此文件名需要有此目录名称前缀。 例如，如果 zip 包含位于该 zip 根目录下的文件 `yourfile.R` 和 `yourData.rdata`，那么在使用 `source` 和 `load` 时会这两个文件处理为 `src/yourfile.R` 和 `src/yourData.rdata`。

我们已经在[加载数据集](#loading)中讨论过加载数据集。 一旦创建并测试上一节中所示的 R 脚本后，请执行以下操作：

1. 将 R 脚本保存到 R 文件。 调用脚本文件“simpleplot.R”。 内容如下：

   ```R
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. 创建一个 zip 文件，并将脚本复制到此 zip 文件。 在 Windows 上，右键单击该文件并选择“发送到”****，并选择“压缩文件夹”****。 此时将创建包含"simpleplot.R"文件的新的 zip 文件。

1. 将文件添加到 Azure 机器学习工作室（经典版）中的**数据集**，并指定类型为 **zip**。 现在可在数据集中看到此 zip 文件。

1. 将 zip 文件从**数据集**拖放到**机器学习工作室（经典版）画布**。

1. 将 **zip 数据**输出图标连接到[执行 R 脚本][execute-r-script]模块的**脚本包**输入。

1. 在[执行 R 脚本][execute-r-script]模块的代码窗口中键入具有 zip 文件名的 `source()` 函数。 在本例中我键入 `source("src/simpleplot.R")`。  

1. 请务必选择“保存”。****

这些步骤完成后，运行试验时，[执行 R 脚本][execute-r-script]模块将执行 zip 文件中的 R 脚本。 此时试验如图 5 所示。

![使用压缩的 R 脚本的试验](./media/r-quickstart/fig6.png)

*图5。使用压缩的 R 脚本进行试验。*

#### <a name="dataset1"></a>Dataset1

使用 Dataset1 输入可以将表格数据传递到 R 代码。 在我们的简单脚本中，`maml.mapInputPort(1)` 函数从端口 1 读取数据。 之后将此数据分配到代码中的数据框变量名。 在我们的简单脚本中的第一行代码执行了此分配。

```R
cadairydata <- maml.mapInputPort(1)
```

选择“运行”按钮执行试验。**** 执行完成后，选择[执行 R 脚本][execute-r-script]模块，并选择“属性”窗格上的“查看 output.log”。**** 浏览器中打开新的页面，其中显示 output.log 文件的内容。 向下滚动页面时会看到类似下面的内容：

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

继续向下滚动页面会显示列的详细信息，其内容如下所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

这些结果大多和预期的一样，数据框中包含 228 个观测值和 9 个列。 我们可以看到列名称、R 数据类型和每个列的示例。

> [!NOTE]
> 可以从[执行 R 脚本][execute-r-script]模块的 R 设备输出中方便地获取此相同的打印输出。 我们会在下一节讨论[执行 R 脚本][execute-r-script]模块的输出。  

#### <a name="dataset2"></a>Dataset2

Dataset2 的输入行为与 Dataset1 相同。 使用此输入可以将另一个表格数据传递到 R 代码。 使用具有参数 2 的函数 `maml.mapInputPort(2)` 传递此数据。  

### <a name="execute-r-script-outputs"></a>执行 R 脚本的输出

#### <a name="output-a-dataframe"></a>输出数据框

可以使用 `maml.mapOutputPort()` 函数，通过结果 Dataset1 端口将 R 数据框的内容输出为表格。 在我们的简单 R 脚本中，由以下行执行此操作。

```
maml.mapOutputPort('cadairydata')
```

运行试验之后，选择结果 Dataset1 输出端口，并选择“可视化”****。 可看到类似图 6 的内容。

![加利福尼亚州乳品数据输出的可视化](./media/r-quickstart/fig7.png)

*图6。加利福尼亚奶制品数据输出的可视化。*

该输出看起来和输入一样，和我们的预期完全相同。  

### <a name="r-device-output"></a>R 设备输出

[执行 R 脚本][execute-r-script]模块的设备输出包含消息和图形输出。 R 的标准输出和标准错误消息都会发送到 R 设备输出端口。  

要查看 R 设备输出，请选择该端口，并选择“可视化”****。 在图 7 中可看到 R 脚本的标准输出和标准错误。

![来自 R 设备端口的标准输出和标准错误](./media/r-quickstart/fig8.png)

*图7：来自 R 设备端口的标准输出和标准错误。*

向下滚动页面可看到图 8 中显示的 R 脚本的图形输出。  

![来自 R 设备端口的图形输出](./media/r-quickstart/fig9.png)

*图8：来自 R 设备端口的图形输出。*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>数据筛选和转换

本节将对加利福尼亚州乳品数据执行一些基本的数据筛选和转换操作。 本节末尾我们将具有适合生成分析模型的格式的数据。  

更具体地说，本节我们将执行几种常见的数据清理和转换任务：类型转换、筛选数据框、添加新的计算列，以及值的转换。 此背景知识可帮助你应对遇到的各种实际问题。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="type-transformations"></a>类型转换

现在，我们可以将加利福尼亚州乳品数据读入[执行 R 脚本][execute-r-script]模块中的 R 代码，我们需要确保列中的数据具有预期的类型和格式。  

R 是一个具有动态类型的语言，这表示可以根据需要将数据类型从一种类型强制转换为另一种类型。 R 的基本数据类型包括数字、逻辑和字符。 因子类型用于以简洁方式存储分类数据。 可以在后面的[延伸阅读](#appendixb)的参考文献中找到有关数据类型的更多信息。

将外部源中的表格数据读入 R 后，最好检查列的结果类型。 可能想要字符类型的列，但在许多情况下则显示为因子，反之亦然。 在其他情况下，您认为的列应该是数字，如 "1.23"，而不是 "1.23" 作为浮点数。  

幸运的是，只要可以映射，就可以轻松地将一种类型转换为另一种。 例如，无法将“Nevada”转换为数值，但可以将其转换为一个因子（分类变量）。 再举一例，可以将数值 1 转换为字符“1”或一个因子。  

所有这些转换的语法非常简单：`as.datatype()`。 这些类型转换函数包括：

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

看一看上一节中输入的列的数据类型：所有列都为数值类型，除了标记为“Month”的列为字符类型。 让我们将该列转换为一个因子并测试结果。  

我删除了创建散点图矩阵的行，并添加了将“Month”列转换为因子的行。 在试验中我只需将 R 代码剪切并粘贴到[执行 R 脚本][execute-r-script]模块的代码窗口中。 你也可以更新 zip 文件，并将其上传到 Azure 机器学习工作室（经典版）中，但这需要几个步骤。  

```R
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

让我们来执行此代码，并查看 R 脚本的输出日志。 图 9 中显示了日志中的相关数据。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*图9：带有系数变量的数据帧的摘要。*

现在 Month 的类型显示为**具有 14 个级别的因子**。 由于一年只有 12 个月，因此这是一个问题。 还可以查看该类型在结果数据集端口的“可视化”**** 中为“分类”****。

问题在于“Month”列未系统地进行编码。 在某些情况下，月称为四月，在其他情况下，缩写为 Apr。我们可以通过将字符串裁剪为3个字符来解决此问题。 现在代码行如下所示：

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

重新运行实验，并查看输出日志。 预期的结果如图 10 所示。  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*图10：具有正确系数级别的数据帧的摘要。*

现在因子变量具有所需的 12 个级别。

### <a name="basic-data-frame-filtering"></a>基本数据框筛选

R 数据框支持强大的筛选功能。 对行或列使用逻辑筛选器可以将数据集划分为多个子集。 在许多情况下，都需要复杂的筛选条件。 后面的[延伸阅读](#appendixb)中的参考文献包含筛选数据框的许多示例。  

我们要对数据集进行一点筛选。 如果看一下 cadairydata 数据框中的列，会发现两个不必要的列。 第一列只包含一行数字，不是很有用。 第二列 Year.Month 包含冗余的信息。 使用以下 R 代码可轻松地排除这两列。

> [!NOTE]
> 本节从现在开始，我只展示在[执行 R 脚本][execute-r-script]模块中添加的附加代码。 我会在 `str()` 函数**前面**添加一个新行。 我使用此函数验证 Azure 机器学习工作室（经典版）中的结果。

将以下行添加到[执行 R 脚本][execute-r-script]模块中的我的 R 代码。

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

在试验中运行此代码，并检查输出日志中的结果。 结果如图 11 所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*图11：已删除两个列的数据帧的摘要。*

好消息！ 我们得到了预期结果。

### <a name="add-a-new-column"></a>添加新列

若要创建时序模型，则具有包含从该时序的起始时间开始的月份的列会使创建更方便。 我们将创建一个新列“Month.Count”。

为了有利于组织代码，我们将创建第一个简单的函数 `num.month()`。 然后将此函数应用于在数据框中创建一个新列。 新代码如下所示：

```R
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

现在运行更新的试验，并使用输出日志查看结果。 结果如图 12 所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*图12：具有其他列的数据帧的摘要。*

看起来一切正常。 数据框中包含了具有预期值的新列。

### <a name="value-transformations"></a>值转换

本节我们将对数据框的一些列的值执行简单转换。 R 语言支持几乎所有值的转换。 后面的[延伸阅读](#appendixb)中的参考文献包含许多示例。

如果看一下数据框摘要中的值，会发现一些奇怪的地方。 加利福尼亚州生产的冰激凌比牛奶还多吗？ 当然不是，这种说法毫无意义，这会让我们的一些冰激凌爱好者感到伤心。 这些数值的单位不同。 价格是以美制磅为单位计算的，牛奶的单位为 1 M 美制磅，冰激凌的单位为 1,000 美制加仑，奶酪的单位为 1,000 美制磅。 假设冰激凌每加仑的重量为 6.5 磅，那么我们可以轻松地使用乘法转换这些值，以便使它们的单位都为 1,000 磅。

对于预测模型，我们对数据的趋势和季节性调整使用乘法模型。 对数转换可以使用线性模型，从而简化了此过程。 可以在应用乘数的相同函数中应用对数转换。

在下面的代码中，我定义了一个新函数 `log.transform()`，并将其应用于包含数值的行。 R `Map()` 函数用于将 `log.transform()` 函数应用到数据框的所选列。 `Map()` 的功能类似于 `apply()`，但是前者允许函数具有多个参数列表。 请注意，乘数列表向 `log.transform()` 函数提供了另一个参数。 `na.omit()` 函数用于进行一些清理，以确保在数据框中没有缺失或未定义的值。

```R
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warningmessage to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

`log.transform()` 函数中有很多代码。 此代码的大部分在检查参数的潜在问题，或者处理在计算中可能还会出现的异常。 只有几行代码实际执行了计算。

防御性编程的目的是要防止使处理无法继续的单个函数的失败。 长时间运行的分析突然失败会让用户非常沮丧。 若要避免这种情况，必须选择默认返回值，以限制对下游处理的危害。 同时生成一条消息警告用户某些地方出现错误。

如果不习惯于 R 中的防御性编程，那么所有这些代码可能看起来有点让人难以招架。 我将讲解以下主要步骤：

1. 定义了四条消息的向量。 这些消息用于传达有关此代码可能发生的一些错误和异常的信息。
2. 对于每种情况返回值 NA。 还有许多其他的值的副作用较少。 例如，可以返回零向量，或原始输入向量。
3. 针对函数的参数运行检查。 在每种情况下，如果检测到错误，则返回默认值，并由 `warning()` 函数生成一条消息。 我使用 `warning()` 而不是 `stop()`，因为后者会终止执行，而这正是我要尝试避免的。 请注意，我采用面向过程的风格编写此代码，因为在本例中函数式方法显得复杂且混淆不清。
4. `tryCatch()` 中包装了对数计算，因此异常不会导致处理突然停止。 如果没有 `tryCatch()`，R 函数引发的大多数错误会导致产生停止信号，这个信号会导致处理停止。

在试验中执行此 R 代码，并在 output.log 文件中查看打印输出。 现在可在日志中看到四个列的转换后的值，如图 13 所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*图13：数据帧中已转换的值的摘要。*

可以看到值已被转换。 现在牛奶生产大大超过了所有其他乳品生产，回忆一下，我们现在是以对数尺度查看数据。

此时数据已清理，我们可以进行一些建模操作。 查看[执行 R 脚本][execute-r-script]模块的结果数据集输出的可视化摘要，会看到“Month”列的类型为“分类”，具有 12 个唯一值，这又和我们希望的一样。

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>时序对象和相关性分析

本节我们将探讨几个基本的 R 时序对象，并分析一些变量之间的相关性。 我们的目的是输出包含几个滞后时间内的成对相关性信息的数据框。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="time-series-objects-in-r"></a>R 中的时序对象

如前所述，时序对象是一系列具有时间索引的数据值。 R 时序对象用于创建和管理时间索引。 使用时序对象有几个优点。 时序对象可以省去管理封装在对象中的时序索引值的许多细节。 此外，时序对象允许使用许多时序方法进行绘图、打印、建模等。

通常使用 POSIXct 时序类，且该类相对比较简单。 此时序类的时间测量起始点为 1970 年 1 月 1 日。 在本示例中我们使用 POSIXct 时序对象。 其他广泛使用的 R 时序对象类包括 zoo 和 xts 等可扩展时序对象。

### <a name="time-series-object-example"></a>时序对象示例

让我们开始使用示例。 将**新**的[执行 R 脚本][execute-r-script]模块拖放到试验中。 将现有的[执行 R 脚本][execute-r-script]模块的结果 Dataset1 输出端口连接到新的[执行 R 脚本][execute-r-script]模块的 Dataset1 输入端口。

正如第一个示例中所演示的，在完成示例中的步骤时，在某些时候我只展示每个步骤中 R 代码的附加行。  

#### <a name="reading-the-dataframe"></a>读取数据框

第一步，读取数据框，并确保得到预期的结果。 下面的代码可执行此作业。

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

现在，运行试验。 新的执行 R 脚本形状的日志应如图 14 所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*图14。执行 R 脚本模块中的数据帧摘要。*

此数据具有预期的类型和格式。 请注意“Month”列的类型为因子，并且具有预期的级别数。

#### <a name="creating-a-time-series-object"></a>创建时序对象

我们需要向数据框中添加一个时序对象。 将当前的代码替换为以下代码，以添加 POSIXct 类的新列。

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

现在，检查日志。 日志如图 15 所示：

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*图15：带有时序对象的数据帧的摘要。*

从此摘要中可以看到新列实际为 POSIXct 类。

### <a name="exploring-and-transforming-the-data"></a>浏览和转换数据

让我们探讨一下此数据集的一些变量。 散点图矩阵是一个可供快速查看的好办法。 我将前面的 R 代码中的 `str()` 函数替换为以下行。

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

运行此代码，并看看结果。 R 设备端口生成的绘图如图 16 所示。

![所选变量的散点图矩阵](./media/r-quickstart/fig17.png)

*图16：所选变量的散点图矩阵。*

这些变量之间的关系的结构看起来有些奇怪。 这可能是因为数据的趋势和我们未标准化变量的事实。

### <a name="correlation-analysis"></a>相关性分析

若要执行相关性分析，需要对变量进行去趋势和标准化处理。 我们只需使用 R `scale()` 函数，该函数可以同时对变量进行中心化和规范化处理。 该函数可能运行较快。 但是，我想要展示 R 中的防御性编程示例。

以下显示的 `ts.detrend()` 函数执行了这两个操作。 以下两行代码对数据进行去趋势处理，并标准化值。

```R
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

`ts.detrend()` 函数中有很多代码。 此代码的大部分在检查参数的潜在问题，或者处理在计算中可能还会出现的异常。 只有几行代码实际执行了计算。

我们在值转换中已经讨论过防御性编程示例。 这两个计算代码块包装在 `tryCatch()` 中。 对于某些错误，返回原始的输入向量是有意义的，而在其他情况，则返回零向量。  

请注意，用于去趋势的线性回归是一个时序回归。 预测器变量是一个时序对象。  

定义 `ts.detrend()` 函数后，就将其应用到数据框中的相关变量。 必须使用 `as.data.frame()` 将 `lapply()` 创建的结果列表强制转换为数据框。 由于 `ts.detrend()` 的防御性，处理一个变量的失败不会阻止其他变量的正常处理。  

最后一行代码创建了成对的散点图。 运行此 R 代码后，散点图的结果如图 17 所示。

![去趋势和标准化时序的成对散点图](./media/r-quickstart/fig18.png)

*图17。趋势化和标准化时序的成对散点图。*

可以将此结果与图 16 中所示结果进行比较。 去除趋势和标准化变量后，这些变量之间的关系结构就不那么明显了。

以 R ccf 对象形式计算相关性的代码如下：

```R
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

运行此代码生成的日志如图 18 所示。

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*图18。成对相关分析中的 ccf 对象列表。*

每个滞后时间有一个相关性值。 这些相关性值没有一个足够大而具有意义。 因此可以确定我们可以单独对每个变量建模。

### <a name="output-a-dataframe"></a>输出数据框
我们已经以 R ccf 对象列表的形式计算成对相关性。 这种方法有一点问题，因为结果数据集输出端口确实需要数据框。 而且，ccf 对象本身就是一个列表，而我们只需要该列表中第一个元素的值，即各种滞后时间中的相关性。

以下代码从 ccf 对象列表中提取滞后时间值，这些值本身就是列表。

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

第一行代码有点复杂，这里的一些解释可以帮助你理解它。 对该代码进行从内到外的解释，如下：

1. **[[** 运算符及参数 **1** 用于从 ccf 对象列表的第一个元素中选择滞后时间中的相关性向量。
2. `do.call()` 函数对 `lapply()` 返回的列表的元素应用 `rbind()` 函数。
3. `data.frame()` 函数将 `do.call()` 生成的结果强制转换为数据框。

请注意，行名在数据框的一个列中。 这样使行名从[执行 R 脚本][execute-r-script]中输出后保留这些行名。

运行此代码并**可视化**结果数据集端口的输出，可生成如图 19 所示的输出。 和预期的一样，行名在第一列中。

![相关性分析的结果输出](./media/r-quickstart/fig20.png)

*图19。来自相关性分析的结果输出。*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>时序示例：季节性预测

现在我们的数据格式已适合分析，并且我们已确定变量之间没有显著的相关性。 让我们继续并创建时序预测模型。 使用此模型可以预测 2013年 12 个月的加利福尼亚州的牛奶生产。

我们的预测模型包含两个成分：趋势成分和季节性成分。 完满的预测是这两个成分的乘积。 此类型模型被称为乘法模型。 另一种可供选择的模型为加法模型。 我们已经对相关变量应用了对数转换，这样可以使分析变得容易。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="creating-the-dataframe-for-analysis"></a>创建用于分析的数据框

首先将新的**new** [执行 R 脚本][execute-r-script]模块添加到试验中。 将现有的[执行 R 脚本][execute-r-script]模块的**结果数据集**输出连接到新的模块的 **Dataset1** 输入。 结果如图 20 所示。

![已添加新执行 R 脚本模块的实验](./media/r-quickstart/fig21.png)

*图20：添加了新的执行 R 脚本模块的实验。*

与刚完成的相关分析一样，我们需要添加包含 POSIXct 时间序列对象的列。 以下代码将执行此操作。

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

运行此代码并查看日志。 结果如图 21 所示。

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*图21：数据帧的摘要。*

凭借此结果，我们可以开始进行分析。

### <a name="create-a-training-dataset"></a>创建训练数据集

构建数据框后，我们需要创建训练数据集。 该数据包含所有观测值，2013 年的 12 个月的数据除外，2013 年数据是测试数据集。 以下代码将数据框划分为不同子集，并创建乳品生产和价格变量的图形。 然后，我创建了四个生产和价格变量的图形。 在 `Map()` 函数中使用匿名函数定义图形的一些参数，并遍历其他两个参数的列表。 如果觉得此处适合使用 for 循环，那么你是对的。 但是，由于 R 是函数式语言，因此我将向你展示函数式方法。

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

运行此代码生成 R 设备输出的系列时序图，如图 22 所示。 请注意，时间轴的单位为日期，这是时序图方法的一大优点。

![加利福尼亚州乳品生产和价格数据的第一个时序图](./media/r-quickstart/unnamed-chunk-161.png)

![加利福尼亚州乳品生产和价格数据的第二个时序图](./media/r-quickstart/unnamed-chunk-162.png)

![加利福尼亚州乳品生产和价格数据的第三个时序图](./media/r-quickstart/unnamed-chunk-163.png)

![加利福尼亚州乳品生产和价格数据的第四个时序图](./media/r-quickstart/unnamed-chunk-164.png)

*图22：加利福尼亚奶制品生产和价格数据的时序图。*

### <a name="a-trend-model"></a>趋势模型

创建时序对象和查看数据后，让我们开始构造加利福尼亚州牛奶生产数据的趋势模型。 我们可以使用时序回归来执行此操作。 但是，从图中可以清晰地看出，我们需要不止一个斜率和截距来精确地为训练数据中观察到的趋势建模。

考虑到数据规模较小，我可以在 RStudio 中构建趋势模型，然后将生成的模型剪切并粘贴到 Azure 机器学习工作室（经典版）。 RStudio 为此类型的交互式分析提供了交互式环境。

在第一次尝试中，我使用 3 次多项式回归。 存在过拟合这些类型的模型的实际危险。 因此，最好避免使用高阶项。 `I()` 函数禁止解释内容（即“按原样”解释内容），并且允许在回归等式中编写按字面意思解释的函数。

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

此代码生成以下结果。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

从该输出中的 P 值 (`Pr(>|t|)`) 来看，平方项可能并不重要。 我使用 `update()` 函数删除平方项来修改此模型。

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

此代码生成以下结果。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

这个结果看起来好多了。 所有项都重要。 但是，2e-16 值是默认值，不必太关心。  

在完好性测试中，创建加利福尼亚州乳品生产数据的带趋势曲线的时序图。 我在 Azure 机器学习工作室（经典版）的[执行 R 脚本][execute-r-script]模型（而不是 RStudio）中添加了以下代码，用于创建模型及绘图。 结果如图 23 所示。

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![显示趋势模型的加利福尼亚州牛奶生产数据](./media/r-quickstart/unnamed-chunk-18.png)

*图23：加利福尼亚用显示的趋势模型来模拟生产数据。*

从图上看来趋势模型很好地拟合了数据。 而且，似乎并不存在过度拟合的证据，如模型曲线中的异常摆动。  

### <a name="seasonal-model"></a>季节性模型

有了趋势模型之后，我们需要继续深入并考虑季节效应。 我们使用一年中的月份作为线性模型中的虚拟变量，用于捕获按月效应。 请注意，在模型中引入因子变量时，不得计算截距。 否则，则会过多指定公式的参数，R 将删除一个所需因子，但保留截距项。

既然我们有令人满意的趋势模型，则可以使用 `update()` 函数向现有模型添加新的项。 更新公式中的为 -1 删除截距项。 此时在 RStudio 中继续运行：

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

此代码生成以下结果。

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

我们看到该模型不再具有截距项，并且有 12 个重要的月因子。 这正是我们想要看到的。

让我们创建加利福尼亚州乳品生产数据的另一个时序图，以便了解季节性模型的运行情况。 我在 Azure 机器学习工作室（经典版）的[执行 R 脚本][execute-r-script]中添加了以下代码，用于创建模型及绘图。

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

在 Azure 机器学习工作室（经典版）中运行此代码生成的绘图如图 24 所示。

![包含季节性效应的加利福尼亚州牛奶生产模型](./media/r-quickstart/unnamed-chunk-20.png)

*图24：加利福尼亚牛奶生产模型，其中包括季节性效应。*

图 24 中显示的数据拟合相当鼓舞人心。 趋势和季节性效应（月度变体）看起来合理。

作为模型的另一项检查，让我们看看残差。 以下代码从两个模型中计算预测值、计算季节性模型的残差，并绘制训练数据的残差图。

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

残差图如图 25 所示。

![训练数据的季节性模型的残差](./media/r-quickstart/unnamed-chunk-21.png)

*图25：定型数据的季节性模型的残差。*

这些残差看起来合理。 这些残差没有特定的结构，2008-2009 年经济衰退期的影响除外，我们的模型未能很好地解释这一影响。

图 25 中所示图形有助于检测残差中的任何时间依赖的模式。 我使用的计算和绘制残差的显式方法在图中按时间顺序放置残差。 另一方面，如果已绘制 `milk.lm$residuals` 图，则该图不按时间顺序放置残差。

还可以使用 `plot.lm()` 生成一系列诊断图。

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

此代码生成一系列诊断图，如图 26 所示。

![季节性模型的第一个诊断图](./media/r-quickstart/unnamed-chunk-221.png)

![季节性模型的第二个诊断图](./media/r-quickstart/unnamed-chunk-222.png)

![季节性模型的第三个诊断图](./media/r-quickstart/unnamed-chunk-223.png)

![季节性模型的第四个诊断图](./media/r-quickstart/unnamed-chunk-224.png)

*图26：季节性模型的诊断图。*

这些图中标识了几个极具影响力的点，但是没有任何一个点引起高度关注。 此外，从 Normal Q-Q 图中可以看出残差接近正态分布，这是线性模型的一个重要假设。

### <a name="forecasting-and-model-evaluation"></a>预测和模型评估

完成本示例只剩一件事要做。 我们需要计算预测值，并且计算预测值和实际数据的误差。 我们的预测针对的是 2013年 12 个月。 我们可以计算该预测值与实际数据的误差，该实际数据不属于训练数据集的一部分。 此外，可以比较 18 年的训练数据和 12 个月的测试数据的性能。  

大量指标用于测量时序模型的性能。 在我们的示例中将使用均方根 (RMS) 误差。 以下函数计算两个序列之间的 RMS 误差。  

```R
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

和“值转换”一节中讨论的 `log.transform()` 函数一样，该函数也存在很多错误检查和异常恢复代码。 两者采用的原理是相同的。 在 `tryCatch()` 函数中包装的两个位置中完成操作。 首先，对时序取幂，因为我们在使用值的对数。 其次，计算实际的 RMS 误差。  

配备一个函数来测量 RMS 误差，生成并输出包含 RMS 误差的数据框。 我们将单独为趋势模型包括项和具有季节性因子的完整模型包括项。 以下代码通过使用构造的两个线性模型完成此作业。

```R
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

运行此代码在结果数据集输出端口生成如图 27 所示的输出。

![模型的 RMS 误差比较](./media/r-quickstart/fig26.png)

*图27。模型的 RMS 错误比较。*

从这些结果可以看出，向模型中添加季节性因子使 RMS 误差显著减少。 不出所料，训练数据的 RMS 误差略小于预测数据的误差。

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>RStudio 指南文档

RStudio 随附了齐备的文档。 下面提供了 RStudio 文档中关键部分的链接，以帮助你入门。

* **创建项目** - 通过使用 RStudio 可以在项目中组织和管理 R 代码。 有关详细信息，请参阅[使用项目](https://support.rstudio.com/hc/articles/200526207-Using-Projects)。 我建议按照文档中的说明进行操作，并为本文中的 R 代码示例创建项目。  
* **编辑和执行 R 代码** - RStudio 提供了一个集成环境，用于编辑和执行 R 代码。 有关详细信息，请参阅[编辑和执行代码](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)。
* **调试** - RStudio 具有强大的调试功能。 有关这些功能的详细信息，请参阅[使用 RStudio 进行调试](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)。 有关断点故障排除功能的信息，请参阅[断点故障排除](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)。

## <a name="further-reading"></a><a id="appendixb"></a>进一步阅读

本 R 编程教程介绍了在 Azure 机器学习工作室（经典版）中使用 R 语言所需的基础知识。 如果不熟悉 R 语言，CRAN 上提供了两个简介：

* 可以从 Emmanuel Paradis 撰写的 [R for Beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf)（面向初学者的 R 语言）入手。  
* [R](https://cran.r-project.org/doc/manuals/R-intro.html) by W 的简介。 Venables et. al.） 更深入地介绍了相关知识。

有许多关于 R 的书籍可以帮助你入门。 以下是我发现的一些有用书籍：

* R 编程的一种方式： Norman Matloff 的**统计软件设计的教程**是对 R 编程的出色介绍。  
* Paul Teetor 的**r 手册**提供使用 r 的问题和解决方案方法。  
* **R In** Robert Kabacoff 是另一个有用的简介书籍。 相伴的 [Quick R 网站](https://www.statmethods.net/)是一个很有用的资源。
* Patrick Burns 撰写的 **R Inferno** 是一本非常幽默的书，书中介绍了如何应对 R 编程中可能遇到的许多棘手而困难的问题。可从 [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/) 免费获取此书籍。
* 如果希望深入了解 R 中的高级主题，请参阅 Hadley Wickham 的书籍**高级 R** 。 本书籍的联机版本免费提供[http://adv-r.had.co.nz/](http://adv-r.had.co.nz/)。

可以在[CRAN 任务视图：时序分析](https://cran.r-project.org/web/views/TimeSeries.html)中找到 R 时序包的目录。 有关特定时序对象包的信息，请参考此包的文档。

使用 R by Paul Cowpertwait 和 Andrew Metcalfe 的书籍**介绍时间系列**介绍了如何使用 r 进行时序分析。 还有更多的理论性文字提供了 R 示例。

下面是一些很好的 Internet 资源：

* DataCamp 通过视频课程和编码练习在舒适的浏览器中教授 R。 DataCamp 提供了关于最新的 R 技术和包的互动式课程。 获取免费的[交互式 R 教程](https://www.datacamp.com/courses/introduction-to-r)。
* Programiz 提供的 [Learn R Programming, The Definitive Guide](https://www.programiz.com/r-programming)（学习 R 编程 - 权威指南）。
* Clarkson 大学的王黑的快速[R 教程](https://www.cyclismo.org/tutorial/R/)。
* [Top R language resources to improve your data skills](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)（用于改善数据技能的顶级 R 语言资源）中列出了 60 多个 R 资源。

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
