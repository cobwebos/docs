---
title: 在本地安装 Jupyter 并将其连接到 Azure HDInsight 中的 Spark
description: 了解如何在计算机上本地安装 Jupyter 笔记本并将其连接到 Apache Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/07/2019
ms.openlocfilehash: 225ee7028b9610a4974f9bee05da667d78d3355e
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903736"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在计算机上安装 Jupyter 笔记本并连接到 HDInsight 上的 Apache Spark

本文介绍如何安装具有自定义 PySpark（适用于 Python）以及具有 Apache Spark（适用于 Scala）内核和 Spark magic 的 Jupyter 笔记本，并将笔记本连接到 HDInsight 群集。 在本地计算机上安装 Jupyter 的原因多种多样，同时这种安装也面临着多种难题。 有关此方面的详细信息，请参阅本文末尾的[为何要在计算机上安装 Jupyter](#why-should-i-install-jupyter-on-my-computer)。

安装 Jupyter 并连接到 HDInsight 上的 Apache Spark 涉及到四个重要步骤。

* 配置 Spark 群集。
* 安装 Jupyter 笔记本。
* 安装 PySpark 和具有 Spark magic 的 Spark 内核。
* 配置 Spark magic 以访问 HDInsight 上的 Spark 群集。

有关适用于装有 HDInsight 群集的 Jupyter 笔记本的自定义内核和 Spark magic 的详细信息，请参阅 [Kernels available for Jupyter notebooks with Apache Spark Linux clusters on HDInsight](apache-spark-jupyter-notebook-kernels.md)（适用于装有 HDInsight 上的 Apache Spark Linux 群集的 Jupyter 笔记本的内核）。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。 这是安装笔记本之后将 Jupyter 笔记本连接到 HDInsight 群集的先决条件。

* 熟悉 Jupyter Notebook 和 Spark on HDInsight 的结合使用。

## <a name="install-jupyter-notebook-on-your-computer"></a>在计算机上安装 Jupyter 笔记本

必须先安装 Python 才能安装 Jupyter 笔记本。 [Anaconda 分发版](https://www.anaconda.com/download/)将安装 Python 和 Jupyter Notebook。

下载适用于平台的 [Anaconda 安装程序](https://www.anaconda.com/download/)，并运行安装程序。 运行安装向导时，请确保选择将 Anaconda 添加到 PATH 变量的选项。  另请参阅[使用 Anaconda 安装 Jupyter](https://jupyter.readthedocs.io/en/latest/install.html)。

## <a name="install-spark-magic"></a>安装 Spark magic

1. 输入以下命令之一以安装 Spark magic。 另请参阅 [sparkmagic 文档](https://github.com/jupyter-incubator/sparkmagic#installation)。

    |群集版本 | 安装命令 |
    |---|---|
    |v3.6 和 v3.5 |`pip install sparkmagic==0.13.1`|
    |v3.4|`pip install sparkmagic==0.2.3`|

1. 确保通过运行以下命令正确安装了 `ipywidgets`：

    ```cmd
    jupyter nbextension enable --py --sys-prefix widgetsnbextension
    ```

## <a name="install-pyspark-and-spark-kernels"></a>安装 PySpark 和 Spark 内核

1. 通过输入以下命令确定 `sparkmagic` 的安装位置：

    ```cmd
    pip show sparkmagic
    ```

    然后，将工作目录更改为使用上述命令确定的位置。

1. 从新的工作目录输入以下一个或多个命令，以安装所需的内核：

    |内核 | 命令 |
    |---|---|
    |Spark|`jupyter-kernelspec install sparkmagic/kernels/sparkkernel`|
    |SparkR|`jupyter-kernelspec install sparkmagic/kernels/sparkrkernel`|
    |PySpark|`jupyter-kernelspec install sparkmagic/kernels/pysparkkernel`|
    |PySpark3|`jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel`|

1. 可选。 输入以下命令以启用服务器扩展：

    ```cmd
    jupyter serverextension enable --py sparkmagic
    ```

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>配置 Spark magic 以连接到 HDInsight Spark 群集

在本部分中，将之前安装的 Spark magic 配置为连接到 Apache Spark 群集。

1. 使用以下命令启动 Python shell：

    ```cmd
    python
    ```

2. Jupyter 配置信息通常存储在用户主目录中。 输入以下命令来标识主目录，并创建一个名为 **.sparkmagic** 的文件夹。  将输出完整路径。

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. 在文件夹 `.sparkmagic` 中，创建名为 **config.json** 的文件，并在该文件中添加以下 JSON 代码片段。  

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

      "custom_headers" : {
        "X-Requested-By": "livy"
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```

4. 对该文件进行以下编辑：

    |模板值 | 新值 |
    |---|---|
    |{USERNAME}|群集登录名，默认为 `admin`。|
    |{CLUSTERDNSNAME}|群集名称|
    |{BASE64ENCODEDPASSWORD}|实际密码的 base64 编码密码。  可在 [https://www.url-encode-decode.com/base64-encode-decode/](https://www.url-encode-decode.com/base64-encode-decode/) 中生成 base64 密码。|
    |`"livy_server_heartbeat_timeout_seconds": 60`|如果使用 `sparkmagic 0.12.7`（群集 v3.5 和 v3.6），请保留此值。  如果使用 `sparkmagic 0.2.3`（群集 v3.4），请替换为 `"should_heartbeat": true`。|

    可在[示例 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json) 中查看完整的示例文件。

   > [!TIP]  
   > 将发送检测信号，以确保会话不会泄漏。 计算机进入睡眠状态或关闭时不会发送检测信号，从而导致会话被清除。 对于群集 v3.4，如果要禁用此行为，可以从 Ambari UI 将 Livy 配置 `livy.server.interactive.heartbeat.timeout` 设置为 `0`。 对于群集 v3.5，如果未设置上述 3.5 配置，会话将不会删除。

5. 启动 Jupyter。 从命令提示符使用以下命令。

    ```cmd
    jupyter notebook
    ```

6. 验证是否可以使用内核随附的 Spark magic。 执行以下步骤。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 创建新的笔记本。 在右侧一角选择“新建”。 应会看到默认内核 **Python 2** 和 **Python 3**，以及安装的内核。 实际值根据安装时所做的选择而有所不同。  选择“PySpark”。

    ![Jupyter 笔记本中的可用内核](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels-notebook.png "Jupyter notebook 中的内核")

    > [!IMPORTANT]  
    > 选择“新建”后，检查 shell 中是否出现任何错误。  如果看到错误 `TypeError: __init__() got an unexpected keyword argument 'io_loop'`，原因可能是遇到了某些 Tornado 版本中的已知问题。  如果出现此情况，请停止内核，然后使用以下命令降级 Tornado 安装：`pip install tornado==4.5.3`。

    b. 运行以下代码片段。

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    如果可以成功检索输出，则表示与 HDInsight 群集的连接已经过测试。

    若要更新笔记本配置以连接到不同的群集，请使用一组新值更新 config.json，如上述步骤 3 中所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>为何要在计算机上安装 Jupyter？

你可能会出于多种原因而要在计算机上安装 Jupyter，然后将其连接到 HDInsight 上的 Apache Spark 群集。

* 尽管 Azure HDInsight 中的 Spark 群集上已提供 Jupyter 笔记本，但在计算机上安装 Jupyter 可以选择在本地创建笔记本，根据正在运行的群集测试应用程序，并将笔记本上载到群集。 要将笔记本上传到群集，可以使用群集上运行的 Jupyter 笔记本来上传，或者将它们保存到与群集关联的存储帐户中的 /HdiNotebooks 文件夹。 有关如何在群集上存储笔记本的详细信息，请参阅 [Where are Jupyter notebooks stored？](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)（Jupyter 笔记本存储在何处？）
* 使用本地提供的笔记本可以根据应用程序要求连接到不同的 Spark 群集。
* 可以使用 GitHub 实施源代码管理系统，并对笔记本进行版本控制。 此外，还可以建立一个协作环境，其中的多个用户可以使用同一个笔记本。
* 甚至不需要启动群集就能在本地使用笔记本。 只需创建一个群集以根据它来测试笔记本，而不需要手动管理笔记本或开发环境。
* 配置自己的本地开发环境比在群集上配置 Jupyter 安装更容易。  你可以利用本地安装的所有软件，无需配置一个或多个远程群集。

> [!WARNING]  
> 在本地计算机上安装 Jupyter 后，多个用户可以同时在同一个 Spark 群集上运行同一个笔记本。 在这种情况下，会创建多个 Livy 会话。 如果遇到问题并想要调试，则跟踪哪个 Livy 会话属于哪个用户将是一项复杂的任务。  

## <a name="next-steps"></a>后续步骤

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
