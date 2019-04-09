---
title: 教程 - 在 Azure 上创建并运行 Jupyter Notebook
description: 如何在 Azure Notebooks 中创建并运行一个用于演示数据科学线性回归过程的 Jupyter Notebook。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 65bbb5fe-9939-4e8e-8f5b-c197d4be142a
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: d5ccf3e9f35a8d35387962278577333ff92ff02b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258518"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>教程：使用 Python 创建并运行 Jupyter Notebook

本教程引导你完成使用 Azure Notebooks 创建一个用于演示简单线性回归的完整 Jupyter Notebook 的过程。 在学习本教程的过程中，你将熟悉 Jupyter Notebook UI，包括创建不同的单元、运行单元，以及以幻灯片的形式演示 Notebook。

可以在 [GitHub - Azure Notebooks 示例](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)中找到已完成的 Notebook。 但是，本教程将从一个新的项目和一个空的 Notebook 开始，让你体验如何逐步创建 Notebook。

## <a name="create-the-project"></a>创建项目

1. 转到 [Azure Notebooks](https://notebooks.azure.com) 并登录。 （有关详细信息，请参阅[快速入门 - 登录 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)）。

1. 在公用个人资料页面中，选择页面顶部的“我的项目”：

    ![浏览器窗口顶部的“我的项目”链接](media/quickstarts/my-projects-link.png)

1. 在“我的项目”页面上，选择“+ 新建项目”（键盘快捷方式：N）；如果浏览器窗口较窄，该按钮可能仅显示为 +：

    ![“我的项目”页面上的“新建项目”命令](media/quickstarts/new-project-command.png)

1. 在出现的“创建新项目”弹出窗口中，输入或设置以下详细信息，然后选择“创建”：

    - **项目名称**：Linear Regression Example - Cricket Chirps
    - **项目 ID**：linear-regression-example
    - **公共项目**：（已清除）
    - **创建 README.md**：（已清除）

1. 几分钟后，Azure Notebooks 会导航到新项目。

## <a name="create-the-data-file"></a>创建数据文件

在 Notebook 中创建的线性回归模型将从项目中名为 *cricket_chirps.csv* 的文件抽取数据。 您可以创建此文件可将其从复制[GitHub-Azure 笔记本示例](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps)，或通过直接输入数据。 以下部分将介绍这两种方法。

### <a name="upload-the-data-file"></a>上传数据文件

1. 在 Azure Notebooks 中的项目仪表板上，选择“上传” > “从 URL”
1. 在弹出窗口上的“文件 URL”中输入以下 URL，在“文件名”中输入 *cricket_chirps.csv*，然后选择“完成”。

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. 现在，*cricket_chirps.csv* 文件应会显示在项目的文件列表中：

    ![项目文件列表中显示的新建 CSV 文件](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>从头开始创建文件

1. 在 Azure Notebooks 中的项目仪表板上，选择“+ 新建” > “空白文件”
1. 项目的文件列表中会显示一个字段。 输入 *cricket_chirps.csv* 并按 Enter。
1. 右键单击“cricket_chirps.csv”并选择“编辑文件”。
1. 在显示的编辑器中输入以下数据：

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. 选择“保存文件”以保存文件并返回到项目仪表板。

## <a name="install-project-level-packages"></a>安装项目级包

在 Notebook 中，始终可以在代码单元中使用类似于 `!pip install` 的命令来安装所需的包。 但是，每次运行 Notebook 的代码单元都会运行此类命令，这可能需要花费相当长的时间。 出于此原因，可以改为使用 `requirements.txt` 文件在项目级别安装包。

1. 使用[从头开始创建文件](#create-a-file-from-scratch)中所述的过程，创建包含以下内容的名为 `requirements.txt` 的文件：

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    如果需要，还可以根据[上传数据文件](#upload-the-data-file)中所述从本地计算机上传 `requirements.txt` 文件。

1. 在项目仪表板上，选择“项目设置”。
1. 在显示的弹出窗口中，依次选择“环境”选项卡、“+添加”。
1. 在“环境设置步骤”下的第一个下拉控件（操作）中，选择“Requirements.txt”。
1. 在第二个下拉控件（文件名）中，选择“requirements.txt”（创建的文件）。
1. 在第三个下拉控件（Python 版本）中，选择“Python 版本 3.6”。
1. 选择“保存”。

![指定 requirements.txt 文件的“项目设置 - 环境”选项卡](media/tutorial/tutorial-requirements-txt.png)

完成此设置步骤后，在项目中运行的任何 Notebook 将在安装这些包的环境中运行。

## <a name="create-and-run-a-notebook"></a>创建和运行笔记本

准备好数据文件并设置项目环境后，可以创建并打开 Notebook。

1. 在项目仪表板上，选择“+ 新建” > “Notebook”。
1. 在弹出窗口中，为“项名称”输入 *Linear Regression Example - Cricket Chirps.ipynb*，选择“Python 3.6”作为语言，然后选择“新建”。
1. 新 Notebook 显示在文件列表中后，请将其选中以启动 Notebook。 此时会自动打开一个新的浏览器标签页。
1. 由于已在环境设置中添加了 *requirements.txt* 文件，因此会看到消息“正在等待容器准备完成。” 可以选择“确定”关闭该消息并继续在 Notebook 中操作；但是，在完全设置好环境之前，无法运行代码单元。
1. 该 Notebook 将在 Jupyter 界面中打开，其中默认包含单个空代码单元。

    [![I新的 notebook Azure 笔记本中的初始视图](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>在 Notebook 界面中导航

当 Notebook 正在运行时，你可以添加代码和 Markdown 单元、运行这些单元，以及管理 Notebook 的操作。 但是，首先值得花费几分钟的时间来熟悉界面。 如需完整文档，请选择“帮助” > “Notebook 帮助”菜单命令。

在窗口顶部可以看到以下项：

(A) Notebook 的名称，单击它可进行编辑。
(B) 用于导航到包含项目和项目仪表板的按钮，单击这些按钮会在浏览器中打开新的标签页。
(C) 一个菜单，其中包含用于操作 Notebook 的命令。
(D) 一个工具栏，其中包含常用操作的快捷方式。
(E) 包含单元的编辑画布。
(F) 指示该 Notebook 是否受信任（默认为“不受信任”）。
(G) 用于运行 Notebook 的内核以及活动指示器。

[![PJupyter 界面的主 UI 区域](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter 提供主要 UI 元素的内置导航功能。 选择“帮助” > “用户界面导航”命令并在各个弹出窗口中单击可以开始导航。

菜单命令的分组如下：

| 菜单 | 描述 |
| --- | --- |
| 文件 | 用于管理 Notebook 文件的命令，包括用于创建和复制 Notebook、显示打印预览，以及以各种格式下载 Notebook 的命令。 |
| 编辑 | 用于剪切、复制和粘贴单元、查找和替换值、管理单元附件，以及插入图像的典型命令。  |
| 查看 | 用于控制 Jupyter UI 不同部分的可见性的命令。 |
| 插入 | 用于在当前单元的上方或下方插入新单元的命令。 创建 Notebook 时经常要使用这些命令。 |
| 单元 | 各种“运行”命令用于运行一个单元，或运行采用不同组合形式的多个单元。 “单元类型”命令用于在“代码”、“Markdown”和“原始 NBConvert”（纯文本）之间更改单元的类型。 “当前输出”和“所有输出”命令控制运行代码的输出显示方式，包括一个用于清除所有输出的命令。 |
| 内核 | 用于管理代码在内核中的运行方式的命令，此外还有一个“更改内核”命令，它可以更改用于运行 Notebook 的语言或 Python 版本。 |
| 数据 | 用于从项目或会话上传和下载文件的命令。 请参阅[使用项目数据文件](work-with-project-data-files.md) |
| 小组件 | 用于管理 [Jupyter 小组件](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html)的命令。这些小组件提供有关可视化、映射和绘制的附加功能。|
| 帮助 | 用于提供 Jupyter 界面帮助和文档的命令。 |

工具栏上的大多数命令都有等效的菜单命令， 但[共享和演示 Notebook](present-jupyter-notebooks-slideshow.md) 中所述的“进入/编辑 RISE 幻灯片”例外。

在后续部分填充 Notebook 时，将使用其中的某些命令。

## <a name="create-a-markdown-cell"></a>创建 Markdown 单元

1. 单击进入 Notebook 画布上显示的第一个空单元。 默认情况下，单元是一种**代码**类型，即，它旨在包含所选内核（Python、R 或 F#）的可运行代码。 当前类型显示在工具栏上的类型下拉列表中：

    ![单元类型工具栏下拉列表](media/tutorial/tutorial-cell-type-drop-down.png)

1. 使用工具栏下拉列表将单元类型更改为“Markdown”；或者，使用“单元” > “单元类型” > “Markdown”菜单命令：

    ![单元类型菜单命令](media/tutorial/tutorial-cell-type-menu.png)

1. 单击进入单元以开始编辑，然后输入以下 Markdown：

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. 若要在浏览器的 HTML 中呈现 Markdown，请在工具栏上选择“运行”命令，或使用“单元” > “运行单元”命令。 现在，浏览器中会按预期方式显示有关格式和链接的 Markdown 代码。

1. 运行 Notebook 中的最后一个单元时，Jupyter 会自动在运行的单元下面创建一个新单元。 重复本部分所述的步骤，将更多的 Markdown 插入此单元，其中包括以下 Markdown：

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. 若要再次编辑 Markdown，请双击呈现的单元。 进行更改后若要再次呈现 HTML，请运行该单元。

## <a name="create-a-code-cell-with-commands"></a>创建包含命令的代码单元

与前面所述的 Markdown 单元一样，可以直接在 Notebook 中包含命令。 可以使用命令来安装包、运行 curl 或 wget 以检索数据，或执行其他任何操作。 Jupyter Notebook 能够有效地在 Linux 虚拟机中运行，因此，整套 Linux 命令都可供使用。

1. 在针对前一 Markdown 单元使用“运行”后显示的代码单元中输入以下命令。 如果未看到新单元，请使用“插入” > “在下面插入”或使用工具栏上的 **+** 按钮创建一个单元。

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. 运行单元之前，使用工具栏上的 **+** 按钮创建一个新单元，将其设置为“Markdown”，然后输入以下说明：

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. 选择“单元” > “全部运行”命令运行 Notebook 中的所有单元。 请注意以 HTML 呈现的 Markdown 单元以及内核中运行的命令，并观察 Markdown 本身中描述的内核指示器：

    ![Notebook 内核繁忙指示器](media/tutorial/tutorial-kernel-busy.png)

1. 运行所有 `pip install` 命令也要花费一小段时间；由于已在项目环境中安装这些包（并且这些包默认也包含在 Azure Notebooks 中），因此，会看到许多消息指出“已满足要求”。 所有这些输出可能会分散注意力，因此请选择该单元（单击），然后使用“单元” > “单元输出” > “切换”来隐藏输出。 还可以使用同一子菜单中的“清除”命令将输出完全删除。

    “切换”命令只会隐藏单元中最近的输出；如果再次运行该单元，该输出会重新出现。

1. 由于包已安装在项目环境中，因此请使用 `#` 注释掉 `! pip install` 命令；这样，它们可以作为指导材料保留在 Notebook 中，但不会花费任何时间来运行，也不会生成不必要的输出。 在这种情况下，在 Notebook 中保留带有注释的命令还会指示 Notebook 的依赖关系。

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>创建剩余的单元

若要填充 Notebook 的剩余部分，请接着创建一系列 Markdown 和代码单元。 对于下面列出的每个单元，请先创建新单元，再设置类型，然后粘贴内容。

尽管可以等到在创建每个单元之后再运行 Notebook，但一边创建一边运行每个单元比较有乐趣。 并非所有单元都显示输出；如果未看到任何错误，则认为单元运行正常。

每个代码单元依赖于前面单元中运行的代码，如果在运行某个单元时出现疏忽，后续单元可能会生成错误。 如果你发现自己忘记了运行某个单元，请先尝试使用“单元” > “运行前面的所有单元”，然后运行当前单元。

如果看到意外的结果（有可能出现这种情况！），请检查每个单元是否按需要设置为“代码”或“Markdown”。 例如，在代码单元中输入 Markdown 时，通常会发生“语法无效”错误。

1. Markdown 单元：

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. 代码单元；运行时，显示表内容作为输出。 可以通过注释掉 `print` 语句来隐藏输出。

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > 可能会看到此代码出现有关“numpy.dtype 大小已更改”的警告；可以放心忽略这些警告。

1. Markdown 单元：

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. 代码单元；运行时，此单元不提供输出。

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Markdown 单元：

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept`).
    ```

1. 代码单元；运行时，此单元显示输出 `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`。

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Markdown 单元：

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_` and `coef_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. 代码单元；运行时，此单元显示类似于 `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]` 的结果。

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Markdown 单元：

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Markdown 单元：

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. 代码单元；运行时，此单元生成绘图。 如果最初未看到绘图（而是看到“图大小 640x480，具有 1 个轴”），请再次运行该单元。

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![为 matplotlib 代码的输出绘图](media/tutorial/tutorial-plot-output.png)

1. Markdown 单元：

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>清除输出并重新运行所有单元

遵循上一部分的步骤填充整个 Notebook 后，即已在有关线性回归的整个教程上下文中创建了一段正常运行的代码。 代码和文本的这种直接组合是 Notebook 的重要优势之一！

现在，请尝试重新运行整个 Notebook：

1. 选择“内核” > “重启并清除输出”，清除内核的所有会话数据和所有单元输出。 如果你已完成某个 Notebook，现在只需确保不会在代码单元之间创建任何不合理的依赖关系，则始终很适合运行此命令。

1. 使用“单元” > “全部运行”重新运行 Notebook。 请注意，当代码正在运行时，会填充内核指示器。

    如果有任何代码的运行时间过长或者停滞，可以使用“内核” > “中断”命令来停止内核。

1. 滚动浏览 Notebook 以检查结果。 （如果绘图仍未出现，请重新运行该单元。）

## <a name="save-halt-and-close-the-notebook"></a>保存、停止和关闭 Notebook

在编辑 Notebook 的过程中，可以使用“文件” > “保存并设置检查点”命令或使用工具栏上的“保存”按钮来保存 Notebook 的当前状态。 “检查点”会创建一个快照。在会话期间，随时可以还原到该快照。 使用检查点可以做出一系列试验性更改，如果这些更改不起作用，使用“文件” > “还原到检查点”命令即可还原到某个检查点。 另一种方法是创建额外的单元，并注释掉你不想要运行的任何代码；这两种方法都是可行的。

还可以随时使用“文件” > “创建副本”命令，在项目的新文件中创建 Notebook 当前状态的副本。 该副本将自动在新浏览器标签页中打开。

处理完 Notebook 后，可以使用“文件” > “关闭并停止”命令关闭 Notebook，并关闭运行 Notebook 的内核。 然后，Azure Notebooks 会自动关闭浏览器标签页。

## <a name="debug-notebooks-using-visual-studio-code"></a>使用 Visual Studio Code 调试 Notebook

如果 Notebook 中代码单元的行为不符合预期，则可能表示代码中存在 bug 或其他缺陷。 但是，典型的 Jupyter 环境不提供任何调试工具，只能使用 `print` 语句来显示变量的值。

幸运的是，可以下载笔记本的 .ipynb 文件，然后使用 Python 扩展在 Visual Studio Code 中打开它。 该扩展直接将笔记本作为单个代码文件导入，在注释中保留 Markdown 单元。 导入笔记本后，可以使用 Visual Studio Code 调试程序来逐步运行代码、设置断点、检查状态，等等。 在对代码进行更正后，从 Visual Studio Code 导出 .ipynb 文件并将其上传回 Azure Notebooks。

有关详细信息，请参阅 Visual Studio Code 文档中的[调试 Jupyter 笔记本](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook)。

另请参阅 [Visual Studio Code - Jupyter 支持](https://code.visualstudio.com/docs/python/jupyter-support)，了解适用于 Jupyter 笔记本的 Visual Studio Code 的其他功能。

## <a name="next-steps"></a>后续步骤

- [浏览示例笔记本](azure-notebooks-samples.md)

操作指南文章：

- [创建和克隆项目](create-clone-jupyter-notebooks.md)
- [配置和管理项目](configure-manage-azure-notebooks-projects.md)
- [从笔记本内安装包](install-packages-jupyter-notebook.md)
- [演示幻灯片放映](present-jupyter-notebooks-slideshow.md)
- [处理数据文件](work-with-project-data-files.md)
- [访问数据资源](access-data-resources-jupyter-notebooks.md)
- [使用 Azure 机器学习服务](use-machine-learning-services-jupyter-notebooks.md)
