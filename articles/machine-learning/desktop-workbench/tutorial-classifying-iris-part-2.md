---
title: 为 Azure 机器学习服务（预览版）生成模型的教程 | Microsoft Docs
description: 本教程的完整内容包括关于如何使用 Azure 机器学习服务（预览版）的端到端演示。 这是第二部分，讨论试验。
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/15/2018
ms.openlocfilehash: 77dcad0f3e49b601110f8700245aaf479bde1c4e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722773"
---
# <a name="tutorial-2-classify-iris---build-a-model"></a>教程 2：鸢尾花分类 - 生成模型
Azure 机器学习服务（预览版）是一个集成式的数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是**由三个部分构成的系列教程的第二部分**。 在教程的此部分，请使用 Azure 机器学习服务执行以下操作：

> [!div class="checklist"]
> * 打开脚本并查看代码
> * 在本地环境中执行脚本
> * 查看运行历史记录
> * 在本地 Azure CLI 窗口中执行脚本
> * 在本地 Docker 环境中执行脚本
> * 在远程 Docker 环境中执行脚本
> * 在云 Azure HDInsight 环境中执行脚本

本教程使用了历久弥新的[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：
- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已创建一个试验帐户，并已根据此[快速入门](../service/quickstart-installation.md)中所述安装了 Azure Machine Learning Workbench
- [教程第 1 部分](tutorial-classifying-iris-part-1.md)提供的项目和准备好的 Iris 数据
- 在本地安装和运行的 Docker 引擎。 Docker 的 Community Edition 已足够。 若要了解如何安装 Docker，请参阅：https://docs.docker.com/engine/installation/。

## <a name="review-irissklearnpy-and-the-configuration-files"></a>查看 iris_sklearn.py 和配置文件

1. 启动 Azure Machine Learning Workbench 应用程序。

1. 打开在[本系列教程的第 1 部分](tutorial-classifying-iris-part-1.md)创建的 myIris 项目。

2. 在打开的项目中，选择最左侧窗格中的“文件”按钮（文件夹图标），打开项目文件夹中的文件列表。

   ![打开 Azure Machine Learning Workbench 项目](media/tutorial-classifying-iris/2-project-open.png)

3. 选择 **iris_sklearn.py** Python 脚本文件。 

   ![选择脚本](media/tutorial-classifying-iris/2-choose-iris_sklearn.png)

   代码随即会在 Workbench 中的新文本编辑器选项卡中打开。 这是你在本教程的此部分使用的脚本。 

   >[!NOTE]
   >看到的代码可能与上面所示代码不完全相同，因为此示例项目经常更新。
   
   ![打开文件](media/tutorial-classifying-iris/open_iris_sklearn.png)

4. 检查 Python 脚本代码，以便熟悉编码样式。 

   脚本 **iris_sklearn.py** 执行以下任务：

   * 加载默认数据准备包 iris.dprep 以创建 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。 

   * 添加随机特征，使问题更难以解决。 随机性是必要的，因为 Iris 是一个小型数据集，能够以接近 100% 的准确率轻松分类。

   * 使用 [scikit-learn](http://scikit-learn.org/stable/index.html) 机器学习库生成逻辑回归模型。  此库是 Azure Machine Learning Workbench 默认随附的。

   * 将 [pickle](https://docs.python.org/3/library/pickle.html) 库插入 `outputs` 文件夹中的某个文件，使模型序列化。 
   
   * 加载序列化模型，然后将其反序列化回内存中。

   * 使用反序列化的模型基于新记录进行预测。 

   * 使用 [matplotlib](https://matplotlib.org/) 库绘制两个图，一个混淆矩阵，一个多类接收者操作特性 (ROC) 曲线，然后将其保存在 `outputs` 文件夹中。 可以在环境中安装此库（如果尚未安装）。

   * 在运行历史记录中自动绘制正则化率和模型准确性。 始终使用 `run_logger` 对象将正则化率和模型准确性记录到日志中。 


## <a name="run-irissklearnpy-in-your-local-environment"></a>在本地环境中运行 iris_sklearn.py

1. 启动 Azure 机器学习命令行界面 (CLI)：
   1. 启动 Azure Machine Learning Workbench。

   1. 在“Workbench”菜单中，选择“文件” > “打开命令提示符”。 
   
   Azure 机器学习命令行界面 (CLI) 窗口在 Windows 上的项目文件夹 `C:\Temp\myIris\>` 中启动。 此项目与本教程的第 1 部分创建的项目相同。

   >[!IMPORTANT]
   >必须使用此 CLI 窗口来完成后续步骤。

1. 在 CLI 窗口中安装 Python 绘制库 **matplotlib**（如果还没有此库）。

   **iris_sklearn.py** 脚本的依赖项依赖于两个 Python 包：**scikit-learn** 和 **matplotlib**。  为方便起见，scikit-learn 包通过 Azure Machine Learning Workbench 安装。 但是，你需要安装 **matplotlib**（如果尚未安装）。

   如果在不安装 **matplotlib** 的情况下继续，则本教程中的代码仍可成功运行。 但是，代码将无法生成历史记录视觉对象中所示的混淆矩阵输出和多类 ROC 曲线绘图。

   ```azurecli
   pip install matplotlib
   ```

   此安装约需一分钟。

1. 返回到 Workbench 应用程序。 

1. 找到名为 **iris_sklearn.py** 的选项卡。 

   ![通过脚本查找选项卡](media/tutorial-classifying-iris/2-iris_sklearn-tab.png)

1. 在该选项卡的工具栏中，选择 **local** 作为执行环境，选择 `iris_sklearn.py` 作为要运行的脚本。 这些可能已被选中了。

   ![本地和脚本选择](media/tutorial-classifying-iris/2-local-script.png)

1. 转到工具栏的右侧，在“参数”字段中输入 `0.01`。 

   此值对应于逻辑回归模型的正则化率。

   ![本地和脚本选择](media/tutorial-classifying-iris/2-local-script-arguments.png)

1. 选择“运行”按钮。 随后会立即计划一个作业。 该作业列在 Workbench 窗口右侧的“作业”窗格中。 

   ![本地和脚本选择](media/tutorial-classifying-iris/2-local-script-arguments-run.png)

   片刻之后，该作业的状态会从“正在提交”转换为“正在运行”，最后转换为“已完成”。

1. 在“作业”窗格的作业状态文本中选择“已完成”。 

   ![运行 sklearn](media/tutorial-classifying-iris/2-completed.png)

   此时会打开一个弹出窗口，其中显示了该运行的标准输出 (stdout) 文本。 若要关闭 stdout 文本，请选择弹出窗口右上角的“关闭”(x) 按钮。

   ![标准输出](media/tutorial-classifying-iris/2-standard-output.png)

9. 在“作业”窗格的同一作业状态中，选择紧靠在“已完成”状态和开始时间上方的蓝色文本“iris_sklearn.py [n]”（_n_ 为运行编号）。 此时会打开“运行属性”窗口，显示该特定运行的以下信息：
   - 运行属性信息
   - **输出**
   - **指标**
   - 可视化效果（如果有）
   - **日志** 

   完成运行后，弹出窗口会显示以下结果：

   >[!NOTE]
   >由于教程此前在训练集中引入了某种随机化，因此确切的结果可能不同于此处显示的结果。

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details pane.
   ```
    
10. 关闭“运行属性”选项卡，然后返回到“iris_sklearn.py”选项卡。 

11. 重复操作即可完成额外的运行。 

    在“参数”字段中输入从 `0.001` 到 `10` 的一系列值。 选择“运行”，再执行几次代码。 每次更改的参数值会馈送到代码中的逻辑回归模型，因而每次都会生成不同的结果。

## <a name="review-the-run-history-in-detail"></a>查看详细的运行历史记录
在 Azure Machine Learning Workbench 中，每次脚本执行被作为一条运行历史记录捕获。 如果打开“运行”视图，则可查看特定脚本的运行历史记录。

1. 若要打开“运行”列表，请选择左侧工具栏上的“运行”按钮（时钟图标）。 然后选择“iris_sklearn.py”，显示 `iris_sklearn.py` 的“运行仪表板”。

   ![“运行”视图](media/tutorial-classifying-iris/run_view.png)

1. 此时会打开“运行仪表板”选项卡。 

   查看在多个运行中捕获的统计信息。 图形呈现在选项卡顶部。每个运行都有一个连续的编号，运行详细信息列在屏幕底部的表中。

   ![“运行”仪表板](media/tutorial-classifying-iris/run_dashboard.png)

1. 筛选该表，然后选择任意图形，查看每个运行的状态、持续时间、准确性和正则化率。 

1. 选择“运行”表中两个或两个以上运行旁边的复选框。 选择“比较”按钮，打开详细的比较窗格。 查看并列比较。 

1. 若要返回到“运行仪表板”，请选择“比较”窗格左上角的“运行列表”后退按钮。

   ![返回到“运行”列表](media/tutorial-classifying-iris/2-compare-back.png)

1. 选择单个运行，查看运行详细信息视图。 请注意，所选运行的统计信息列在“运行属性”部分。 写入输出文件夹中的文件列在“输出”部分，可供下载。

   ![运行详细信息](media/tutorial-classifying-iris/run_details.png)

   “视觉对象”部分呈现了两个绘图：混淆矩阵和多类 ROC 曲线。 还可以在“日志”部分中找到所有日志文件。


## <a name="run-scripts-in-local-docker-environments"></a>在本地 Docker 环境中运行脚本

（可选）可以尝试对本地 Docker 容器运行脚本。 可以配置其他执行环境（例如 Docker）并在这些环境中运行脚本。 

>[!NOTE]
>若要尝试调度脚本，使之在远程 Azure VM 或 Azure HDInsight Spark 群集的 Docker 容器中运行，可以遵循[创建基于 Ubuntu 的 Azure 数据科学虚拟机或 HDInsight 群集的说明](how-to-create-dsvm-hdi.md)。

1. 在本地的 Windows 或 MacOS 计算机上安装并启动 Docker（如果尚未这样做）。 有关详细信息，请参阅 https://docs.docker.com/install/ 中的 Docker 安装说明。 Community Edition 已足够。

1. 在左侧窗格中选择“文件夹”图标，打开项目的“文件”列表。 展开 `aml_config` 文件夹。 

2. 已预配置了多个环境：docker-python、docker-spark、local。 

   每个环境包含两个文件，例如 `docker.compute`（适用于 **docker-python** 和 **docker-spark**）和 `docker-python.runconfig`。 打开每个文件可以发现，某些选项可在文本编辑器中配置。  

   若要进行清理，请在任何已打开文本编辑器的选项卡上选择“关闭”(x)。

3. 使用 docker-python 环境运行 iris_sklearn.py 脚本： 

   - 在左侧工具栏中选择“时钟”图标，打开“运行”窗格。 选择“所有运行”。 

   - 在“所有运行”选项卡的顶部，选择 docker-python 作为目标环境，而不要选择默认的 local。 

   - 接下来转到右侧，选择 iris_sklearn.py 作为要运行的脚本。 

   - 将“参数”字段留空，因为脚本指定了默认值。 

   - 选择“运行”按钮。

4. 观察新作业启动。 该作业显示在 Workbench 窗口右侧的“作业”窗格中。

   首次针对 Docker 运行时，该作业需要多花几分钟才能完成。 

   Azure Machine Learning Workbench 在后台生成新的 Docker 文件。 
   新文件引用在 `docker.compute` 文件中指定的基本 Docker 映像，以及在 `conda_dependencies.yml` 文件中指定的依赖项 Python 包。 
   
   Docker 引擎执行以下任务：

    - 从 Azure 下载基本映像。
    - 安装 `conda_dependencies.yml` 文件中指定的 Python 包。
    - 启动 Docker 容器。
    - 复制或引用（具体取决于运行配置）项目文件夹的本地副本。      
    - 执行 `iris_sklearn.py` 脚本。

   最后，看到的结果应与使用 local 作为目标时的结果完全相同。

5. 现在，让我们试试 Spark。 Docker 基本映像包含预先安装并配置的 Spark 实例，该实例可以用来执行 PySpark 脚本。 使用此基础映像是开发和测试 Spark 程序的一种简便方法，无需花费时间自行安装和配置 Spark。 

   打开 `iris_spark.py` 文件。 此脚本加载 `iris.csv` 数据文件，并使用 Spark 机器学习库中的逻辑回归算法将“鸢尾花”数据集分类。 现在，将运行环境更改为 docker-spark，将脚本更改为 iris_spark.py，然后再次运行。 由于必须在 Docker 容器中创建并启动 Spark 会话，因此此过程所需的时间稍长一些。 还可以看到，stdout 不同于 `iris_spark.py` 的 stdout。

6. 多运行几次，并使用不同的参数进行演练。 

7. 打开 `iris_spark.py` 文件，查看使用 Spark 机器学习库生成的逻辑回归模型。 

8. 在不同执行环境中运行时，与“作业”窗格交互，运行历史记录列表视图，然后运行详细信息视图。

## <a name="run-scripts-in-the-cli-window"></a>在 CLI 窗口中运行脚本

1. 启动 Azure 机器学习命令行界面 (CLI)：
   1. 启动 Azure Machine Learning Workbench。

   1. 在“Workbench”菜单中，选择“文件” > “打开命令提示符”。 
   
   CLI 命令提示符在 Windows 上的项目文件夹 `C:\Temp\myIris\>` 中启动。 这是在本教程第 1 部分创建的项目。

   >[!IMPORTANT]
   >必须使用此 CLI 窗口来完成后续步骤。

1. 在 CLI 窗口中登录到 Azure。 [详细了解 az 登录](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)。

   你可能已登录。 这种情况下，可以跳过此步骤。

   1. 在命令提示符处，输入：
      ```azurecli
      az login
      ```

      此命令返回的代码可以在浏览器的 https://aka.ms/devicelogin 中使用。

   1. 转到浏览器中的 https://aka.ms/devicelogin。

   1. 提示时，请将在 CLI 中收到的代码输入到浏览器中。

   针对 Azure 资源进行身份验证时，Workbench 应用和 CLI 使用独立的凭据缓存。 登录以后，在缓存的令牌过期之前，不需再次进行身份验证。 

1. 如果组织有多个 Azure 订阅（企业环境），则必须设置要使用的订阅。 找到订阅，使用订阅 ID 对其进行设置，然后进行测试。

   1. 使用以下命令列出有权访问的所有 Azure 订阅：
   
      ```azurecli
      az account list -o table
      ```

      **az account list** 命令返回可用于登录的订阅列表。 
      如果有多个订阅，请标识要使用的订阅的订阅 ID 值。

   1. 设置需用作默认帐户的 Azure 订阅：
   
      ```azurecli
      az account set -s <your-subscription-id>
      ```
      其中，\<your-subscription-id\> 为要使用的订阅的 ID 值。 请勿包括大括号。

   1. 通过请求当前订阅的详细信息来确认新的订阅设置。 

      ```azurecli
      az account show
      ```    

1. 在 CLI 窗口中安装 Python 绘制库 **matplotlib**（如果还没有此库）。

   ```azurecli
   pip install matplotlib
   ```

1. 在 CLI 窗口中，将 **iris_sklearn.py** 脚本作为试验提交。

   在本地计算上下文中执行 iris_sklearn.py。

   + 在 Windows 上：
     ```azurecli
     az ml experiment submit -c local .\iris_sklearn.py
     ```

   + 在 MacOS 上：
     ```azurecli
     az ml experiment submit -c local iris_sklearn.py
     ```
   
   输出应类似于：
    ```text
    RunId: myIris_1521077190506
    
    Executing user inputs .....
    ===========================
    
    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]
    
    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
              intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
              penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
              verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679
        
    ==========================================
    Serialize and deserialize using the outputs folder.
    
    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
     [ 1 37 12]
     [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.
    
    Execution Details
    =================
    RunId: myIris_1521077190506
    ```

1. 查看输出。 输出和结果与使用 Workbench 来运行脚本相同。 

1. 在 CLI 窗口中使用 Docker 执行环境再次运行 Python 脚本 **iris_sklearn.py**（如果已在计算机上安装 Docker）。

   + 如果容器在 Windows 上： 
     |执行<br/>环境|Windows 上的命令|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python .\iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark .\iris_spark.py 0.1`|

   + 如果容器在 MacOS 上： 
     |执行<br/>环境|Windows 上的命令|
     |---------------------|------------------|
     |Python|`az ml experiment submit -c docker-python iris_sklearn.py 0.01`|
     |Spark|`az ml experiment submit -c docker-spark iris_spark.py 0.1`|

1. 回到 Workbench，然后执行以下操作：
   1. 选择左侧窗格中的文件夹图标，列出项目文件。
   
   1. 打开名为 **run.py** 的 Python 脚本。 此脚本适用于针对各种正则化率执行循环操作。 

   ![返回到“运行”列表](media/tutorial-classifying-iris/2-runpy.png)

1. 使用这些正则化率多次运行试验。 

   此脚本启动 `iris_sklearn.py` 作业时，使用的正则化率为 `10.0`（此数字过大）。 然后，此脚本在下次运行时将该正则化率减半，依此类推，直至正则化率不小于 `0.005`。 

   脚本包含以下代码：

   ![返回到“运行”列表](media/tutorial-classifying-iris/2-runpy-code.png)

1. 从命令行运行 **run.py** 脚本，如下所示：

   ```cmd
   python run.py
   ```

   此命令使用不同的正则化率提交 iris_sklearn.py 多次。

   当 `run.py` 完成时，可以看到 Workbench 的运行历史记录列表视图中不同指标的图形。

## <a name="run-scripts-in-a-remote-docker-container"></a>在远程 Docker 容器中运行脚本
若要在远程 Linux 计算机上的 Docker 容器中执行脚本，需要通过 SSH 访问（使用用户名和密码）该远程计算机。 此外，该计算机必须已安装并运行 Docker 引擎。 获取此类 Linux 计算机的最简单方法是在 Azure 上创建基于 Ubuntu 的数据科学虚拟机 (DSVM)。 了解[如何创建要在 Azure ML Workbench 中使用的 Ubuntu DSVM](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal)。

>[!NOTE] 
>不支持基于 CentOS 的 DSVM。

1. 创建 VM 后，可将 VM 附加为执行环境，方法是生成 `.runconfig` 和 `.compute` 文件对。 请使用以下命令生成这两个文件。 

 让我们将新计算目标命名为 `myvm`。
 
   ```azurecli
   az ml computetarget attach remotedocker --name myvm --address <your-IP> --username <your-username> --password <your-password>
   ```
   
   >[!NOTE]
   >IP 地址也可以是可公开寻址的完全限定域名 (FQDN)，例如 `vm-name.southcentralus.cloudapp.azure.com`。 最好是将 FQDN 添加到 DSVM，用它来代替 IP 地址。 之所以要这样做，是因为有时可能需关闭 VM 以节省成本。 此外，下次启动 VM 时，IP 地址可能会更改。

   >[!NOTE]
   >除了用户名和密码身份验证，还可以使用 `--private-key-file` 和 `--private-key-passphrase` 选项（后者为可选）指定私钥和相应的通行短语（如果有）。

   接下来，通过运行以下命令来准备 **myvm** 计算目标。
   
   ```azurecli
   az ml experiment prepare -c myvm
   ```
   
   前一命令在 VM 中构造 Docker 映像，使之做好运行脚本的准备。
   
   >[!NOTE]
   >还可将 `myvm.runconfig` 中 `PrepareEnvironment` 的值从默认值 `false` 更改为 `true`。 这样更改以后，就会在首次运行时自动准备 Docker 容器。

2. 编辑在 `aml_config` 下生成的 `myvm.runconfig` 文件，并将框架从默认值 `PySpark` 更改为 `Python`：

   ```yaml
   Framework: Python
   ```
   >[!NOTE]
   >如果实际上并不需要 Spark 会话就可以运行 Python 脚本，则使用 Python 会更有效，虽然也可以使用 PySpark。

3. 与前面一样在 CLI 窗口中发出相同的命令，这次使用目标 myvm 在远程 Docker 容器中执行 iris_sklearn.py：
   ```azurecli
   az ml experiment submit -c myvm iris_sklearn.py
   ```
   该命令的执行方式与在 `docker-python` 环境中相同，不过，执行是在远程 Linux VM 上发生的。 CLI 窗口显示相同的输出信息。

4. 让我们在容器中试用 Spark。 打开文件资源管理器。 创建 `myvm.runconfig` 文件的副本并将其命名为 `myvm-spark.runconfig`。 编辑新文件，将 `Framework` 设置从 `Python` 更改为 `PySpark`：
   ```yaml
   Framework: PySpark
   ```
   不要对 `myvm.compute` 文件进行任何更改。 同一 VM 上的同一 Docker 映像用于 Spark 执行。 在新的 `myvm-spark.runconfig` 中，`Target` 字段根据名称 `myvm` 指向同一 `myvm.compute` 文件。

5. 键入以下命令，在 Spark 实例中运行 **iris_spark.py** 脚本，而该实例在远程 Docker 容器中运行：
   ```azureli
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="run-scripts-in-hdinsight-clusters"></a>在 HDInsight 群集中运行脚本
还可以在 HDInsight Spark 群集中运行此脚本。 了解[如何创建在 Azure ML Workbench 中使用的 HDInsight Spark 群集](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)。

>[!NOTE] 
>HDInsight 群集必须使用 Azure Blob 作为主存储。 尚不支持使用 Azure Data Lake 存储。

1. 如果有权访问 Azure HDInsight 群集的 Spark，请按如下所示生成 HDInsight 运行配置命令。 提供 HDInsight 群集名称、HDInsight 用户名和密码作为参数。 

   使用以下命令创建一个计算目标，使之指向 HDInsight 群集：

   ```azurecli
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <your-username> --password <your-password>
   ```

   若要准备 HDInsight 群集，请运行以下命令：

   ```
   az ml experiment prepare -c myhdi
   ```

   群集头节点 FQDN 通常为 `<your_cluster_name>-ssh.azurehdinsight.net`。

   >[!NOTE]
   >`username` 是群集 SSH 用户名，在 HDInsight 设置过程中定义。 默认情况下，该值为 `sshuser`。 该值不是 `admin`，这是在设置期间为了能够访问群集管理员网站而创建的另一个用户。 

2. 通过以下命令在 HDInsight 群集中运行 **iris_spark.py** 脚本：

   ```azurecli
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >针对远程 HDInsight 群集执行时，还可以使用 `admin` 用户帐户在 `https://<your_cluster_name>.azurehdinsight.net/yarnui` 上查看 Yet Another Resource Negotiator (YARN) 作业执行详细信息。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤
由三个部分构成的系列教程的此第二部分介绍了如何执行以下操作：
> [!div class="checklist"]
> * 在 Workbench 中打开脚本并查看代码
> * 在本地环境中执行脚本
> * 查看运行历史记录
> * 在本地 Docker 环境中执行脚本

现在可以尝试本教程系列的第三部分，了解如何部署作为实时 Web 服务创建的逻辑回归模型。

> [!div class="nextstepaction"]
> [教程 3 - 部署模型](tutorial-classifying-iris-part-3.md)
