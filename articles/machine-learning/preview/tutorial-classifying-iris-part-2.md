---
title: "为 Azure 机器学习服务（预览版）生成模型 | Microsoft Docs"
description: "本完整教程端到端演示如何使用 Azure 机器学习服务（预览版）。 这是第二部分，讨论试验。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 11/06/2017
ms.openlocfilehash: 7e489526fd1b6a7d38172d147df1f30d74119e95
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="classify-iris-part-2-build-a-model"></a>鸢尾花分类（第 2 部分）：生成模型
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是由三个部分构成的系列教程的第二部分。 在教程的此部分，请使用 Azure 机器学习服务（预览版）执行以下操作：

> [!div class="checklist"]
> * 使用 Azure Machine Learning Workbench。
> * 打开脚本并查看代码。
> * 在本地环境中执行脚本。
> * 查看运行历史记录。
> * 在本地 Docker 环境中执行脚本。
> * 在本地 Azure CLI 窗口中执行脚本。
> * 在远程 Docker 环境中执行脚本。
> * 在云 Azure HDInsight 环境中执行脚本。

本教程使用了永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 屏幕截图与 Windows 相关，但 Mac OS 上的体验几乎相同。

## <a name="prerequisites"></a>系统必备
完成本系列教程的第一部分。 开始本教程的步骤之前，请遵循[准备数据教程](tutorial-classifying-iris-part-1.md)创建 Azure 机器学习资源，并安装 Azure Machine Learning Workbench 应用程序。

