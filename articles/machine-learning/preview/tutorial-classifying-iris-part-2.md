---
title: "为 Azure 机器学习服务（预览版）生成模型 | Microsoft Docs"
description: "本完整教程端到端演示如何使用 Azure 机器学习服务（预览版）。 这是有关试验的第 2 个部分。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f1f270b95a60e847ce7d29c2d7d60c57891924cc
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-2-build-a-model"></a>“鸢尾花”分类第 2 部分：生成模型
Azure 机器学习服务（预览版）是一个集成式的端到端数据科学和高级分析解决方案，可让专业数据科学家以云的规模准备数据、开发试验和部署模型。

本教程是由三个部分构成的系列教程的第二部分。 在本教程部分，我们使用 Azure 机器学习服务（预览版）来了解以下操作：

> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中操作
> * 打开脚本并查看代码
> * 在本地环境中执行脚本
> * 查看运行历史记录
> * 在本地 Docker 环境中执行脚本
> * 在本地 Azure CLI 窗口中执行脚本
> * 在远程 Docker 环境中执行脚本
> * 在云 HDInsight 环境中执行脚本

为方便起见，本教程使用了永久[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 屏幕截图与 Windows 相关，但 macOS 上的体验几乎相同。

## <a name="prerequisites"></a>先决条件
应该先完成本系列教程的第一部分。 开始本教程的步骤之前，请遵循[准备数据教程](tutorial-classifying-iris-part-1.md)创建 Azure 机器学习资源，并安装 Azure Machine Learning Workbench 应用程序。

## <a name="review-irissklearnpy-and-configuration-files"></a>查看 iris_sklearn.py 和配置文件
1. 启动 **Azure Machine Learning Workbench** 应用程序，打开在本系列教程前一部分中创建的 **myIris** 项目。

2. 打开该项目后，单击 Azure Machine Learning Workbench 左侧工具栏上的“文件”按钮（文件夹图标），以便在项目文件夹中打开文件列表。

3. 选择 **iris_sklearn.py** 文件，Python 代码随即会在 Workbench 中的新文本编辑器选项卡上打开。

   ![打开文件](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >看到的代码可能与上面所示代码不完全相同，因为此示例项目经常更新。

4. 查看 Python 脚本代码，以便熟悉编码样式。 请注意，脚本执行以下任务：

   - 加载数据准备包 **iris.dprep** 以创建 [pandas 数据帧](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)。 

        >[!NOTE]
        >我们将使用示例项目随附的 `iris.dprep` 数据准备包，该包应与本教程第 1 部分中生成的 `iris-1.dprep` 文件相同。

   - 添加随机特征，使问题更难以解决。 （随机性是必要的，因为“鸢尾花”是一个小型数据集，能够以接近 100% 的准确率轻松分类。）

   - 使用 [scikit-learn](http://scikit-learn.org/stable/index.html) 机器学习库生成简单的逻辑回归模型。 

   - 使用 [pickle](https://docs.python.org/2/library/pickle.html) 库将该模型序列化到 `outputs` 文件夹中的某个文件，然后加载该文件并将其反序列化回到内存中

   - 使用反序列化的模型基于新记录进行预测。 

   - 使用 [matplotlib](https://matplotlib.org/) 库绘制两个图形 - 混淆矩阵和多类 ROC 曲线，并将其保存在 `outputs` 文件夹中。

   - 在整个过程中，使用了 `run_logger` 对象将正则化率和模型准确性记录到日志中，日志自动在运行历史记录中绘制。


## <a name="execute-irissklearnpy-script-in-local-environment"></a>在本地环境中执行 iris_sklearn.py 脚本

让我们准备首次运行 **iris_sklearn.py** 脚本。 此脚本需要 scikit-learn 和 matplotlib 包。 **scikit-learn** 已由 Azure ML Workbench 安装。 但是，我们需要安装 **matplotlib**。 

1. 在 Azure Machine Learning Workbench 中，单击“文件”菜单并选择“打开命令提示符”启动命令提示符。 我们将此命令行接口窗口称为 Azure Machine Learning Workbench CLI 窗口，简称 CLI 窗口。

2. 在 CLI 窗口中，键入以下命令安装 **matplotlib** Python 包。 安装应在一分钟内完成。

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >如果跳过上述 `pip install` 命令，`iris_sklearn.py` 中的代码确实能够成功运行，但不会生成历史记录视觉对象中所示的混淆矩阵输出和多类 ROC 曲线绘图。

3. 返回到 Workbench 应用窗口。 

4. 在 **iris_sklearn.py** 选项卡左上角的保存图标旁边，单击下拉列表选择“运行配置”选项。  选择“local”作为执行环境，选择 `iris_sklearn.py` 作为要运行的脚本。

5. 接下来，转到同一选项卡的右侧，在“参数”字段中填写值 `0.01`。 

   ![img](media/tutorial-classifying-iris/run_control.png)

6. 单击“运行”按钮。 随后会立即计划一个作业。 该作业列在 Workbench 窗口右侧的“作业”面板中。 

7. 片刻之后，该作业的状态会从“正在提交”转换为“正在运行”，然后很快会转换为“已完成”。

   ![运行 sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. 在“作业”面板上的作业状态文本中单击文字“已完成”。 此时会打开一个弹出窗口，其中显示了正在运行的脚本的标准输出 (stdout) 文本。 若要关闭 stdout 文本，请单击弹出窗口右上角的“X”按钮。

9. 在“作业”面板上的同一作业状态中，单击紧靠在“已完成”状态和开始时间上方的蓝色文本“iris_sklearn.py [1]”。 此时会打开“运行属性”页，其中显示了“运行属性”信息、“输出”文件、任何“视觉对象”，以及该特定运行的“日志”。 

   完成运行后，弹出窗口会显示以下结果：

   >[!NOTE]
   >由于前面在训练集中引入了某种随机化，因此确切的结果有所不同。

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
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. 关闭“运行属性”选项卡，并返回到“iris_sklearn.py”选项卡。 

11. 重复额外的运行。 

    在“参数”字段中输入从 `0.001` 到 `10` 的一系列不同数值。 单击“运行”再执行几次代码。 每次更改的参数值会馈送到代码中的逻辑回归算法，因而每次都会生成不同的结果。

## <a name="review-run-history-in-detail"></a>详细查看运行历史记录
在 Azure Machine Learning Workbench 中，每次脚本执行被作为一条运行历史记录捕获。 可以打开“运行”视图来查看特定脚本的运行历史记录。

1. 单击左侧工具栏上的“运行”按钮（时钟图标）打开“运行”列表。 然后，单击“iris_sklearn.py”显示 `iris_sklearn.py` 的“运行仪表板”。

   ![img](media/tutorial-classifying-iris/run_view.png)

2. 此时会打开“运行仪表板”选项卡。 查看在多个运行中捕获的统计信息。 图形呈现在选项卡顶部，每个带编号的运行及其详细信息列在页面底部的表中。

   ![img](media/tutorial-classifying-iris/run_dashboard.png)

3. 筛选该表，然后在图形中单击，以便以交互方式查看每个运行的状态、持续时间、准确性和正则化率。 

4. 在“运行”表中选择两个或三个运行，单击“比较”按钮打开详细的比较页。 查看并列比较。 单击比较页左上角的“运行列表”后退按钮返回到“运行仪表板”。

5. 单击单个运行可查看该运行的详细信息视图。 请注意，所选运行的统计信息列在“运行属性”部分中。 写入输出文件夹中的文件列在“输出”部分中并可下载。

   ![img](media/tutorial-classifying-iris/run_details.png)

   “视觉对象”部分中呈现了两个绘图：混淆矩阵和多类 ROC 曲线。 还可以在“日志”部分中找到所有日志文件。

## <a name="execute-scripts-in-the-local-docker-environment"></a>在本地 Docker 环境中执行脚本

使用 Azure 机器学习可以轻松配置其他执行环境（例如 Docker）并在这些环境中运行脚本。 

>[!IMPORTANT]
>若要完成此步骤，必须在本地安装并启动 Docker 引擎。 有关更多详细信息，请参阅安装指南。

1. 在左侧工具栏上，选择文件夹图标打开项目的“文件”列表。 展开 `aml_config` 文件夹。 

2. 请注意，已预配置了多个环境，例如 **docker-python**、**docker-spark** 和 **local**。 

   每个环境包含两个文件，例如 `docker-python.compute` 和 `docker-python.runconfig`。 打开每种文件可以发现，某些选项可在文本编辑器中进行配置。  

   关闭（单击“X”）任何已打开的文本编辑器对应的选项卡可进行清理。

3. 使用 **docker-python** 环境运行 **iris_sklearn.py** 脚本。 

   - 在左侧工具栏中，单击时钟图标打开“运行”面板。 单击“所有运行”。 
   - 在“所有运行”选项卡的顶部，选择 **docker-python** 作为目标环境，而不要选择默认的 **local**。 
   - 接下来，转到右侧，选择 **iris_sklearn.py** 作为要运行的脚本。 
   - 将“参数”字段留空，因为脚本指定了默认值。 
   - 单击“运行”按钮。

4. 如 Workbench 窗口右侧的“作业”面板中所示，可以看到新作业已启动。

   首次针对 Docker 运行时，该作业需要花费额外的几分钟才能完成。 

   在幕后，Azure Machine Learning Workbench 会生成一个新的 Docker 文件，该文件引用 `docker.compute` 文件中指定的基础 Docker 映像，以及 `conda_dependencies.yml` 文件中指定的依赖项 Python 包。 然后，Docker 引擎从 Azure 下载基础映像，安装 `conda_dependencies.yml` 文件中指定的 Python 包，并启动 Docker 容器。 然后，Docker 引擎复制（或引用，具体取决于运行配置）项目文件夹的本地副本，并执行 `iris_sklearn.py` 脚本。 最后，看到的结果应与使用 **local** 作为目标时的结果完全相同。

5. 现在，让我们试试 Spark。 Docker 基础映像包含预先安装且配置的 Spark 实例。 因此，可在该实例中执行 PySpark 脚本。 这是开发和测试 Spark 程序的一种简便方法，无需花费时间自行安装和配置 Spark。 

   打开 `iris_pyspark.py` 文件。 此脚本加载 `iris.csv` 数据文件，并使用 Spark 机器学习库中的逻辑回归算法将“鸢尾花”数据集分类。 现在，将运行环境更改为 **docker-spark**，并将脚本更改为 **iris_pyspark.py**，然后再次运行。 由于必须在 Docker 容器中创建并启动 Spark 会话，因此，此过程所需的时间稍长一些。 还可以看到，stdout 不同于 `iris_pyspark.py` 的 stdout。

6. 多运行几次，并使用不同的参数进行演练。 

7. 打开 `iris_pyspark.py` 文件，查看使用 Spark 机器学习库生成的简单逻辑回归模型。 

8. 与不同执行环境中的运行的“作业”面板、运行历史记录列表视图和运行详细信息视图交互。

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>在 Azure 机器学习 CLI 窗口中执行脚本

1. 在 Azure Machine Learning Workbench 中，依次单击“文件”菜单和“打开命令提示符”启动命令行窗口。 命令提示符会在项目文件夹中启动，提示符为 `C:\Temp\myIris\>`。

   >[!Important]
   >必须使用命令行窗口（从 Workbench 启动）完成以下步骤：

2. 使用命令提示符 (CLI) 登录到 Azure。 

   针对 Azure 资源进行身份验证时，Workbench 应用和 CLI 使用独立的凭据缓存。 在缓存的令牌过期之前，只需执行此操作一次。 **az account list** 命令返回可用于登录的订阅列表。 如果有多个订阅，请使用所需订阅中的 ID 值，并将该订阅设置为与 **az set account -s** 命令配合使用的默认帐户，此时应提供订阅 ID 值。 然后使用 account show 命令确认设置。

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. 完成身份验证并设置当前 Azure 订阅上下文后，请在 CLI 窗口中键入以下命令来安装 matplotlib，并提交 python 脚本作为要运行的试验。

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. 查看输出。 可以看到，输出和结果与前面在本教程中使用 Workbench 运行脚本后的输出和结果相同。 

5. 如果在计算机上安装了 Docker，可以使用 Docker 执行环境运行同一脚本。

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. 在 Azure Machine Learning Workbench 中，单击左侧工具栏上的“文件夹”图标列出项目文件，并打开名为 **run.py** 的 Python 脚本。 

   此脚本可用于循环访问各种正则化率，并使用这些比率多次运行试验。 此脚本使用正则化率 `10.0`（一个巨大的数字）启动 `iris_sklearn.py` 作业，在下一次运行时将比率减半，依此类推，直到比率小于 `0.005`。 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   若要从命令行启动 **run.py** 脚本，请运行以下命令：

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   `run.py` 完成时，Azure Machine Learning Workbench 的运行历史记录列表视图中会出现一个图形。

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>在远程计算机上执行 Docker 容器
若要在远程 Linux 计算机上的 Docker 容器中执行脚本，需要通过 SSH 访问（使用用户名和密码）该远程计算机。 此外，该远程计算机上必须安装并运行了 Docker 引擎。 获得此类 Linux 计算机的最简单方法是在 Azure 上创建[基于 Ubuntu 的数据科学虚拟机 (DSVM)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)。 （请注意，不支持基于 CentOS 的 DSVM。） 

1. 创建 VM 后，可使用以下命令，通过生成 `.runconfig` 和 `.compute` 文件对，将 VM 附加为执行环境。 让我们将新环境命名为 `myvm`。
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >“IP 地址”区域也可以是可公开寻址的 FQDN（完全限定的域名），例如 `vm-name.southcentralus.cloudapp.azure.com`。 最好是将 FQDN 添加到 DSVM，并在此处使用它来取代 IP 地址，因为到时可以关闭 VM 来节省成本。 此外，下次启动 VM 时，IP 地址可能会更改。

   接下来，运行以下在 VM 中构造 Docker 映像，以准备使用该映像来运行脚本。
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >还可将 `myvm.runconfig` 中的值 `PrepareEnvironment` 从默认值 `false` 更改为 `true`。 这样，在首次运行时，会自动准备 Docker 容器。

2. 编辑在 `aml_config` 下生成的 `myvm.runconfig` 文件，并将框架从默认的 `PySpark` 更改为 `Python`：

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >将框架设置保留为 PySpark 应该也没问题。 但是，如果实际上并不需要在 Spark 会话中运行 Python 脚本，则使用该设置会稍微降低效率。

3. 如前所述在 CLI 窗口中发出相同的命令，不过，这一次我们使用 _myvm_ 作为目标：
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   该命令的执行方式与使用 `docker-python` 环境时相同，不过，执行是在远程 Linux VM 上发生的。 CLI 窗口显示相同的输出信息。

4. 让我们在容器中试试 Spark。 打开文件资源管理器（如果熟悉基本的文件操作命令，则也可以通过 CLI 窗口执行此操作）。 创建 `myvm.runconfig` 文件的副本并将其命名为 `myvm-spark.runconfig`。 编辑新文件，将 `Framework` 设置从 `Python` 更改为 `PySpark`：
   ```yaml
   "Framework": "PySpark"
   ```
   不要对 `myvm.compute` 文件进行任何更改。 同一 VM 上的同一 Docker 映像用于 Spark 执行。 在新的 `myvy-spark.runconfig` 中，`target` 字段根据名称 `myvm` 指向同一 `myvm.compute` 文件。

5. 键入以下命令，在远程 Docker 容器中的 Spark 实例上运行此文件：
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>在 HDInsight 群集中执行脚本
还可以在实际 Spark 群集中运行此脚本。 

1. 如果有权访问 Azure HDInsight 群集的 Spark，请按如下所示生成 HDI 运行配置命令。 提供 HDInsight 群集名称、HDInsight 用户名和密码作为参数。 请使用以下命令：

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   群集头节点的完全限定域名 (FQDN) 通常是 `<cluster_name>-ssh.azurehdinsight.net`。

   >[!NOTE]
   >`username` 是群集的 SSH 用户名。 如果在 HDI 预配期间未更改用户名，则默认值为 `sshuser`。 用户名不是 `admin`，这是在预配期间为了能够访问群集管理员网站而创建的另一个用户。 

2. 运行以下命令，脚本将在 HDInsight 群集中运行：

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >针对远程 HDI 群集执行时，还可以使用 `admin` 用户帐户在 `https://<cluster_name>.azurehdinsight.net/yarnui` 上查看 YARN 作业执行详细信息。


## <a name="next-steps"></a>后续步骤
由三个部分构成的系列教程的本第二部分已介绍如何使用 Azure 机器学习服务来执行以下操作：
> [!div class="checklist"]
> * 在 Azure Machine Learning Workbench 中操作
> * 打开脚本并查看代码
> * 在本地环境中执行脚本
> * 查看运行历史记录
> * 在本地 Docker 环境中执行脚本
> * 在本地 Azure CLI 窗口中执行脚本
> * 在远程 Docker 环境中执行脚本
> * 在云 HDInsight 环境中执行脚本

现在，可以继续学习系列教程的第三部分。 创建逻辑回归模型后，让我们将它部署为实时 Web 服务。

> [!div class="nextstepaction"]
> [部署模型](tutorial-classifying-iris-part-3.md)

