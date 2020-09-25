---
title: 将 R 与机器学习 Studio (经典) 配合使用-Azure
description: 通过本 R 编程教程，在 Azure 机器学习工作室（经典版）中使用 R 创建预测解决方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 133c7e95e620bfea51d1d6c9f6fd1d2946eeca33
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91341698"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>在 Azure 机器学习工作室（经典版）中使用 R

**适用于：**  ![yes](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![no](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../compare-azure-ml-to-studio-classic.md)


<!-- Stephen F Elston, Ph.D. -->
本教程介绍如何使用 Azure 机器学习 Studio (经典) 来创建、测试和执行 R 代码。 最终，你将拥有一个完整的预测解决方案。

> [!div class="checklist"]
> * 创建用于数据清理和转换的代码。
> * 分析数据集中几个变量之间的相关性。
> * 创建牛奶生产的季节性时序预测模型。


机器学习 Studio (经典) 包含许多功能强大的机器学习和数据操作模块。 借助 R 编程语言，此组合提供了易用性和易用性，可轻松部署机器学习 Studio (经典) ，以及 R 的灵活性和深度分析。

预测是一种广泛使用的有用分析方法。 常见使用范围从预测季节性项的销售额和确定最佳库存级别以预测 macroeconomic 变量。 通常使用时序模型进行预测。 时序数据是其中的值具有时间索引的数据。 时间索引可以是常规的，例如每个月或每分钟。 时间索引也可以是不规则的。 时序模型基于时序数据。 R 编程语言包含针对时序数据的灵活框架和广泛分析。

## <a name="get-the-data"></a>获取数据

在本教程中，你将使用加利福尼亚州牛奶生产和定价数据，其中包括有关多个牛奶产品的生产的每月信息和牛奶脂肪价格，这是一项基准测试。

本文中使用的数据和 R 脚本可以从 [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 下载。 文件中的数据 `cadairydata.csv` 最初是从威斯康星 [奶制品市场网站](https://dairymarkets.com)大学提供的信息合成而来的。

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>在机器学习工作室（经典版）中与 R 语言交互

本部分介绍在机器学习工作室（经典版）环境中与 R 编程语言交互的一些基础知识。 R 语言提供了一个功能强大的工具，可用于在机器学习 Studio (经典) 环境中创建自定义的分析和数据操作模块。

我们将使用 RStudio 开发、测试和调试小规模的 R 代码。 然后，将此代码剪切并粘贴到 [执行 R 脚本][execute-r-script] 模块中，以便在机器学习 Studio (经典) 中运行。

### <a name="the-execute-r-script-module"></a>执行 R 脚本模块

在机器学习工作室（经典版）中，R 脚本在[执行 R 脚本][execute-r-script]模块中运行。 机器学习 Studio 中的 " [执行 R 脚本][execute-r-script] " 模块 (经典) 如下所示。

 ![显示 R 编程语言的屏幕截图：在机器学习 Studio 中选择的 "执行 R 脚本" 模块 (经典) "。](./media/r-quickstart/fig1.png)

上图显示了机器学习 Studio (经典) 环境中用于 [执行 R 脚本][execute-r-script] 模块的一些关键部分：

* 用于试验的模块在中间窗格中显示。
* 右窗格的上半部分包含可用于查看和编辑 R 脚本的窗口。
* 右窗格的下半部分显示 [执行 R 脚本][execute-r-script]的一些属性。 您可以通过选择此窗格的相应区域来查看错误和输出日志。

本文的其余部分将更详细地讨论 [执行 R 脚本][execute-r-script] 。

使用复杂的 R 函数时，建议你在 RStudio 中编辑、测试和调试。 与任何软件开发一样，以增量方式扩展代码，并在小型、简单的测试用例上对其进行测试。 然后将函数剪切并粘贴到[执行 R 脚本][execute-r-script]模块的 R 脚本窗口中。 利用此方法，你可以将 RStudio 集成开发环境 (IDE) 以及机器学习 Studio (经典) 的强大功能。

#### <a name="execute-r-code"></a>执行 R 代码

选择“运行”按钮运行此试验时，将执行[执行 R 脚本][execute-r-script]模块中的任何 R 代码。**** 执行完成后，会在 " [执行 R 脚本][execute-r-script] " 图标上出现一个复选标记。

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>适用于机器学习 Studio (经典) 的防御性 R 编码

如果要使用机器学习 Studio (经典) 为 web 服务开发 R 代码，则应该明确计划代码如何处理意外的数据输入和异常。 为了保持清晰性，我们未在所示的大多数代码示例中包含太多的检查或异常处理。 继续操作时，我们将通过使用 R 的异常处理功能为你介绍几个函数示例。

如果需要更完整地处理 R 异常处理，请阅读书籍中的相应章节，按 Wickham [进一步阅读](#appendixb)。

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>在机器学习工作室（经典版）中调试和测试 R

在 RStudio 的小规模测试和调试 R 代码。 还需要跟踪 [执行 r 脚本][execute-r-script] 本身中的 R 代码问题。 此外，最好在机器学习 Studio (经典) 中检查结果。

通过执行 R 代码和机器学习 Studio (经典) 平台的输出主要在输出中找到。 其他一些信息出现错误。日志。

如果在运行 R 代码时机器学习 Studio (经典) 出现错误，则第一项操作应该是查看错误 .log。 此文件包含有用的错误消息，可帮助你了解和纠正错误。 若要查看错误 .log，请在包含错误的[执行 R 脚本][execute-r-script]的 "属性" 窗格中选择 "**查看错误日志**"。

例如，我们在 [执行 r 脚本][execute-r-script] 模块中运行了以下 R 代码，其中包含未定义的变量 y。

```r
x <- 1.0
z <- x + y
```

此代码无法执行，这将导致错误。 选择 "属性" 窗格上的 " **查看错误日志** " 将生成以下显示。

  ![显示错误消息弹出窗口的屏幕截图。](./media/r-quickstart/fig2.png)

看来我们需要打开 output.log 文件查看此 R 错误消息。 选择 " [执行 R 脚本][execute-r-script] " 模块，然后在右侧的 "属性" 窗格中选择 " **查看输出日志** " 项。 此时将打开一个新的浏览器窗口，并显示以下错误消息。

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

此错误消息没有意料之外的内容，明确标识了问题所在。

要检查 R 中的任何对象的值，可以将这些值打印到 output.log 文件。 检查对象的值的规则本质上与交互式 R 会话中的相同。 例如，如果在行上输入变量名称，则会将该对象的值输出到输出文件。

#### <a name="packages-in-machine-learning-studio-classic"></a>机器学习工作室（经典版）中的包

机器学习 Studio (经典) 附带了350多个预安装 R 语言包。 可以在[执行 R 脚本][execute-r-script]模块中使用下面的代码来检索预安装的包列表。

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

如果此时不了解此代码的最后一行，请继续阅读。 本文的其余部分将详细介绍如何使用机器学习 Studio (经典) 环境中的 R。

### <a name="introduction-to-rstudio"></a>RStudio 简介

RStudio 是用于 R 的广泛使用的 IDE。我们将使用 RStudio 来编辑、测试和调试本指南中使用的一些 R 代码。 测试并准备好 R 代码后，可以从 RStudio 编辑器剪切并粘贴到机器学习 Studio (经典) [执行 R 脚本][execute-r-script] 模块。

如果你的桌面计算机上没有安装 R 编程语言，请立即执行此操作。 [ (CRAN) 的综合性 r 存档网络](https://www.r-project.org/)提供开放源代码 R 语言的免费下载。 下载适用于 Windows、Mac OS 和 Linux/UNIX。 选择附近的镜像，并按照下载说明进行操作。 此外，CRAN 包含许多有用的分析和数据操作包。

如果不熟悉 RStudio，请下载并安装桌面版。 可以在 [RStudio](http://www.rstudio.com/products/RStudio/)中找到适用于 Windows、Mac OS 和 LINUX/UNIX 的 RStudio 下载。 按照提供的说明在台式计算机上安装 RStudio。

[使用 RStudio IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio) 中提供了 RStudio 的教程介绍。

有关使用 RStudio 的详细信息，请参阅 [RStudio 文档指南](#appendixa)。

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>获取执行 R 脚本模块中的输入和输出数据

在本部分中，我们将讨论如何在 [执行 R 脚本][execute-r-script] 模块中传入和传出数据。 我们还将回顾如何处理从 [执行 R 脚本][execute-r-script] 模块读入和移出的各种数据类型。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整代码。

### <a name="load-and-check-data"></a>加载和检查数据

#### <a name="load-the-dataset"></a><a id="loading"></a>加载数据集

首先，将 **csdairydata.csv** 文件加载到机器学习 Studio (经典) 中。

1. 启动机器学习 Studio (经典) 环境。
1. 选择屏幕左下角的 " **+ 新建** "，然后选择 " **数据集**"。
1. 选择 " **从本地文件**"，然后选择 " **浏览** " 以选择该文件。
1. 请确保已选择 " **带有标题 ( 的通用 CSV 文件) ** 作为数据集的类型。
1. 选择勾选标记。
1. 上传数据集后，当你选择 " **数据集** " 选项卡时，你应该会看到新的数据集。

#### <a name="create-an-experiment"></a>创建试验

现在，我们在机器学习 Studio (经典) 中有了一些数据，我们需要创建一个试验来进行分析。  

1. 选择屏幕左下角的 " **+ 新建**"，并选择 "**试验**  >  **空白试验**"。
1. 通过选择和修改在页面顶部为标题 **创建的实验** 来命名试验。 例如，将其更改为 " **CA 奶制品分析**"。
1. 在 "试验" 页的左侧，选择 "**保存的数据集**  >  **" "我的数据集"**。 你应该会看到之前上传的 **cadairydata.csv** 文件。
1. 将 **csdairydata.csv 数据集** 拖到试验。
1. 在左侧窗格顶部的 " **搜索试验项** " 框中，输入 " [执行 R 脚本][execute-r-script]"。 该模块将显示在搜索列表中。
1. 将 [执行 R 脚本][execute-r-script] 模块拖放到托盘上。
1. 将**csdairydata.csv 数据集**的输出连接到[执行 R 脚本][execute-r-script]的最左侧输入 (**Dataset1**) 。
1. 选择“保存”。

此时，试验应类似于此示例。

![此图显示了 CA 奶制品分析与数据集和执行 R 脚本模块的实验。](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>查看数据

让我们看一下我们在试验中加载的数据。 在试验中，选择 " **cadairydata.csv" 数据集**的输出，并选择 " **可视化**"。 应该会看到类似于此摘要的内容。

![显示 cadairydata.csv 数据集的摘要的屏幕截图。](./media/r-quickstart/fig4.png)

此视图显示了许多有用的信息。 我们可以看到数据集的前几行。 如果选择列，" **统计信息** " 部分会显示有关列的详细信息。 例如，" **功能类型** " 行显示机器学习 Studio (经典) 分配给列的数据类型。 在开始执行任何严重工作之前，请查看此视图。

### <a name="first-r-script"></a>第一个 R 脚本

让我们创建一个简单的第一个 R 脚本，在机器学习 Studio (经典) 中进行试验。 我们在 RStudio 中创建并测试了以下脚本。

```r
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

现在，我们需要将此脚本传输到机器学习 Studio (经典) 。 可以剪切并粘贴，但在这种情况下，可以通过 zip 文件传输 R 脚本。

### <a name="data-input-to-the-execute-r-script-module"></a>输入到执行 R 脚本模块的数据

让我们看看[执行 R 脚本][execute-r-script]模块的输入。 在此示例中，我们将在 " [执行 R 脚本][execute-r-script] " 模块中读取加利福尼亚个奶制品数据。

[执行 R 脚本][execute-r-script]模块可能有三个输入。 你可以使用任何一个或所有输入，具体取决于你的应用程序。 你还可以使用不带任何输入的 R 脚本。

让我们从左到右查看每个输入。 将光标置于输入上并阅读工具提示可看到每个输入的名称。

#### <a name="script-bundle"></a>脚本捆绑

脚本捆绑输入允许将 zip 文件的内容传递到 [执行 R 脚本][execute-r-script] 模块中。 可以使用以下任一命令将 zip 文件的内容读入 R 代码。

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> 机器学习 Studio (经典) 处理 zip 文件中的文件，就像它们位于 src/目录中一样，因此，你需要在文件名前加上此目录名称前缀。 例如，如果 zip 文件包含文件， `yourfile.R` 并且 `yourData.rdata` 位于 zip 文件的根目录中，则可以在 `src/yourfile.R` `src/yourData.rdata` 使用和时使用和来处理这些文件 `source` `load` 。

我们已经在[加载数据集](#loading)中讨论过加载数据集。 创建并测试了上一节中显示的 R 脚本后，请执行以下步骤。

1. 将 R 脚本保存到 R 文件。 我们称之为脚本文件 **simpleplot.r。R**。 下面是文件中的内容：

   ```r
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

1. 创建一个 zip 文件，然后将脚本复制到此 zip 文件中。 在 Windows 中，可以右键单击该文件，然后选择 "**发送到**  >  **压缩的文件夹**"。 此操作创建包含 simpleplot.r 的新的 zip 文件 **。R** 文件。

1. 将文件添加到机器学习 Studio 中的 **数据集** (经典) ，并将类型指定为 **zip**。 现在可在数据集中看到此 zip 文件。

1. 将 zip 文件从 **数据集中** 拖到 **ML Studio (经典) 画布**上。

1. 将 **zip 数据**输出图标连接到[执行 R 脚本][execute-r-script]模块的**脚本包**输入。

1. 在 `source()` " [执行 R 脚本][execute-r-script] " 模块的代码窗口中输入包含 zip 文件名的函数。 在这种情况下，我们输入 `source("src/simpleplot.R")` 。

1. 选择“保存”。

完成这些步骤后，在运行试验时， [执行 r 脚本][execute-r-script] 模块将执行 zip 文件中的 R 脚本。 此时，试验应类似于此示例。

![使用压缩的 R 脚本显示试验的关系图。](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

使用 Dataset1 输入可以将表格数据传递到 R 代码。 在我们的简单脚本中， `maml.mapInputPort(1)` 函数从端口1读取数据。 之后将此数据分配到代码中的数据框变量名。 在我们的简单脚本中，第一行代码执行赋值。

```r
cadairydata <- maml.mapInputPort(1)
```

选择“运行”按钮执行试验。**** 执行完成后，选择 " [执行 R 脚本][execute-r-script] " 模块，然后在 "属性" 窗格中选择 " **查看输出日志** "。 浏览器中打开新的页面，其中显示 output.log 文件的内容。 向下滚动时，应会看到类似于以下输出的内容。

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

页面上向下更详细地介绍有关列的信息，这些列的内容类似于以下输出。

```output
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
```

这些结果大多和预期的一样，数据框中包含 228 个观测值和 9 个列。 可以查看列名称、R 数据类型和每个列的示例。

> [!NOTE]
> 可以从[执行 R 脚本][execute-r-script]模块的 R 设备输出中方便地获取此相同的打印输出。 我们将在下一节讨论 [执行 R 脚本][execute-r-script] 模块的输出。  

#### <a name="dataset2"></a>Dataset2

Dataset2 的输入行为与 Dataset1 相同。 使用此输入，可以将第二个矩形数据表传递到 R 代码中。 使用具有参数 2 的函数 `maml.mapInputPort(2)` 传递此数据。  

### <a name="execute-r-script-outputs"></a>执行 R 脚本的输出

#### <a name="output-a-dataframe"></a>输出数据框

可以使用 `maml.mapOutputPort()` 函数，通过结果 Dataset1 端口将 R 数据框的内容输出为表格。 在简单的 R 脚本中，此操作由以下行执行。

```r
maml.mapOutputPort('cadairydata')
```

运行试验后，选择结果 Dataset1 输出端口，并选择 " **可视化**"。 应会看到类似于此示例的内容。

![屏幕截图，显示加利福尼亚奶制品数据输出的可视化效果。](./media/r-quickstart/fig7.png)

该输出看起来和输入一样，和我们的预期完全相同。

### <a name="r-device-output"></a>R 设备输出

[执行 R 脚本][execute-r-script]模块的设备输出包含消息和图形输出。 R 的标准输出和标准错误消息都会发送到 R 设备输出端口。

若要查看 R 设备输出，请选择该端口，然后选择 " **可视化**"。 从 R 脚本中看到标准输出和标准错误。

![屏幕截图，显示来自 R 设备端口的标准输出和标准错误。](./media/r-quickstart/fig8.png)

向下滚动，将看到来自 R 脚本的图形输出。

![屏幕截图，显示来自 R 设备端口的图形输出。](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>数据筛选和转换

在本部分中，我们将对加利福尼亚奶制品数据执行一些基本的数据筛选和转换操作。 本部分结束时，我们将采用适合生成分析模型的格式的数据。

更具体地说，在本部分中，我们将执行几个常见的数据清理和转换任务：类型转换、对 dataframes 进行筛选、添加新的计算列和值转换。 此背景知识可帮助你应对遇到的各种实际问题。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="type-transformations"></a>类型转换

现在，我们可以将加利福尼亚州乳品数据读入[执行 R 脚本][execute-r-script]模块中的 R 代码，我们需要确保列中的数据具有预期的类型和格式。

R 是一个具有动态类型的语言，这表示可以根据需要将数据类型从一种类型强制转换为另一种类型。 R 中的原子数据类型包括数字、逻辑和字符。 因子类型用于以简洁方式存储分类数据。 有关数据类型的详细信息，请参阅 [进一步阅读](#appendixb)中的参考。

当表格数据从外部源读入 R 时，最好是检查列中的结果类型。 您可能需要一个类型为 "字符" 的列，但在许多情况下，该列将显示为系数，反之亦然。 在其他情况下，您认为的列应该是数字，如 "1.23"，而1.23 不是以浮点数表示。

幸运的是，只要有可能，就可以轻松地将一种类型转换为另一种类型。 例如，不能将 "内华达" 转换为数值，但可以将其转换为 (分类变量) 的因子。 作为另一个示例，您可以将数字1转换为字符 "1" 或系数。

所有这些转换的语法非常简单：`as.datatype()`。 这些类型转换函数包括以下函数：

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

查看在上一节中输入的列的数据类型，所有列的类型均为 numeric。 例外情况是标记为 "Month" 的列，它是字符类型。 让我们将此类型转换为一个因素，并测试结果。

我们删除了创建散点图矩阵的行，并添加了用于将月份列转换为系数的线条。 在此实验中，我们将 R 代码剪切并粘贴到 [执行 r 脚本][execute-r-script] 模块的代码窗口中。 你还可以更新 zip 文件并将其上载到机器学习 Studio (经典) ，但此选项需要几个步骤。

```r
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

让我们来执行此代码，并查看 R 脚本的输出日志。 此处显示了日志中的相关数据。

```output
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
```

月份的类型现在应显示 **系数 w/14 级**。 此类型是一个问题，因为每年只有12个月。 还可以查看结果数据集端口的 **可视化效果** 中的类型是否为 **分类**。

问题在于，月份列尚未系统地编码。 在某些情况下，一个月称为四月，而另一个月份的缩写为 Apr。我们可以通过将字符串裁剪为三个字符来解决此问题。 代码行现在如下例所示。

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

重新运行试验，并查看输出日志。 此处显示了预期的结果。

```output
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
```


现在因子变量具有所需的 12 个级别。

### <a name="basic-dataframe-filtering"></a>基本数据帧筛选

R 数据框支持强大的筛选功能。 对行或列使用逻辑筛选器可以将数据集划分为多个子集。 在许多情况下，都需要复杂的筛选条件。 有关筛选 dataframes 的详细示例，请参阅 [进一步阅读](#appendixb)中的参考。

我们应该对数据集进行一次筛选。 如果查看 cadairydata.csv 数据帧中的列，将看到两个不必要的列。 第一列包含行号，这并不是非常有用。 第二列 Year.Month 包含冗余的信息。 使用以下 R 代码可轻松地排除这两列。

> [!NOTE]
> 从现在开始，我们将向您显示在 [执行 R 脚本][execute-r-script] 模块中添加的附加代码。 在函数 *之前* 添加每个新行 `str()` 。 我们使用此函数来验证机器学习 Studio (经典) 的结果。

将以下行添加到 [执行 r 脚本][execute-r-script] 模块中的 R 代码。

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

在试验中运行此代码，并检查输出日志中的结果。 此处显示了这些结果。

```output
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
```

现在，我们得到了预期的结果。

### <a name="add-a-new-column"></a>添加新列

若要创建时序模型，可以使用包含自时序开始后的月份的列。 我们将创建新的 "Month" 列。

为了帮助组织代码，我们将创建第一个简单的函数 `num.month()` 。 然后，将此函数应用于在数据帧中创建一个新列。 新代码如下所示：

```r
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

现在运行已更新的试验，并使用输出日志查看结果。 此处显示了这些结果。

```output
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
```


看起来一切正常。 数据框中包含了具有预期值的新列。

### <a name="value-transformations"></a>值转换

在本部分中，我们将对数据帧的某些列中的值执行一些简单的转换。 R 语言支持几乎所有值的转换。 有关更多示例，请参阅 [进一步阅读](#appendixb)中的参考。

如果查看数据帧摘要中的值，则会在此处看到奇怪的内容。 加利福尼亚州生产的冰激凌比牛奶还多吗？ 当然不是。 问题在于单位不同。 价格单位为美制镑，牛奶的单位为1000000美元，冰淇淋的单位为1000美国加仑，奶酪奶酪单位为1000美元。 假设冰淇淋的重量约为6.5 磅，则可以轻松地将这些值进行相乘，使其以1000磅为单位。

对于我们的预测模型，我们将为此数据的趋势和季节性调整使用乘法模型。 日志转换允许我们使用线性模型，从而简化了此过程。 可以在应用乘数的相同函数中应用对数转换。

在下面的代码中，我们定义了一个新函数， `log.transform()` 并将其应用于包含数字值的行。 R `Map()` 函数用于将 `log.transform()` 函数应用到数据框的所选列。 `Map()`函数类似于 `apply()` ，但它允许函数有多个参数列表。 请注意，乘数列表向 `log.transform()` 函数提供了另一个参数。 `na.omit()`函数用作一种清理功能，以确保数据帧中没有缺少或未定义的值。

```r
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
  ## If there is an exception, print the warning message to
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

函数中发生了很多情况 `log.transform()` 。 此代码的大部分在检查参数的潜在问题，或者处理在计算中可能还会出现的异常。 此代码只有几行执行计算。

防御性编程的目标是防止单一函数发生故障，阻止处理过程继续进行。 对于用户来说，长时间运行的分析突然失败可能会让人感到沮丧。 若要避免这种情况，必须选择默认返回值，以限制对下游处理的危害。 同时生成一条消息警告用户某些地方出现错误。

如果未使用在 R 中防御编程，则所有这些代码都可能会很困难。 让我们演练以下主要步骤：

1. 定义了四条消息的向量。 这些消息用于传达有关此代码可能发生的一些错误和异常的信息。
1. 对于每种情况，我们返回值 NA。 还有许多其他的值的副作用较少。 例如，我们可以返回0向量或原始输入向量。
1. 针对函数的参数运行检查。 在每种情况下，如果检测到错误，则返回默认值，并由 `warning()` 函数生成一条消息。 我们使用 `warning()` 的是而不是 `stop()` ，因为后者将终止执行，而这正是我们要尝试避免的。 此代码以过程样式编写，因为在这种情况下，函数方法看起来比较复杂且模糊。
1. 日志计算包装在中， `tryCatch()` 因此异常不会导致突然停止处理。 如果没有 `tryCatch()` ，R 函数引发的大多数错误都将导致停止信号，这就是这样。

在试验中执行此 R 代码，并在 output.log 文件中查看打印输出。 现在，您将看到日志中四列的转换后的值，如下所示。

```output
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
```

可以看到值已被转换。 现在，牛奶生产大大超过了所有其他牛奶产品生产，这意味着我们现在正在查看对数刻度。

此时，我们的数据已清理，我们已准备好进行一些建模。 如果查看 " [执行 R 脚本][execute-r-script] " 模块的结果数据集输出的可视化摘要，会看到 "Month" 列是包含12个唯一值的分类，这就是我们所需要的。

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>时序对象和相关性分析

在本部分中，我们将探讨几个基本的 R 时序对象，并分析一些变量之间的相关性。 我们的目标是在几个滞后时间输出包含成对相关信息的数据帧。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="time-series-objects-in-r"></a>R 中的时序对象

如前所述，时序对象是一系列具有时间索引的数据值。 R 时序对象用于创建和管理时间索引。 使用时序对象有几个优点。 时序对象可以省去管理封装在对象中的时序索引值的许多细节。 此外，时序对象允许您将多个时序方法用于绘图、打印和建模等。

通常使用 POSIXct 时序类，且该类相对比较简单。 此时序类的时间测量起始点为 1970 年 1 月 1 日。 在此示例中，我们将使用 POSIXct 时序对象。 其他广泛使用的 R 时序对象类包括 zoo 和 xts (可扩展时序) 。

### <a name="time-series-object-example"></a>时序对象示例

让我们开始使用示例。 将新的 [执行 R 脚本][execute-r-script] 模块拖放到试验中。 将现有的[执行 R 脚本][execute-r-script]模块的结果 Dataset1 输出端口连接到新的[执行 R 脚本][execute-r-script]模块的 Dataset1 输入端口。

正如我们在第一个示例中所做的那样，我们将通过示例。 在某些时候，我们会在每个步骤只显示额外的 R 代码行。

#### <a name="read-the-dataframe"></a>阅读数据帧

第一步，读取数据框，并确保得到预期的结果。 下面的代码可执行此作业。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

现在，运行试验。 新的 "执行 R 脚本" 形状的日志应类似于此示例。

```output
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
```

此数据具有预期的类型和格式。 现在，月份列属于类型因素，并且具有预期的级别数。

#### <a name="create-a-time-series-object"></a>创建时序对象

我们需要向数据框中添加一个时序对象。 将当前代码替换为以下代码，这将添加 POSIXct 类的新列。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

现在，检查日志。 它应与此示例类似。

```output
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
```

从此摘要中可以看到新列实际为 POSIXct 类。

### <a name="explore-and-transform-the-data"></a>浏览和转换数据

让我们探讨一下此数据集的一些变量。 散点图是一种很好的外观。 我们会将 `str()` 前面 R 代码中的函数替换为以下行。

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

运行此代码，看看会发生什么。 在 R 设备端口上生成的绘图应如下例所示。

![显示选定变量的散点图矩阵的屏幕截图。](./media/r-quickstart/fig17.png)

这些变量之间的关系中有一些看起来很奇怪的结构。 这种情况可能是由数据中的趋势引起的，也可能是我们尚未对变量进行标准化。

### <a name="correlation-analysis"></a>相关性分析

若要执行相关分析，需要对变量进行反趋势分析和标准化。 我们只需使用 R `scale()` 函数，该函数可以同时对变量进行中心化和规范化处理。 该函数可能运行较快。 但让我们看一下 R 中的防御性编程的示例。

`ts.detrend()`下面的函数同时执行这两个操作。 以下两行代码对数据进行去趋势处理，并标准化值。

```r
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

函数中发生了很多情况 `ts.detrend()` 。 此代码的大部分在检查参数的潜在问题，或者处理在计算中可能还会出现的异常。 只有几行代码实际执行了计算。

我们已经在值转换中讨论了防御性编程的示例。 这两个计算代码块包装在 `tryCatch()` 中。 对于某些错误，返回原始输入向量是有意义的。 在其他情况下，我们返回一个0向量。

请注意，用于去趋势的线性回归是一个时序回归。 预测器变量是一个时序对象。

`ts.detrend()`定义后，我们将其应用于数据帧中感兴趣的变量。 必须使用将创建的结果列表强制转换 `lapply()` 为数据帧中的数据 `as.data.frame()` 。 由于的防御方面的原因 `ts.detrend()` ，无法处理其中一个变量不会阻止对其他变量进行正确处理。

最后一行代码创建了成对的散点图。 运行 R 代码后，散点图的结果显示在此处。

![显示趋势化和标准化时序的成对散点图的屏幕截图。](./media/r-quickstart/fig18.png)

您可以将这些结果与前面示例中所示的结果进行比较。 去除趋势和标准化变量后，这些变量之间的关系结构就不那么明显了。

以 R ccf 对象形式计算相关性的代码如下：

```r
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

运行此代码会生成此处显示的日志。

```output
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
```

每个 lag 都有相关值。 没有一个相关性值足够大而具有意义。 我们可以得出的结论是，我们可以单独对每个变量建模。

### <a name="output-a-dataframe"></a>输出数据框
我们已将成对相关作为 R ccf 对象的列表进行了计算。 这种方法有一点问题，因为结果数据集输出端口确实需要数据框。 此外，ccf 对象本身是一个列表，我们只需要此列表的第一个元素中的值，这些值在不同的滞后时间。

以下代码从 ccf 对象列表中提取滞后时间值，这些值本身就是列表。

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

第一行代码有点棘手，一些说明可能有助于您了解这一点。 从内到外工作，我们有：

1. **[[** Operator with 参数**1**从 ccf 对象列表的第一个元素中选择滞后的关联向量。
1. `do.call()` 函数对 `lapply()` 返回的列表的元素应用 `rbind()` 函数。
1. `data.frame()` 函数将 `do.call()` 生成的结果强制转换为数据框。

请注意，行名在数据框的一个列中。 这样做会在行名称从 [执行 R 脚本][execute-r-script]输出时保留这些行名。

当我们选择 " **可视化效果** " 以查看结果数据集端口的输出时，运行代码将产生如下所示的输出。 和预期的一样，行名在第一列中。

![屏幕截图，显示相关分析的结果输出。](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>时序示例：季节性预测

现在，我们的数据采用适合分析的形式，我们已确定这些变量之间没有显著的关联。 让我们继续并创建时序预测模型。 使用此模型，我们将在2013年12个月内预测加利福尼亚州牛奶生产。

我们的预测模型包含两个成分：趋势成分和季节性成分。 完满的预测是这两个成分的乘积。 此类型模型被称为乘法模型。 另一种可供选择的模型为加法模型。 我们已将日志转换应用到相关变量，这使得此分析易。

[MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) 中提供了本部分的完整 R 代码。

### <a name="create-the-dataframe-for-analysis"></a>创建用于分析的数据帧

首先将新的 [执行 R 脚本][execute-r-script] 模块添加到试验中。 将现有的执行 R 脚本模块的[结果数据集][execute-r-script]输出连接到新的模块的 **Dataset1** 输入。 结果应与下面的示例类似。

![显示新执行 R 脚本模块添加的试验的关系图。](./media/r-quickstart/fig21.png)

与刚完成的相关分析一样，我们需要添加包含 POSIXct 时间序列对象的列。 下面的代码将添加该列。

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

运行此代码，并查看日志。 结果应与以下示例类似。

```output
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
```

在此结果中，我们已准备好开始分析。

### <a name="create-a-training-dataset"></a>创建训练数据集

构造数据帧后，需要创建定型数据集。 该数据包含所有观测值，2013 年的 12 个月的数据除外，2013 年数据是测试数据集。 以下代码将数据框划分为不同子集，并创建乳品生产和价格变量的图形。 然后创建四个生产和价格变量的图形。 在 `Map()` 函数中使用匿名函数定义图形的一些参数，并遍历其他两个参数的列表。 如果你认为 for 循环在此处可以正常工作，则是正确的。 不过，由于 R 是一种功能语言，我们正在查看函数方法。

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

运行代码时，会从此处显示的 R 设备输出生成一系列时序图。 时间轴以日期单位为单位，这对于时序绘图方法非常有用。

![加利福尼亚奶制品生产和价格数据的第一时序绘图。](./media/r-quickstart/unnamed-chunk-161.png)

![加利福尼亚奶制品生产和价格数据的第二个时序图。](./media/r-quickstart/unnamed-chunk-162.png)

![加利福尼亚奶制品生产和价格数据的第三个时序图。](./media/r-quickstart/unnamed-chunk-163.png).

![加利福尼亚州牛奶生产和价格数据的第四个时序图](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>趋势模型

现在，我们已经创建了时序对象并查看了数据，接下来让我们开始为加利福尼亚州牛奶生产数据构建一个趋势模型。 我们可以使用时序回归。 从绘图中可以清楚地说，我们将需要更多的斜率和截距来准确地对定型数据中观察到的趋势进行建模。

考虑到少量的数据，我们将在 RStudio 中构建趋势模型，然后将生成的模型剪切并粘贴到机器学习 Studio (经典) 中。 RStudio 为此类型的交互式分析提供了交互式环境。

第一次尝试时，我们将尝试使用多达三个的多项式回归。 对于这种类型的模型，有一个真正的危险。 最好是避免高阶术语。 `I()`函数会禁止对内容的解释 (按) 解释内容，并允许您在回归公式中编写字面解释的函数。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

此函数将生成以下输出。

```output
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
```

`Pr(>|t|)`在此输出中，从 P 值 () ，我们可以看到，方术语可能并不重要。 我们将使用 `update()` 函数来通过删除方框术语来修改此模型。

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

此函数将生成以下输出。

```output
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
```

此输出看起来更好。 所有项都重要。 2e-16 值是默认值，不应过于严重。  

在完好性测试中，创建加利福尼亚州乳品生产数据的带趋势曲线的时序图。 我们已在机器学习 Studio 中添加了以下代码 (经典) [执行 R 脚本][execute-r-script] 模型 (不 RStudio) 来创建模型并进行绘图。 下面的示例显示了结果。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![加利福尼亚用显示的趋势模型来模拟生产数据。](./media/r-quickstart/unnamed-chunk-18.png)

从图上看来趋势模型很好地拟合了数据。 此外，似乎没有过度拟合的证据，如模型曲线中的奇数摆动。

### <a name="seasonal-model"></a>季节性模型

有了趋势模型之后，我们需要继续深入并考虑季节效应。 我们将使用一年中的月份作为线性模型中的虚拟变量来捕获每月的影响。 在模型中引入因子变量时，不得计算截距。 如果不执行此操作，则公式会被指定，并且 R 将删除所需的因素之一，但保留 "截距" 一词。

由于我们有一个令人满意的趋势模型，因此我们可以使用 `update()` 函数向现有模型添加新术语。 更新公式中的为 -1 删除截距项。 此时在 RStudio 中继续运行：

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

此函数将生成以下输出。

```output
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
```

我们看到该模型不再具有截距项，并且有 12 个重要的月因子。 这正是我们想要看到的结果。

让我们创建加利福尼亚州乳品生产数据的另一个时序图，以便了解季节性模型的运行情况。 我们已在机器学习 Studio 中添加了以下代码 (经典) [执行 R 脚本][execute-r-script] 来创建模型并创建绘图。

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

在机器学习 Studio 中运行此代码 (经典) 生成此处显示的绘图。

![加利福尼亚牛奶生产模型，其中包括季节性效应。](./media/r-quickstart/unnamed-chunk-20.png)

此示例中显示的数据适合于这种情况。 趋势和季节性效应（月度变体）看起来合理。

作为模型的另一项检查，让我们看看残差。 以下代码从两个模型中计算预测值、计算季节性模型的残差，并绘制训练数据的残差图。

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

残留图形显示在此处。

![定型数据的季节性模型的残差。](./media/r-quickstart/unnamed-chunk-21.png)

这些残差看起来合理。 除了2008-2009 经济衰退的效果外，没有特定的结构，模型不特别适合这样做。

此示例中显示的图可用于检测残差中任何依赖于时间的模式。 计算和绘制使用的残差的显式方法会将时间段的剩余时间置于绘图上。 如果 `milk.lm$residuals` 已绘制，则绘图不会按时间顺序排列。

还可以使用 `plot.lm()` 生成一系列诊断图。

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

此代码将生成一系列诊断图，如以下示例中所示。

![季节性模型的第一个诊断绘图。](./media/r-quickstart/unnamed-chunk-221.png)

![季节性模型的第二个诊断绘图。](./media/r-quickstart/unnamed-chunk-222.png)

![季节性模型的第三个诊断绘图。](./media/r-quickstart/unnamed-chunk-223.png)

![季节性模型的第四个诊断绘图。](./media/r-quickstart/unnamed-chunk-224.png)

这些图中标识了几个极具影响力的点，但是没有任何一个点引起高度关注。 此外，我们还可以从正常的 Q-Q 绘图中看到，残差通常分布在一起，这是线性模型的重要假设。

### <a name="forecasting-and-model-evaluation"></a>预测和模型评估

完成我们的示例只需要执行一项操作。 我们需要计算预测值，并且计算预测值和实际数据的误差。 我们的预测针对的是 2013年 12 个月。 我们可以将此预测的错误度量值计算为不属于定型数据集的实际数据。 此外，可以比较 18 年的训练数据和 12 个月的测试数据的性能。

大量指标用于测量时序模型的性能。 在我们的示例中，我们将使用平方根 (RMS) 错误。 以下函数计算两个序列之间的 RMS 误差。

```r
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

与 `log.transform()` 我们在 "值转换" 一节中讨论的函数一样，此函数中有大量错误检查和异常恢复代码。 两者采用的原理是相同的。 在 `tryCatch()` 函数中包装的两个位置中完成操作。 首先，时间序列是指数化的，因为我们一直在使用这些值的日志。 其次，计算实际的 RMS 误差。

通过配备函数来度量 RMS 错误，让我们生成并输出一个包含 RMS 错误的数据帧。 我们将为趋势模型单独提供术语，并包含季节性因素的完整模型。 下面的代码使用我们构造的两种线性模型来执行该作业。

```r
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

运行此代码会生成在结果数据集输出端口上显示的输出。

![显示模型的 RMS 错误比较的屏幕截图。](./media/r-quickstart/fig26.png)

从这些结果可以看出，向模型中添加季节性因子使 RMS 误差显著减少。 不出所料，训练数据的 RMS 误差略小于预测数据的误差。

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>RStudio 指南文档

RStudio 记录良好。 下面提供了 RStudio 文档中关键部分的链接，以帮助你入门。

* **创建项目**：可以使用 RStudio 将 R 代码组织和管理到项目中。 有关详细信息，请参阅 [使用项目](https://support.rstudio.com/hc/articles/200526207-Using-Projects)。 按照这些说明进行操作，并为本文中的 R 代码示例创建一个项目。
* **编辑和执行 r 代码**： RStudio 提供了一个用于编辑和执行 r 代码的集成环境。 有关详细信息，请参阅 [编辑和执行代码](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)。
* **Debug**： RStudio 包含强大的调试功能。 有关这些功能的详细信息，请参阅 [RStudio 调试](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)。 有关断点故障排除功能的信息，请参阅[断点故障排除](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)。

## <a name="further-reading"></a><a id="appendixb"></a>延伸阅读

此 R 编程教程介绍了将 R 语言与机器学习 Studio (经典) 结合使用所需的基础知识。 如果你不熟悉 R，CRAN 上提供了两个简介：

* 可以从 Emmanuel Paradis 撰写的 [R for Beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf)（面向初学者的 R 语言）入手。
* [An Introduction to R](https://cran.r-project.org/doc/manuals/R-intro.html)（R 简介）（作者：W. N. Venables et al。

有许多有关 R 的书籍可以帮助您入门：

* **R 编程的一种方式： Norman Matloff 的统计软件设计** 简介是对 R 编程的出色介绍。
* Paul Teetor 的**r 手册**提供使用 r 的问题和解决方案方法。
* Robert Kabacoff 撰写的 **R in Action**（R 实战）是另一本有用的简介书籍。 相伴的 [Quick R 网站](https://www.statmethods.net/)是一个很有用的资源。
* **R inferno 》** 按多个节，这是一项令人惊奇的幽默书籍，处理在 R 中进行编程时可能遇到的许多棘手和困难的主题。可在 [R inferno 》](https://www.burns-stat.com/documents/books/the-r-inferno/)免费获取此书籍。
* Hadley Wickham 的**高级 R**提供了深入探讨 R 中的高级主题。本书籍的联机版本在[高级](http://adv-r.had.co.nz/)版中免费提供。
* 使用 R by Paul Cowpertwait 和 Andrew Metcalfe 的**介绍性**时序介绍了如何使用 r 进行时序分析。 还有更多的理论性文字提供了 R 示例。

下面是一些很好的 Internet 资源：

* [CRAN 任务视图：时序分析](https://cran.r-project.org/web/views/TimeSeries.html) 具有 R 时序包的目录。 有关特定时序对象包的信息，请参阅该包的文档。
* [R 简介： r](https://www.datacamp.com/courses/introduction-to-r) 是 DataCamp 提供的免费交互式课程，可以在浏览器中轻松地讲授视频课程和编码练习。 DataCamp 提供了关于最新的 R 技术和包的互动式课程。
* DataMentor 提供的 [Learn R Programming, The Definitive Guide](https://www.datamentor.io/r-programming/)（学习 R 编程 - 权威指南）。
* [R 编码员](https://r-coder.com/) 有详细的 r 教程和初学者的免费 r 课程。
* 从 Clarkson 大学开始，王黑的[R 教程](https://www.cyclismo.org/tutorial/R/)是一个快速教程。
* [用于提高数据技能的热门 r 语言资源](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) 列出了超过60个 R 资源。

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
