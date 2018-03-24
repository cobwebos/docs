---
title: 为 Azure 机器学习服务部署模型的教程
description: 本教程全面介绍如何使用 Azure 机器学习服务。 这是第三部分，讨论部署模型。
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 3/13/2018
ms.openlocfilehash: 87d1e605bfd7603e4e07f6b427033fe2c1d2b83e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-3-classify-iris-deploy-a-model"></a>教程 3：鸢尾花分类：部署模型
Azure 机器学习（预览版）是一个集成式的端到端数据科学和高级分析解决方案，适用于专业数据科学家。 数据科学家可以使用它以云的规模准备数据、开发试验和部署模型。

本教程是**由三个部分构成的系列教程的第三部分**。 在本教程的此部分，请使用机器学习（预览版）来执行以下操作：

> [!div class="checklist"]
> * 找到模型文件。
> * 生成评分脚本和架构文件。
> * 准备环境。
> * 创建实时 Web 服务。
> * 运行实时 Web 服务。
> * 检查输出 Blob 数据。 

本教程使用了历久弥新的[鸢尾花卉数据集](https://en.wikipedia.org/wiki/Iris_flower_data_set)。 

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：
- Azure 订阅。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已创建一个试验帐户，并已根据此[快速入门](quickstart-installation.md)中所述安装了 Azure Machine Learning Workbench
- [教程第 2 部分](tutorial-classifying-iris-part-2.md)中所述的分类模型
- 在本地安装和运行的 Docker 引擎

## <a name="download-the-model-pickle-file"></a>下载模型 pickle 文件
在本教程的前一部分，已在 Machine Learning Workbench 中以本地方式运行了 iris_sklearn.py 脚本。 该操作已使用流行的 Python 对象序列化包 [pickle](https://docs.python.org/3/library/pickle.html) 序列化了逻辑回归模型。 

1. 打开 Machine Learning Workbench 应用程序， 然后打开在本系列教程前面部分创建的 myIris 项目。

2. 打开项目后，选择左侧窗格中的“文件”按钮（文件夹图标），打开项目文件夹中的文件列表。

3. 选择 iris_sklearn.py 文件。 Python 代码随即会在 Workbench 中的新文本编辑器选项卡中打开。

4. 查看 **iris_sklearn.py** 文件，确定在哪个位置生成了 pickle 文件。 选择 Ctrl+F 打开“查找”对话框，然后在 Python 代码中找到 pickle 一词。

   此代码片段演示 pickle 输出文件是如何生成的。 输出 pickle 文件在磁盘上命名为 model.pkl。 

   ```python
   print("Export the model to model.pkl")
   f = open('./outputs/model.pkl', 'wb')
   pickle.dump(clf1, f)
   f.close()
   ```

5. 在前一运行的输出文件中找到模型 pickle 文件。
   
   运行 **iris_sklearn.py** 脚本时，模型文件已写入名为 **model.pkl** 的 **outputs** 文件夹。 此文件夹驻留在选择用于运行脚本的执行环境中，而不是驻留在本地项目文件夹中。 
   
   a. 若要找到该文件，请选择左窗格中的“运行”按钮（时钟图标），打开“所有运行”列表。 

   b. 此时会打开“所有运行”选项卡。 在运行表中，选择一个目标为 local 且脚本名称为 iris_sklearn.py 的最近运行。 

   c. 此时会打开“运行属性”窗格。 在窗格的右上部分，可以看到“输出”部分。

   d.单击“下一步”。 若要下载 pickle 文件，请选中 model.pkl 文件旁边的复选框，然后选择“下载”。 将文件保存到项目文件夹的根目录。 后续步骤需要该文件。

   ![下载 pickle 文件](media/tutorial-classifying-iris/download_model.png)

   在[如何读取和写入大型数据文件](how-to-read-write-files.md)中详细了解 `outputs` 文件夹。

## <a name="get-the-scoring-script-and-schema-files"></a>获取评分脚本和架构文件
若要部署 Web 服务和模型文件，还需一个评分脚本。 也可能需要适用于 Web 服务输入数据的架构。 评分脚本从当前文件夹加载 **model.pkl** 文件，并使用它来生成新预测。

1. 打开 Machine Learning Workbench 应用程序， 然后打开在本系列教程前一部分创建的 myIris 项目。

2. 打开项目后，选择左侧窗格中的“文件”按钮（文件夹图标），打开项目文件夹中的文件列表。

3. 选择 score_iris.py 文件。 此时会打开 Python 脚本。 此文件用作评分文件。

   ![评分文件](media/tutorial-classifying-iris/model_data_collection.png)

4. 若要获取架构文件，请运行该脚本。 在命令栏中选择 local 环境和 score_iris.py 脚本，然后选择“运行”。 

   该脚本在“输出”部分创建一个 JSON 文件，用于捕获模型所需的输入数据架构。

6. 请注意“项目仪表板”窗格右侧的“作业”窗格。 等待最新的 score_iris.py 作业显示绿色的“已完成”状态。 然后选择最新作业运行对应的超链接 **score_iris.py**，查看运行详细信息。 

7. 在“运行属性”窗格的“输出”部分，选择新建的 service_schema.json 文件。 选中文件名旁边的复选框，然后选择“下载”。 将该文件保存到项目根文件夹。

8. 返回到在其中打开了 score_iris.py 脚本的前一选项卡。 使用数据收集即可从 Web 服务捕获模型输入和预测。 以下步骤专用于数据收集。

9. 查看文件顶部用于导入 **ModelDataCollector** 类的代码，因为其中包含模型数据收集功能：

   ```python
   from azureml.datacollector import ModelDataCollector
   ```

10. 查看 init() 函数中用于实例化 ModelDataCollector 的以下代码行：

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")`
    ```

11. 查看 run(input_df) 函数中的以下代码行，因为这些行用于收集输入和预测数据：

    ```python
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

现在可以准备环境来操作化模型。

## <a name="prepare-to-operationalize-locally"></a>在本地准备操作化
使用“本地模式”部署在本地计算机上的 Docker 容器中运行。

可以使用“本地模式”进行开发和测试。 若要完成以下步骤来操作化模型，必须在本地运行 Docker 引擎。 可以在每个命令尾部使用 `-h` 标志来显示相应的帮助消息。

>[!NOTE]
>如果本地没有 Docker 引擎，仍可通过在 Azure 中创建用于部署的群集来继续操作。 完成本教程后，可以保留群集以便重复使用，或者将其删除，以免持续产生费用。

>[!NOTE]
>本地部署的 Web 服务不会显示在 Azure 门户的服务列表中。 它们将在本地计算机上的 Docker 中运行。

1. 打开命令行界面 (CLI)。
   在 Machine Learning Workbench 应用程序的“文件”菜单上，选择“打开命令提示符”。

   命令行提示符会在当前项目文件夹位置 **c:\temp\myIris>** 中打开。

2. 创建环境。 必须针对每个环境运行此步骤一次。 例如，针对开发环境运行一次，针对生产环境运行一次。 对于这第一个环境，请使用“本地模式”。 稍后可尝试在以下命令中使用 `-c` 或 `--cluster` 开关，以“群集模式”设置环境。

   以下设置命令要求你具有订阅的“参与者”访问权限。 如果没有该权限，则至少应具有要将内容部署到其中的资源组的“参与者”访问权限。 如果属于后一种情况，需在设置命令中使用 `-g` 标志来指定资源组名称。 

   ```azurecli
   az ml env setup -n <new deployment environment name> --location <e.g. eastus2>
   ```
   
   遵照屏幕说明预配一个用于存储 Docker 映像的存储帐户、一个用于列出 Docker 映像的 Azure 容器注册表，以及一个用于收集遥测数据的 Azure Application Insights 帐户。 如果使用 `-c` 开关，此命令会额外创建容器服务群集。
   
   可以参考群集名称识别环境。 位置应该与在 Azure 门户中创建的模型管理帐户的位置相同。

   为了确保环境设置成功，请使用以下命令来检查状态：

   ```azurecli
   az ml env show -n <deployment environment name> -g <existing resource group name>
   ```

   在步骤 5 中设置环境之前，请确保“Provisioning State”的值为“Succeeded”，如下所示：

   ![预配状态](media/tutorial-classifying-iris/provisioning_state.png)
 
3. 如果尚未在本教程前面部分创建模型管理帐户，请现在就创建。 这是一次性设置。
   ```azurecli
   az ml account modelmanagement create --location <e.g. eastus2> -n <new model management account name> -g <existing resource group name> --sku-name S1
   ```
   
4. 设置模型管理帐户。
   ```azurecli
   az ml account modelmanagement set -n <youracctname> -g <yourresourcegroupname>
   ```

5. 设置环境。

   完成设置后，使用以下命令设置操作化环境时所需的环境变量。 使用前面在步骤 2 中用过的同一环境名称。 使用完成设置过程时在命令窗口中输出的同一资源组名称。

   ```azurecli
   az ml env set -n <deployment environment name> -g <existing resource group name>
   ```

6. 若要验证是否为本地 Web 服务部署正确配置了操作化环境，请输入以下命令：

   ```azurecli
   az ml env show
   ```

现在可以创建实时 Web 服务。

>[!NOTE]
>可以重复使用模型管理帐户和环境进行后续的 Web 服务部署。 不必为每个 Web 服务创建它们。 帐户或环境可以有多个与之关联的 Web 服务。

## <a name="create-a-real-time-web-service-in-one-command"></a>以一条命令创建实时 Web 服务
1. 若要创建实时 Web 服务，请使用以下命令：

   ```azurecli
   az ml service create realtime -f score_iris.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true -c aml_config\conda_dependencies.yml
   ```
   此命令生成稍后可用的 Web 服务 ID。

   可将以下开关与 **az ml service create realtime** 命令结合使用：

   * `-f`：评分脚本文件名。

   * `--model-file`：模型文件。 在此示例中，它是 pickle model.pkl 文件。

   * `-s`：服务架构。 这是在前面的步骤中生成的，方法是在本地运行 **score_iris.py** 脚本。

   * `-n`：应用名称，必须全部采用小写形式。

   * `-r`：模型的运行时。 在此示例中，它是 Python 模型。 有效的运行时为 `python` 和 `spark-py`。

   * `--collect-model-data true`：此开关启用数据收集功能。

   * `-c`：在其中指定了其他包的 conda 依赖项文件的路径。

   >[!IMPORTANT]
   >服务名称，也是新的 Docker 映像名称，必须全部小写， 否则会出错。 

2. 运行该命令时，模型和评分文件会上传到在设置环境过程中创建的存储帐户。 部署过程会生成包含模型、架构和评分文件的 Docker 映像，然后将其推送到 Azure 容器注册表：\<ACR_name\>.azureacr.io/\<imagename\>:\<version\>。 

   此命令会在本地将该映像提取到计算机，然后启动基于该映像的 Docker 容器。 如果环境是以群集模式配置的，则 Docker 容器会改为部署到 Azure 云服务 Kubernetes 群集中。

   在部署过程中，会在本地计算机上创建 Web 服务的 HTTP REST 终结点。 几分钟后，该命令会完成并返回成功消息。 Web 服务已准备好运行！

3. 若要查看正在运行的 Docker 容器，请使用 docker ps 命令：

   ```azurecli
   docker ps
   ```

## <a name="create-a-real-time-web-service-by-using-separate-commands"></a>使用单独的命令创建实时 Web 服务
除了前面所示的 az ml service create realtime 命令以外，还可以单独执行这些步骤。 

先注册模型， 再生成清单，然后生成 Docker 映像，最后创建 Web 服务。 这种循序渐进的方法能够更灵活地控制每个步骤。 此外，可以重复使用前述步骤生成的实体，仅当需要时才重新生成实体。

1. 通过提供 pickle 文件名注册模型。

   ```azurecli
   az ml model register --model model.pkl --name model.pkl
   ```
   此命令生成模型 ID。

2. 创建清单。

   若要创建清单，请使用以下命令并提供前一步骤生成的模型 ID 输出：

   ```azurecli
   az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
   ```
   此命令生成清单 ID。

3. 创建 Docker 图像。

   若要创建 Docker 映像，请使用以下命令并提供前一步骤生成的清单 ID 值输出。 也可选择使用 `-c` 开关来包括 conda 依赖项。

   ```azurecli
   az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
   ```
   此命令生成 Docker 映像 ID。
   
4. 创建服务。

   若要创建服务，请使用以下命令并提供前一步骤生成的映像 ID 输出：

   ```azurecli
   az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
   ```
   此命令生成 Web 服务 ID。

现在，可以运行该 Web 服务。

## <a name="run-the-real-time-web-service"></a>运行实时 Web 服务

若要测试正在运行的 irisapp Web 服务，请使用 JSON 编码的记录（其中包含由四个随机数构成的数组）。

1. 该 Web 服务包括示例数据。 以本地模式运行时，可以调用 **az ml service usage realtime** 命令。 该调用会检索可用于测试服务的示例运行命令。 该调用还会检索可用于将服务合并到你自己的自定义应用中的评分 URL。

   ```azurecli
   az ml service usage realtime -i <web service ID>
   ```

2. 若要测试服务，请执行返回的服务运行命令：
    
   ```azurecli
   az ml service run realtime -i <web service ID> -d "{\"input_df\": [{\"petal width\": 0.25, \"sepal length\": 3.0, \"sepal width\": 3.6, \"petal length\": 1.3}]}"
   ```

   输出为“Iris-setosa”，即预测的类。 （你的结果可能不同。） 

## <a name="view-the-collected-data-in-azure-blob-storage"></a>查看在 Azure Blob 存储中收集的数据

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 找到自己的存储帐户。 为此，请选择“所有服务”。

3. 在搜索框中输入“存储帐户”，然后按 Enter。

4. 在“存储帐户”搜索框中，选择与环境匹配的“存储帐户”资源。 

   > [!TIP]
   > 若要确定哪个存储帐户正在使用，请执行以下操作：
   > 1. 打开 Machine Learning Workbench。
   > 2. 选择正在处理的项目。
   > 3. 从“文件”菜单打开命令行提示符。
   > 4. 在命令行提示符处输入 `az ml env show -v` 并查看 storage_account 值。 这是存储帐户的名称。

5. 当“存储帐户”窗格打开后，从“服务”部分选择“Blob”。 找到名为 **modeldata** 的容器。 
 
   如果看不到任何数据，可能需要在发出第一个 Web 服务请求之后，最长等待 10 分钟才能看到数据传播到存储帐户。

   数据通过以下容器路径流入 Blob：

   ```
   /modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv
   ```

6. 可以从 Azure Blob 存储使用此数据。 有多种使用 Microsoft 软件和开源工具的工具，例如：

   * 机器学习：打开 CSV 文件，方法是将 CSV 文件作为数据源来添加。

   * Excel：将日常 CSV 文件作为电子表格打开。

   * [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)：使用从 Blob 中的 CSV 数据提取的数据创建图表。

   * [Hive](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started)：将 CSV 数据加载到 Hive 表中，直接对 Blob 执行 SQL 查询。

   * [Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-overview)：使用大部分 CSV 数据创建 DataFrame。

      ```python
      var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
      ```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤
由三个部分构成的系列教程的此第三部分已介绍如何使用机器学习来执行以下操作：
> [!div class="checklist"]
> * 找到模型文件。
> * 生成评分脚本和架构文件。
> * 准备环境。
> * 创建实时 Web 服务。
> * 运行实时 Web 服务。
> * 检查输出 Blob 数据。 

现已成功地在各种计算环境中运行训练脚本， 并且还创建模型、序列化模型，并通过基于 Docker 的 Web 服务将模型操作化。 

现在可以进行高级数据准备：
> [!div class="nextstepaction"]
> [教程 4 - 高级数据准备](tutorial-bikeshare-dataprep.md)