（可选）可以尝试对本地 Docker 容器运行脚本。 为此，需在本地的 Windows 或 Mac OS 计算机上安装并启动 Docker 引擎（Community Edition 已足够）。 有关 Docker 安装的详细信息，请参阅 [Docker 安装说明](https://docs.docker.com/engine/installation/)。

若要尝试调度脚本，使之在远程 Azure VM 或 Azure HDInsight Spark 群集的 Docker 容器中运行，可以遵循[创建基于 Ubuntu 的 Azure 数据科学虚拟机或 HDInsight 群集的说明](how-to-create-dsvm-hdi.md)。

## <a name="review-irissklearnpy-and-the-configuration-files"></a>查看 iris_sklearn.py 和配置文件
1. 打开 Azure Machine Learning Workbench 应用程序，然后打开在本系列教程前一部分创建的 myIris 项目。

2. 打开项目后，选择最左侧窗格中的“文件”按钮（文件夹图标），打开项目文件夹中的文件列表。

3. 选择 iris_sklearn.py 文件。 Python 代码随即会在 Workbench 中的新文本编辑器选项卡中打开。

   ![打开文件](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >看到的代码可能与上面所示代码不完全相同，因为此示例项目经常更新。

4. 查看 Python 脚本代码，以便熟悉编码样式。 此脚本执行以下任务：

   - 加载数据准备包 iris.dprep 以创建 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。 

        >[!NOTE]
        >请使用示例项目随附的 `iris.dprep` 数据准备包，该包应与本教程第 1 部分生成的 `iris-1.dprep` 文件相同。

   - 添加随机特征，使问题更难以解决。 随机性是必要的，因为“鸢尾花”是一个小型数据集，能够以接近 100% 的准确率轻松分类。

   - 使用 [scikit-learn](http://scikit-learn.org/stable/index.html) 机器学习库生成逻辑回归模型。 

   - 将 [pickle](https://docs.python.org/2/library/pickle.html) 库插入 `outputs` 文件夹中的某个文件，使模型序列化。 然后，脚本会将其加载并反序列化回内存中。

   - 使用反序列化的模型基于新记录进行预测。 

   - 使用 [matplotlib](https://matplotlib.org/) 库绘制两个图，一个混淆矩阵，一个多类接收者操作特性 (ROC) 曲线，然后将其保存在 `outputs` 文件夹中。

   - 始终使用 `run_logger` 对象来记录正则化率并将准确性建模到日志中。 日志自动在运行历史记录中绘制。


## <a name="execute-irissklearnpy-script-in-a-local-environment"></a>在本地环境中执行 iris_sklearn.py 脚本

让我们准备首次运行 **iris_sklearn.py** 脚本。 此脚本需要 scikit-learn 和 matplotlib 包。 scikit-learn 包已通过 Azure Machine Learning Workbench 安装。 仍需安装 matplotlib。 

1. 在 Azure Machine Learning Workbench 中选择“文件”菜单，然后选择“打开命令提示符”打开命令提示符。 此命令行接口窗口称为 Azure Machine Learning Workbench CLI 窗口，简称 CLI 窗口。

2. 在 CLI 窗口中输入以下命令，安装 matplotlib Python 包。 安装应在一分钟内完成。

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >如果跳过上一个 `pip install` 命令，则 `iris_sklearn.py` 中的代码会成功运行。 如果只运行 `iris_sklearn.py`，则该代码不会生成历史记录视觉对象中所示的混淆矩阵输出和多类 ROC 曲线绘图。

3. 返回到 Workbench 应用窗口。 

4. 在 iris_sklearn.py 选项卡顶部的工具栏中，选择打开“保存”图标旁边的下拉菜单，然后选择“运行配置”。 选择“local”作为执行环境，然后输入 `iris_sklearn.py` 作为要运行的脚本。

5. 接下来转到工具栏的右侧，在“参数”字段中输入 `0.01`。 

   ![“运行”控件](media/tutorial-classifying-iris/run_control.png)

6. 选择“运行”按钮。 随后会立即计划一个作业。 该作业列在 Workbench 窗口右侧的“作业”窗格中。 

7. 片刻之后，该作业的状态会从“正在提交”转换为“正在运行”，然后转换为“已完成”。

   ![运行 sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. 在“作业”窗格的作业状态文本中选择“已完成”。 此时会打开一个弹出窗口，其中显示了正在运行的脚本的标准输出 (stdout) 文本。 若要关闭 stdout 文本，请选择弹出窗口右上角的“关闭”(x) 按钮。

9. 在“作业”窗格的同一作业状态中，选择紧靠在“已完成”状态和开始时间上方的蓝色文本“iris_sklearn.py [n]”（n 为运行编号）。 此时会打开“运行属性”窗口，显示该特定运行的以下信息：
   - 运行属性信息
   - 输出文件
   - 可视化效果（如果有）
   - **日志** 

   完成运行后，弹出窗口会显示以下结果：

   >[!NOTE]
   >由于前面在训练集中引入了某种随机化，因此确切的结果可能不同于此处显示的结果。

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

11. 重复额外的运行。 

    在“参数”字段中输入从 `0.001` 到 `10` 的一系列不同数值。 选择“运行”，再执行几次代码。 每次更改的参数值会馈送到代码中的逻辑回归算法，因而每次都会生成不同的结果。

## <a name="review-the-run-history-in-detail"></a>查看详细的运行历史记录
在 Azure Machine Learning Workbench 中，每次脚本执行被作为一条运行历史记录捕获。 如果打开“运行”视图，则可查看特定脚本的运行历史记录。

1. 若要打开“运行”列表，请选择左侧工具栏上的“运行”按钮（时钟图标）。 然后选择“iris_sklearn.py”，显示 `iris_sklearn.py` 的“运行仪表板”。

   ![“运行”视图](media/tutorial-classifying-iris/run_view.png)

2. 此时会打开“运行仪表板”选项卡。 查看在多个运行中捕获的统计信息。 图形呈现在选项卡顶部。每个运行都有一个连续的编号，运行详细信息列在屏幕底部的表中。

   ![“运行”仪表板](media/tutorial-classifying-iris/run_dashboard.png)

3. 筛选该表，然后选择任意图形，查看每个运行的状态、持续时间、准确性和正则化率。 

4. 在“运行”表中选择两个或三个运行，然后选择“比较”按钮，打开详细的比较窗格。 查看并列比较。 选择“比较”窗格左上角的“运行列表”后退按钮，返回到“运行仪表板”。

5. 选择单个运行，查看运行详细信息视图。 请注意，所选运行的统计信息列在“运行属性”部分。 写入输出文件夹中的文件列在“输出”部分，可供下载。

   ![运行详细信息](media/tutorial-classifying-iris/run_details.png)

   “视觉对象”部分呈现了两个绘图：混淆矩阵和多类 ROC 曲线。 还可以在“日志”部分中找到所有日志文件。

## <a name="execute-scripts-in-the-local-docker-environment"></a>在本地 Docker 环境中执行脚本

使用机器学习可以轻松配置其他执行环境（例如 Docker）并在这些环境中运行脚本。 

>[!IMPORTANT]
>若要完成此步骤，必须在本地安装并启动 Docker 引擎。 有关详细信息，请参阅 Docker 安装说明。

1. 在左侧窗格中选择“文件夹”图标，打开项目的“文件”列表。 展开 `aml_config` 文件夹。 

2. 已预配置了多个环境，例如 docker-python、docker-spark、local。 

   每个环境包含两个文件，例如 `docker-python.compute` 和 `docker-python.runconfig`。 打开每个文件可以发现，某些选项可在文本编辑器中配置。  

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

5. 现在，让我们试试 Spark。 Docker 基本映像包含预先安装并配置的 Spark 实例。 由于存在此实例，可在其中执行 PySpark 脚本。 这是开发和测试 Spark 程序的一种简便方法，无需花费时间自行安装和配置 Spark。 

   打开 `iris_spark.py` 文件。 此脚本加载 `iris.csv` 数据文件，并使用 Spark 机器学习库中的逻辑回归算法将“鸢尾花”数据集分类。 现在，将运行环境更改为 docker-spark，将脚本更改为 iris_spark.py，然后再次运行。 由于必须在 Docker 容器中创建并启动 Spark 会话，因此此过程所需的时间稍长一些。 还可以看到，stdout 不同于 `iris_spark.py` 的 stdout。

6. 多运行几次，并使用不同的参数进行演练。 

7. 打开 `iris_spark.py` 文件，查看使用 Spark 机器学习库生成的逻辑回归模型。 

8. 在不同执行环境中运行时，与“作业”窗格交互，运行历史记录列表视图，然后运行详细信息视图。

## <a name="execute-scripts-in-the-azure-machine-learning-cli-window"></a>在 Azure 机器学习 CLI 窗口中执行脚本

1. 在 Azure Machine Learning Workbench 中打开命令行窗口，选择“文件”菜单，然后选择“打开命令提示符”。 命令提示符会在项目文件夹中启动，提示符为 `C:\Temp\myIris\>`。

   >[!IMPORTANT]
   >必须使用命令行窗口（从 Workbench 打开）完成以下步骤。

2. 使用命令提示符登录到 Azure。 

   针对 Azure 资源进行身份验证时，Workbench 应用和 CLI 使用独立的凭据缓存。 在缓存的令牌过期之前，只需执行此操作一次。 **az account list** 命令返回可用于登录的订阅列表。 如果存在多个订阅，请使用所需订阅中的 ID 值。 通过 az account set -s 命令将该订阅设置为要使用的默认帐户，然后提供订阅 ID 值。 然后使用 account show 命令确认设置。

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. 完成身份验证并设置当前的 Azure 订阅上下文后，请在 CLI 窗口中输入以下命令来安装 matplotlib，然后提交 Python 脚本作为要运行的试验。

   ```azurecli
   REM you don't need to run this command if you have installed matplotlib locally from the previous steps
   pip install matplotlib
   
   REM kicks off an execution of the iris_sklearn.py file against the local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. 查看输出。 输出和结果与使用 Workbench 来运行脚本相同。 

5. 如果在计算机上安装了 Docker，可以使用 Docker 执行环境再次运行同一脚本。

   ```azurecli
   REM executes iris_sklearn.py in the local Docker container Python environment
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM executes iris_spark.py in the local Docker container Spark environment
   az ml experiment submit -c docker-spark .\iris_spark.py 0.1
   ```
6. 在 Workbench 中选择左侧窗格中的“文件夹”图标，列出项目文件，然后打开名为 run.py 的 Python 脚本。 

   此脚本适用于针对各种正则化率执行循环操作。 使用这些正则化率多次运行试验。 此脚本启动 `iris_sklearn.py` 作业时，使用的正则化率为 `10.0`（此数字过大）。 然后，此脚本在下次运行时将该正则化率减半，依此类推，直至正则化率不小于 `0.005`。 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   若要从命令行打开 run.py 脚本，请运行以下命令：

   ```cmd
   REM submits iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   `run.py` 完成时，Workbench 的运行历史记录列表视图中会出现一个图形。

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>在远程计算机上执行 Docker 容器
若要在远程 Linux 计算机上的 Docker 容器中执行脚本，需要通过 SSH 访问（使用用户名和密码）该远程计算机。 此外，该远程计算机必须已安装并运行 Docker 引擎。 获取此类 Linux 计算机的最简单方法是在 Azure 上创建基于 Ubuntu 的数据科学虚拟机 (DSVM)。 了解[如何创建要在 Azure ML Workbench 中使用的 Ubuntu DSVM](how-to-create-dsvm-hdi.md#create-an-ubuntu-dsvm-in-azure-portal)。

>[!NOTE] 
>不支持基于 CentOS 的 DSVM。

1. 创建 VM 后，可将 VM 附加为执行环境，前提是生成 `.runconfig` 和 `.compute` 文件对。 请使用以下命令生成这两个文件。 让我们将新环境命名为 `myvm`。
 
   ```azurecli
   REM creates an myvm compute target
   az ml computetarget attach remotedocker --name myvm --address <IP address> --username <username> --password <password>
   ```
   
   >[!NOTE]
   >IP 地址也可以是可公开寻址的完全限定的域名 (FQDN)，例如 `vm-name.southcentralus.cloudapp.azure.com`。 最好是将 FQDN 添加到 DSVM，然后将其用在此处，而不要使用 IP 地址。 之所以要这样做，是因为有时可能需关闭 VM 以节省成本。 此外，下次启动 VM 时，IP 地址可能会更改。

   接下来运行以下命令，在 VM 中构造 Docker 映像，使之做好运行脚本的准备：
   
   ```azurecli
   REM prepares the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >还可将 `myvm.runconfig` 中 `PrepareEnvironment` 的值从默认值 `false` 更改为 `true`。 这样更改以后，就会在首次运行时自动准备 Docker 容器。

2. 编辑在 `aml_config` 下生成的 `myvm.runconfig` 文件，并将框架从默认的 `PySpark` 更改为 `Python`：

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >如果将框架设置保留为 PySpark，则也应可行。 但是，如果实际上并不需要在 Spark 会话中运行 Python 脚本，则使用该设置会降低效率。

3. 与前面一样在 CLI 窗口中发出相同的命令，但这次以 myvm 为目标：
   ```azurecli
   REM executes iris_sklearn.py in a remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   该命令的执行方式与在 `docker-python` 环境中相同，不过，执行是在远程 Linux VM 上发生的。 CLI 窗口显示相同的输出信息。

4. 让我们在容器中试用 Spark。 打开文件资源管理器。 如果熟悉基本的文件操作命令，则也可通过 CLI 窗口执行此操作。 创建 `myvm.runconfig` 文件的副本并将其命名为 `myvm-spark.runconfig`。 编辑新文件，将 `Framework` 设置从 `Python` 更改为 `PySpark`：
   ```yaml
   "Framework": "PySpark"
   ```
   不要对 `myvm.compute` 文件进行任何更改。 同一 VM 上的同一 Docker 映像用于 Spark 执行。 在新的 `myvm-spark.runconfig` 中，`target` 字段根据名称 `myvm` 指向同一 `myvm.compute` 文件。

5. 键入以下命令，在远程 Docker 容器的 Spark 实例中运行此文件：
   ```azureli
   REM executes iris_spark.py in a Spark instance on a remote Docker container
   az ml experiment submit -c myvm-spark .\iris_spark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>在 HDInsight 群集中执行脚本
还可以在 HDInsight Spark 群集中运行此脚本。 了解[如何创建要在 Azure ML Workbench 中使用的 HDInsight Spark 群集](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal)。

>[!NOTE] 
>HDInsight 群集必须使用 Azure Blob 作为主存储。 尚不支持使用 Azure Data Lake 存储。

1. 如果有权访问 Azure HDInsight 群集的 Spark，请按如下所示生成 HDInsight 运行配置命令。 提供 HDInsight 群集名称、HDInsight 用户名和密码作为参数。 请使用以下命令：

   ```azurecli
   REM creates a compute target that points to a HDInsight cluster
   az ml computetarget attach cluster --name myhdi --address <cluster head node FQDN> --username <username> --password <password>

   REM prepares the HDInsight cluster
   az ml experiment prepare -c myhdi
   ```

   群集头节点 FQDN 通常为 `<cluster_name>-ssh.azurehdinsight.net`。

   >[!NOTE]
   >`username` 是群集的 SSH 用户名。 默认值为 `sshuser`（如果在 HDInsight 设置期间未更改）。 该值不是 `admin`，这是在设置期间为了能够访问群集管理员网站而创建的另一个用户。 

2. 运行以下命令，脚本将在 HDInsight 群集中运行：

   ```azurecli
   REM executes iris_spark on the HDInsight cluster
   az ml experiment submit -c myhdi .\iris_spark.py
   ```

   >[!NOTE]
   >针对远程 HDInsight 群集执行时，还可以使用 `admin` 用户帐户在 `https://<cluster_name>.azurehdinsight.net/yarnui` 上查看 Yet Another Resource Negotiator (YARN) 作业执行详细信息。


## <a name="next-steps"></a>后续步骤
由三个部分构成的系列教程的此第二部分已介绍如何使用 Azure 机器学习服务来执行以下操作：
> [!div class="checklist"]
> * 使用 Azure Machine Learning Workbench。
> * 打开脚本并查看代码。
> * 在本地环境中执行脚本。
> * 查看运行历史记录。
> * 在本地 Docker 环境中执行脚本。
> * 在本地 Azure CLI 窗口中执行脚本。
> * 在远程 Docker 环境中执行脚本。
> * 在云 HDInsight 环境中执行脚本。

现在，可以继续学习系列教程的第三部分。 创建逻辑回归模型后，即可将其部署为实时 Web 服务。

> [!div class="nextstepaction"]
> [部署模型](tutorial-classifying-iris-part-3.md)
