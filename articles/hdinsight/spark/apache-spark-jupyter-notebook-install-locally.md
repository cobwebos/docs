---
title: 在本地安装 Jupyter 并将其连接到 Azure HDInsight 中的 Spark
description: 了解如何在计算机上本地安装 Jupyter 笔记本并将其连接到 Apache Spark 群集。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 7b20f0ec4669b485f87d050fcf597244fb898c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091250"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在计算机上安装 Jupyter 笔记本并连接到 HDInsight 上的 Apache Spark

本文介绍如何安装具有自定义 PySpark（适用于 Python）以及具有 Apache Spark（适用于 Scala）内核和 Spark magic 的 Jupyter 笔记本，并将笔记本连接到 HDInsight 群集。 在本地计算机上安装 Jupyter 的原因多种多样，同时这种安装也面临着多种难题。 有关此方面的详细信息，请参阅本文末尾的[为何要在计算机上安装 Jupyter](#why-should-i-install-jupyter-on-my-computer)。

有四个关键步骤涉及安装 Jupyter 并连接到 HDInsight 上的 Apache Spark。

* 配置 Spark 群集。
* 安装 Jupyter 笔记本。
* 安装 PySpark 和具有 Spark magic 的 Spark 内核。
* 配置 Spark magic 以访问 HDInsight 上的 Spark 群集。

有关适用于装有 HDInsight 群集的 Jupyter 笔记本的自定义内核和 Spark magic 的详细信息，请参阅 [Kernels available for Jupyter notebooks with Apache Spark Linux clusters on HDInsight](apache-spark-jupyter-notebook-kernels.md)（适用于装有 HDInsight 上的 Apache Spark Linux 群集的 Jupyter 笔记本的内核）。

> [!IMPORTANT]  
> 一文中的步骤仅适用到 Spark 版本 2.1.0。

## <a name="prerequisites"></a>必备组件
此处所列的先决条件不适用于安装 Jupyter。 这些先决条件适用于安装笔记本之后将 Jupyter 笔记本连接到 HDInsight 群集。

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* 在 HDInsight 上 Apache Spark 群集 （ver 2.1.0 或更低版本）。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。



## <a name="install-jupyter-notebook-on-your-computer"></a>在计算机上安装 Jupyter 笔记本

必须先安装 Python 才能安装 Jupyter 笔记本。 Python 和 Jupyter 都作为 [Anaconda 分发版](https://www.anaconda.com/download/)的一部分提供。 安装 Anaconda 时，安装的是某个 Python 发行版。 安装 Anaconda 之后，可通过运行相应命令来添加 Jupyter 安装。

1. 下载适用于平台的 [Anaconda 安装程序](https://www.anaconda.com/download/)，并运行安装程序。 运行安装向导时，请确保选择将 Anaconda 添加到 PATH 变量的选项。

2. 运行以下命令来安装 Jupyter。

        conda install jupyter

    有关安装 Jupyter 的详细信息，请参阅[使用 Anaconda 安装 Jupyter](https://jupyter.readthedocs.io/en/latest/install.html)。

## <a name="install-the-kernels-and-spark-magic"></a>安装内核和 Spark magic

有关如何安装 Spark magic、PySpark 和 Spark 内核的说明，请参阅 GitHub 上的 [sparkmagic 文档](https://github.com/jupyter-incubator/sparkmagic#installation)中的安装说明。 Spark magic 文档中的第一步会要求安装 Spark magic。 使用以下命令替换该链接中的第一步，具体取决于要连接到的 HDInsight 群集的版本。 之后，按照 Spark magic 文档中的剩余步骤进行操作。 如果要安装不同的内核，则必须执行 Spark magic 安装说明部分中的步骤 3。

* 对于群集 v3.5 和 v3.6，请通过执行 `pip install sparkmagic==0.11.2` 安装 sparkmagic 0.11.2

* 对于群集 v3.4，请通过执行 `pip install sparkmagic==0.2.3` 安装 sparkmagic 0.2.3

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>配置 Spark magic 以连接到 HDInsight Spark 群集

在本部分中，你将配置前面安装连接到你必须已创建在 Azure HDInsight 中的 Apache Spark 群集的 Spark magic。

1. 使用以下命令启动 Python shell:

    ```
    python
    ```

2. Jupyter 配置信息通常存储在用户主目录中。 输入以下命令，以标识主目录中，并创建名为的文件夹 **.sparkmagic**。  将输出的完整路径。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 在文件夹内`.sparkmagic`，创建名为的文件**config.json**并添加以下 JSON 代码片段中的其内部。  

    ```json
    {
      "kernel_python_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "kernel_scala_credentials" : {
        "username": "{USERNAME}",
        "base64_password": "{BASE64ENCODEDPASSWORD}",
        "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```
4. 对文件进行以下编辑：

    |模板值 | 新值 |
    |---|---|
    |{USERNAME}|默认的群集登录名是 admin。|
    |{CLUSTERDNSNAME}|群集名称|
    |{BASE64ENCODEDPASSWORD}|Base64 编码的密码作为实际密码。  可以生成在 base64 密码[ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/)。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|如果使用，请记住`sparkmagic 0.11.23`（群集 v3.5 和 v3.6）。  如果使用`sparkmagic 0.2.3`（群集 v3.4），将替换为`"should_heartbeat": true`。|

    你可以看到在一个完整的示例文件[示例 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)。

   > [!TIP]  
   > 将发送检测信号，以确保会话不会泄漏。 计算机进入睡眠状态或关闭时不会发送检测信号，从而导致会话被清除。 对于群集 v3.4，如果要禁用此行为，可以从 Ambari UI 将 Livy 配置 `livy.server.interactive.heartbeat.timeout` 设置为 `0`。 对于群集 v3.5，如果未设置上述 3.5 配置，会话将不会删除。

5. 启动 Jupyter。 从命令提示符使用以下命令。

        jupyter notebook

6. 验证可以使用内核随附的 Spark magic。 执行以下步骤。

    a. 创建新的笔记本。 在右下角，选择**新建**。 应会看到默认内核**Python 2**或**Python 3**和安装的内核。 实际值可能会有所不同，具体取决于所选的安装。  选择**PySpark**。

    ![Jupyter 笔记本中的内核](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter 笔记本中的内核")

    > [!IMPORTANT]  
    > 选择后**新建**查看 shell，任何错误。  如果看到此错误`TypeError: __init__() got an unexpected keyword argument 'io_loop'`可能会遇到 Tornado 某些版本的已知的问题。  如果是这样，停止内核和再降级 Tornado 安装使用以下命令： `pip install tornado==4.5.3`。

    b. 运行以下代码片段。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    如果可以成功检索输出，则表示与 HDInsight 群集的连接已经过测试。

    如果你想要更新笔记本配置以连接到不同的群集，使用一组新的值，更新 config.json 上面步骤 3 中所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>为何要在计算机上安装 Jupyter？

你可能会出于多种原因而要在计算机上安装 Jupyter，然后将其连接到 HDInsight 上的 Apache Spark 群集。

* 尽管 Azure HDInsight 中的 Spark 群集上已提供 Jupyter 笔记本，但在计算机上安装 Jupyter 可以选择在本地创建笔记本，根据正在运行的群集测试应用程序，然后将笔记本上传到群集。 若要将笔记本上传到群集，可以使用群集上运行的 Jupyter 笔记本来上传，或者将它们保存到与群集关联的存储帐户中的 /HdiNotebooks 文件夹。 有关如何在群集上存储笔记本的详细信息，请参阅 [Where are Jupyter notebooks stored？](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)（Jupyter 笔记本存储在何处？）
* 使用本地提供的笔记本可以根据应用程序要求连接到不同的 Spark 群集。
* 可以使用 GitHub 实施源代码管理系统，并对笔记本进行版本控制。 此外，还可以建立一个协作环境，其中的多个用户可以使用同一个笔记本。
* 甚至不需要启动群集就能在本地使用笔记本。 只需创建一个群集以根据它来测试笔记本，而不需要手动管理笔记本或开发环境。
* 配置自己的本地开发环境比在群集上配置 Jupyter 安装更容易。  可以利用本地安装的所有软件，而不需要配置一个或多个远程群集。

> [!WARNING]  
> 在本地计算机上安装 Jupyter 后，多个用户可以同时在同一个 Spark 群集上运行同一个笔记本。 在这种情况下，会创建多个 Livy 会话。 如果遇到问题并想要调试，则跟踪哪个 Livy 会话属于哪个用户将是一项复杂的任务。  

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
