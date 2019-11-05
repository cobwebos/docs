---
title: 使用 Visual Studio Code 进行机器学习
titleSuffix: Azure Machine Learning
description: 了解如何为 Visual Studio Code 安装 Azure 机器学习并在 Azure 机器学习中创建试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: df23c41629dfe86058f0f2fb6602e85fd5a6d494
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489297"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code 入门

在本文中，你将学习如何使用 Visual Studio Code 扩展的**Azure 机器学习**来训练和部署机器学习模型。

[Azure 机器学习](overview-what-is-azure-ml.md)简化了机器学习模型的构建、培训和部署。
+ 对于培训，它为在本地或远程运行试验提供支持。 对于每个试验，可以记录多个运行的自定义指标来微调超参数
+ 你还可以使用 Azure 机器学习轻松部署机器学习模型，以满足测试和生产需求。

## <a name="prerequisites"></a>必备组件

+ 如果还没有 Azure 订阅，请在开始前创建免费帐户。 试用[Azure 机器学习免费或付费版本](https://aka.ms/AMLFree)。

+ 安装[Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)，这是在 Windows、Mac 和 Linux 上运行的轻型代码编辑器。

+ [安装 Python 3.5 或更高版本](https://www.anaconda.com/download/)。


## <a name="install-the-extension"></a>安装扩展

安装 Azure 机器学习扩展时，会自动安装两个扩展。 它们是[Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)和[Microsoft Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python)。 有关使用 Python 扩展编辑、运行和调试 Python 代码的详细信息，请参阅[python hello world 教程](https://code.visualstudio.com/docs/Python/Python-tutorial)。

安装 Azure 机器学习扩展：

1. 打开 Visual Studio Code。

1. 切换到 "扩展" 选项卡，搜索 "Azure 机器学习"。

1. 在 "扩展" 选项卡上，选择 "**安装**"。

1. 此时会在 Visual Studio Code 中打开扩展的 "欢迎" 选项卡，并将 Azure 符号（在以下屏幕截图中突出显示为红色）添加到活动栏。

   ![Visual Studio Code 活动栏上的 Azure 图标](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在对话框中，选择 "**登录**"，然后按照提示进行 Azure 身份验证。

   与 Visual Studio Code 扩展的 Azure 机器学习一起安装的 Azure 帐户扩展可帮助你通过 Azure 帐户进行身份验证。 有关命令列表，请参阅[Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)的页面。

> [!TIP]
> 你还可以直接从 Azure 机器学习下载扩展安装程序[以 Visual Studio Code 扩展（预览版）](https://aka.ms/vscodetoolsforai)。

## <a name="quickstart-with-azure-machine-learning"></a>Azure 机器学习快速入门
可以通过多种方式使用 Azure 机器学习运行训练脚本。 如果刚开始，我们首先演练如何快速提交要在 Azure 中运行的训练脚本。

如果你已经熟悉 Azure 机器学习概念，并且想要更详细地了解如何管理和使用扩展的详细信息，请参阅以下[VS Code Azure 机器学习深入](./how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code)了解。

## <a name="run-an-existing-python-training-script-in-azure"></a>在 Azure 中运行现有的 Python 定型脚本
如果你有现成的训练脚本，则 VS Code 的 Azure 机器学习扩展不仅可提供出色的编辑、调试和源管理体验，还可让你轻松地在 Azure 中运行和存储脚本的指标。

让我们开始吧。 如果你已准备好，则可以使用自己的训练脚本，或克隆[vscode](https://github.com/microsoft/vscode-tools-for-ai)存储库的示例。 这是用于归档有关此扩展的问题的公共存储库。 它还包含一个小型**mnist**示例文件夹，我们将在此示例中使用此文件夹。

1. 在 VS Code 中打开 " **mnist** " 文件夹。

1. 使用最喜欢的虚拟环境包或 Anaconda 创建新的 Python 环境，并安装 TensorFlow 和 numpy 包。

1. 在 VS Code 状态栏的左下角，选择你创建的新环境作为 Python 解释器。

1. 打开**train.py** ，并按 "运行" 按钮（或只需按 F5）来运行该程序。

   [![运行 MNIST 培训](./media/vscode-tools-for-ai/run-mnist.gif)](./media/vscode-tools-for-ai/run-mnist.gif#lightbox)

如果所有内容都已正确安装，则脚本将运行，并在输出文件夹中创建 TensorFlow 模型。

[![显示 TensorFlow 模型](./media/vscode-tools-for-ai/show-tensorflow-model.gif)](./media/vscode-tools-for-ai/show-tensorflow-model.gif#lightbox)

既然您已经知道您的脚本运行正常，接下来让我们在 Azure 中运行！

这可以轻松地完成，无需额外修改**train.py**。 但是，只需进行几次简单的更改，就可以使用 Azure 机器学习来自动跟踪你选择的有关每个定型运行的重要指标。

### <a name="make-azure-aware-of-your-run-metrics"></a>使 Azure 了解你的运行指标
修改项目，以便可以了解运行中的重要信息：

1. 在与**train.py**相同的文件夹中创建名为**amlrun.py**的文件

    ```Python
    import azureml
    from azureml.core import Run

    # access the Azure ML run
    # init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. 导入**train.py**中的 amlrun 文件

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. 初始化**train.py**中的 run 对象

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. 用 run .log （）函数将指标记录到 Azure：

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>在 Azure 中运行脚本
就这么简单！ 现在只需使用扩展即可在云中运行你的脚本！ 请注意，下面的演练视频会让你自由地压缩创建新的 Azure ML 工作区和计算所需的时间，以及运行训练脚本所需的时间。

   [![开始 Azure ML 试验](./media/vscode-tools-for-ai/start-golden-path.gif)](./media/vscode-tools-for-ai/start-golden-path.gif#lightbox)

单击 "运行试验" 按钮后，按如下所示回答提示：

1. 选择自己的 Azure 订阅
1. 选择创建*新*的 Azure ML 工作区
1. 从一组预配置的模板中进行选择，以便初始化运行的 Python 环境。 这些模板提供了一个起始点，并包括以下各内容的设置：
    1. **PyTorch**、 **TensorFlow**或**scikit-learn-了解**
    1. **单个**或**分布式**计算培训
    1. 自定义环境**一般**
1. 通过添加模板中未包含的任何包，确保为脚本完成 pip 和 conda 包的列表。
1. 查看试验运行的默认名称和规格，并单击 json 文件中的 "**提交试验**" 链接。 不会保存 json 文件，只需在提交之前查看或更改试验设置。
1. 坐下来放松，让扩展为你设置一切并运行你的脚本！

    [![在云中定型](./media/vscode-tools-for-ai/run-golden-path.gif)](./media/vscode-tools-for-ai/run-golden-path.gif#lightbox)

几秒钟后，将会通知你实验已提交到 Azure，此时你 Azure 机器学习可以通过单击 VS Code 通知中的 "**查看试验运行**" 链接，或在 VS Code按 "Azure" 选项卡中的 "刷新" 按钮。

目前，在工作室中仅支持查看运行度量值。 上面提到的 "**查看试验运行**" 链接会将你转到运行，你会在其中看到你记录的度量值。
[在门户中运行 ![试验](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG)](./media/vscode-tools-for-ai/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Azure 机器学习深入了解 VS Code

在上面的演练中，我们按照最简单的路径提交了一次试验。 正如您可能已经注意到的，扩展最大限度地减少了运行试验所需的步骤。 在本部分中，我们将介绍如何单独管理所有 Azure 机器学习概念，同时提供最大的控制权。

在 Visual Studio Code 中开始训练和部署机器学习模型之前，需要在云中创建一个[Azure 机器学习工作区](concept-workspace.md)。 此工作区将包含模型和资源。

### <a name="create-a-workspace"></a>创建工作区

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

    [![创建工作区](./media/vscode-tools-for-ai/create-workspace.gif)](./media/vscode-tools-for-ai/create-workspace.gif#lightbox)


1. 右键单击你的 Azure 订阅，然后选择“创建工作区”。 默认情况下，会生成包含创建日期和时间的名称。 将名称更改为**TeamWorkspace** ，然后按 enter。

1. 如果你知道要选择或创建一个新的资源组，请从列表中选择该资源组。 如果要创建一个新的位置，请选择最接近你计划用于部署模型的位置的位置。 在此示例中，我们选择了 "**美国西部 2**"。

1. 按 enter 后，Azure 机器学习会收到创建工作区的请求。 你将在 Visual Studio Code 通知区域收到有关该过程的通知。

1. 展开 "订阅" 节点以查找新创建的工作区。

### <a name="create-an-experiment"></a>创建试验
可以在工作区中创建一个或多个试验，以跟踪和分析各个模型定型运行。 可在 Azure 云中或在本地计算机上执行运行。

1. 展开 " **TeamWorkspace** " 工作区。 右键单击 "**试验**" 节点，然后从上下文菜单中选择 "**创建试验**"。

1. 在提示符下，为试验输入名称。 在示例屏幕截图中，试验命名为**MNIST**。

1. 选择 Enter 以创建新试验。 新实验作为**试验**节点的子节点出现在树中。

1. 在工作区中，可以右键单击某个试验，将其设置为**活动**试验。 **活动**试验会将云中试验的链接到当前已在 Visual Studio Code 中打开的文件夹中。 此文件夹应包含本地 Python 脚本。 设置活动试验后，所有训练运行的关键指标都将存储在试验中，而不考虑它们的执行位置。

    [![创建试验](./media/vscode-tools-for-ai/create-experiment.gif)](./media/vscode-tools-for-ai/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>创建和管理计算目标

利用 Azure 机器学习 Visual Studio Code，你可以准备你的数据、训练模型，并在本地和远程计算目标上部署它们。

扩展插件支持 Azure 机器学习的多个远程计算目标。 有关详细信息，请参阅 Azure 机器学习支持的[计算目标](how-to-set-up-training-targets.md)的完整列表。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>为中的 Azure 机器学习创建计算目标 Visual Studio Code

创建计算目标：

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅并 Azure 机器学习 "工作区。

1. 在工作区节点下，右键单击“计算”节点，再选择“创建计算”。

1. 从列表中选择计算目标类型。

1. 在命令面板提示符下，选择 "虚拟机大小"。 可以筛选用文本计算的计算，如 "gpu"。

1. 在命令面板提示符下，输入计算目标的名称。

1. 输入名称后，将使用默认参数创建计算。 若要更改参数，请右键单击新计算，然后选择 "**编辑计算**"。

1. 在显示的 json 中，进行所需的更改，然后单击 "保存并继续" CodeLens （使用键盘，可以按**ctrl + shift-p**来调用命令面板并运行**Azure ML： "保存并继续**" 命令）

下面是有关如何创建和编辑 Azure 机器学习计算（AMLCompute）的示例：

[![在 Visual Studio Code 中创建 AML 计算](./media/vscode-tools-for-ai/create-remote-compute.gif)](./media/vscode-tools-for-ai/create-remote-compute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>运行配置文件

若要在计算上运行 Azure 机器学习试验，需要适当地配置计算。 运行配置文件是此环境的指定机制。

下面的示例演示如何为上面创建的 AmlCompute 创建运行配置。

[![为计算创建运行配置](./media/vscode-tools-for-ai/create-runconfig.gif)](./media/vscode-tools-for-ai/create-runconfig.gif#lightbox)

若要在本地计算机上运行 Azure ML 试验，还需要运行配置文件。 创建本地运行配置时，所使用的 Python 环境将默认为 VS Code 中设置的解释器的路径。

### <a name="train-and-tune-models"></a>定型和优化模型

使用适用于 VS Code 的 Azure ML 扩展，可以通过多种方式在试验中运行训练脚本。

1. 右键单击训练脚本，并选择 " **AZURE ML：在 azure 中运行为试验"**
1. 单击 "运行试验" 工具栏图标。
1. 右键单击 "运行配置" 节点。
1. 使用 VS Code 命令面板执行**AZURE ML：运行试验**

运行 Azure 机器学习试验：

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。

1. 在树视图中，展开 Azure 订阅并 Azure 机器学习 "工作区。

1. 在 "工作区" 节点下，展开 "**试验**" 节点，然后右键单击要运行的试验。

1. 选择“运行试验”。

1. 选择要运行的 Python 文件的名称，以对模型进行定型并按 enter 提交运行。 注意：所选文件必须位于当前在 VS Code 中打开的文件夹中。

1. 提交运行后，**运行节点**将显示在所选试验的下方。 使用此节点监视运行状态。 注意：可能需要定期刷新窗口才能查看最新状态。

下面的示例演示如何对先前创建的计算运行试验：

[![在本地运行试验](./media/vscode-tools-for-ai/run-experiment.gif)](./media/vscode-tools-for-ai/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>部署和管理模型
在 Azure 机器学习中，可以在云中和边缘部署和管理机器学习模型。

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>将模型注册到 Azure 机器学习 Visual Studio Code

现在您已训练了模型，接下来可以在工作区中注册它。 您可以跟踪和部署已注册的模型。

注册模型：

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅并 Azure 机器学习 "工作区。

1. 在工作区节点下，右键单击“模型”，再选择“注册模型”。

1. 在 "命令" 面板的 "字段" 中，输入模型名称。

1. 从列表中，选择是上载**模型文件**（适用于单个模型）还是**模型文件夹**（对于包含多个文件的模型，如 TensorFlow）。

1. 选择你的文件夹或文件。

1. 完成模型属性配置后，在窗口右下角，选择 "**提交**"。

下面的示例演示如何将模型注册到 Azure 机器学习：

[![将模型注册到 AML](./media/vscode-tools-for-ai/register-model.gif)](./media/vscode-tools-for-ai/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>从 Visual Studio Code 部署服务

在 Visual Studio Code 中，你可以将 web 服务部署到：
+ 用于测试的 Azure 容器实例（ACI）。
+ 用于生产的 Azure Kubernetes 服务（AKS）。

不需要事先创建 ACI 容器来进行测试，因为可以根据需要创建 ACI 容器。 但是，您需要提前配置 AKS 群集。 有关详细信息，请参阅[部署模型与 Azure 机器学习](how-to-deploy-and-where.md)。

部署 web 服务：

1. 在 "Visual Studio Code" 活动栏上，选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 "Azure 订阅" 和 "Azure 机器学习" 工作区。

1. 在工作区节点下，展开“模型”节点。

1. 右键单击要部署的模型，然后从上下文菜单中选择 "**从已注册的模型部署服务**"。

1. 在 "命令" 面板中，选择要部署到的计算目标。

1. 在 "命令" 面板上的 "字段" 中，输入此服务的名称。

1. 在 "命令" 面板中，选择键盘上的 Enter 键以浏览并选择该脚本文件。

1. 在命令面板中，选择键盘上的 Enter 键以浏览并选择 conda 依赖项文件。

1. 完成服务属性配置后，请在窗口右下角选择 "**提交**" 以进行部署。 在服务属性文件中，可以指定本地 docker 文件或架构文件。

此时，Web 服务已部署。

下面是有关如何部署 web 服务的示例：

[![部署 web 服务](./media/vscode-tools-for-ai/create-image.gif)](./media/vscode-tools-for-ai/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>试验附加功能

您可以使用命令面板访问 Visual Studio Code 中的许多 Azure 机器学习功能。 若要调用命令面板，请按 Ctrl + Shift + P。 可在此处搜索扩展的其他 Azure ML 功能。

[![用于 Azure 机器学习的键盘快捷方式 Visual Studio Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>后续步骤

* 有关如何在 Visual Studio Code 外训练 Azure 机器学习的演练，请参阅[教程：使用 Azure 机器学习训练模型](tutorial-train-models-with-aml.md)。
* 有关如何在本地编辑、运行和调试代码的演练，请参阅[Python hello world 教程](https://code.visualstudio.com/docs/Python/Python-tutorial)。
