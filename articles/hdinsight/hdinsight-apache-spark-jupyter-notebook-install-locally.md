---
title: "在本地安装 Jupyter 并将其连接到 Azure HDInsight Spark 群集 | Microsoft Docs"
description: "了解如何在计算机上本地安装 Jupyter 笔记本并将其连接到 Azure HDInsight 上的 Apache Spark 群集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: fe9dcdb643aa6a8ee5d55738b7a446e4b0153986
ms.contentlocale: zh-cn
ms.lasthandoff: 06/13/2017

---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>在计算机上安装 Jupyter 笔记本并连接到 HDInsight 上的 Apache Spark

在本文中，你将了解如何安装具有自定义 PySpark（适用于 Python）以及具有 Spark（适用于 Scala）内核和 Spark magic 的 Jupyter 笔记本，然后将笔记本连接到 HDInsight 群集。 在本地计算机上安装 Jupyter 的原因多种多样，同时这种安装也面临着多种难题。 有关此方面的详细信息，请参阅本文末尾的[为何要在计算机上安装 Jupyter](#why-should-i-install-jupyter-on-my-computer)。

在计算机上安装 Jupyter 和 Spark magic 包括三个重要步骤。

* 安装 Jupyter 笔记本
* 安装 PySpark 和具有 Spark magic 的 Spark 内核
* 配置 Spark magic 以访问 HDInsight 上的 Spark 群集

有关适用于装有 HDInsight 群集的 Jupyter 笔记本的自定义内核和 Spark magic 的详细信息，请参阅 [Kernels available for Jupyter notebooks with Apache Spark Linux clusters on HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)（适用于装有 HDInsight 上的 Apache Spark Linux 群集的 Jupyter 笔记本的内核）。

## <a name="prerequisites"></a>先决条件
此处所列的先决条件不适用于安装 Jupyter。 这些先决条件适用于安装笔记本之后将 Jupyter 笔记本连接到 HDInsight 群集。

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="install-jupyter-notebook-on-your-computer"></a>在计算机上安装 Jupyter 笔记本

必须先安装 Python 才能安装 Jupyter 笔记本。 Python 和 Jupyter 都作为 [Anaconda 分发版](https://www.continuum.io/downloads)的一部分提供。 安装 Anaconda 时，安装的是某个 Python 发行版。 安装 Anaconda 之后，可通过运行相应命令来添加 Jupyter 安装。

1. 下载适用于平台的 [Anaconda 安装程序](https://www.continuum.io/downloads)，然后运行安装程序。 运行安装向导时，请确保选择将 Anaconda 添加到 PATH 变量的选项。
2. 运行以下命令来安装 Jupyter。

        conda install jupyter

    有关安装 Jupyter 的详细信息，请参阅[使用 Anaconda 安装 Jupyter](http://jupyter.readthedocs.io/en/latest/install.html)。

## <a name="install-the-kernels-and-spark-magic"></a>安装内核和 Spark magic

有关如何安装 Spark magic、PySpark 和 Spark 内核的说明，请参阅 GitHub 上的 [sparkmagic 文档](https://github.com/jupyter-incubator/sparkmagic#installation)中的安装说明。 Spark magic 文档中的第一步会要求你安装 Spark magic。 使用以下命令替换该链接中的第一步，具体取决于要连接到的 HDInsight 群集的版本。 之后，按照 Spark magic 文档中的剩余步骤进行操作。 如果要安装不同的内核，则必须执行 Spark magic 安装说明部分中的步骤 3。

* 对于群集 v3.4，请通过执行 `pip install sparkmagic==0.2.3` 安装 sparkmagic 0.2.3

* 对于群集 v3.5 和 v3.6，请通过执行 `pip install sparkmagic==0.11.2` 安装 sparkmagic 0.11.2

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>配置 Spark magic 以连接到 HDInsight Spark 群集

在本部分中，你将配置前面安装的 Spark magic，以连接到 Apache Spark 群集（必须事先在 Azure HDInsight 中创建）。

1. Jupyter 配置信息通常存储在用户主目录中。 若要在任何 OS 平台上找到你的主目录，请键入以下命令。

    启动 Python shell。 在命令窗口中键入以下命令：

        python

    在 Python shell 中，输入以下命令以找到主目录。

        import os
        print(os.path.expanduser('~'))

2. 导航到主目录，然后创建一个名为 **.sparkmagic** 的文件夹（如果尚不存在）。
3. 在该文件夹中，创建一个名为 **config.json** 的文件，然后在该文件中添加以下 JSON 代码片段。

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
          }
        }

4. 将 **{USERNAME}**、**{CLUSTERDNSNAME}** 和**{BASE64ENCODEDPASSWORD}** 替换为适当的值。 可以使用许多以你偏好的编程语言编写的实用工具或联机实用工具，来生成 base64 编码的密码作为实际密码。

5. 在 `config.json` 中配置相应的检测信号设置。 应以早前添加 `kernel_python_credentials` 和 `kernel_scala_credentials` 代码片段的等级添加这些设置。 有关添加该信号检测设置的方法和位置的示例，请参阅此[示例 config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json)。

    * 对于 `sparkmagic 0.2.3`（群集 v3.4），包括：

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * 对于 `sparkmagic 0.11.2`（群集 v3.5 和 v3.6），包括：

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >将发送检测信号，以确保会话不会泄漏。 计算机进入睡眠状态或关闭时不会发送检测信号，从而导致会话被清除。 对于群集 v3.4，如果要禁用此行为，可以从 Ambari UI 将 Livy 配置 `livy.server.interactive.heartbeat.timeout` 设置为 `0`。 对于群集 v3.5，如果未设置上述 3.5 配置，会话将不会删除。

6. 启动 Jupyter。 从命令提示符使用以下命令。

        jupyter notebook

7. 验证是否可以使用 Jupyter 笔记本连接到群集，以及是否可以使用内核随附的 Spark magic。 执行以下步骤。

    a. 创建新的笔记本。 在右下角单击“新建”。 用户应该会看到默认内核 **Python2** 和安装的两个新内核：**PySpark** 和 **Spark**。 单击“PySpark”。

    ![Jupyter 笔记本中的内核](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Jupyter 笔记本中的内核")

    b. 运行以下代码片段。

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    如果可以成功检索输出，则表示与 HDInsight 群集的连接已经过测试。

    >[!TIP]
    >如果你想要更新笔记本配置以连接到不同的群集，请使用一组新值更新 config.json，如上述步骤 3 中所示。

## <a name="why-should-i-install-jupyter-on-my-computer"></a>为何要在计算机上安装 Jupyter？
你可能会出于多种原因而要在计算机上安装 Jupyter，然后将其连接到 HDInsight 上的 Spark 群集。

* 尽管 Azure HDInsight 中的 Spark 群集上已提供 Jupyter 笔记本，但在计算机上安装 Jupyter 可以选择在本地创建笔记本，根据正在运行的群集测试你的应用程序，然后将笔记本上传到群集。 若要将笔记本上传到群集，可以使用群集上运行的 Jupyter 笔记本来上传，或者将它们保存到与群集关联的存储帐户中的 /HdiNotebooks 文件夹。 有关如何在群集上存储笔记本的详细信息，请参阅 [Where are Jupyter notebooks stored？](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)（Jupyter 笔记本存储在何处？）
* 使用本地提供的笔记本可以根据应用程序要求连接到不同的 Spark 群集。
* 你可以使用 GitHub 实施源代码管理系统，并对笔记本进行版本控制。 此外，还可以建立一个协作环境，其中的多个用户可以使用同一个笔记本。
* 你甚至不需要启动群集就能在本地使用笔记本。 只需创建一个群集以根据它来测试你的笔记本，而不需要手动管理笔记本或开发环境。
* 配置自己的本地开发环境比在群集上配置 Jupyter 安装更容易。  你可以利用本地安装的所有软件，而不需要配置一个或多个远程群集。

> [!WARNING]
> 在本地计算机上安装 Jupyter 后，多个用户可以同时在同一个 Spark 群集上运行同一个笔记本。 在这种情况下，将会创建多个 Livy 会话。 如果你遇到问题并想要调试，则跟踪哪个 Livy 会话属于哪个用户将是一项复杂的任务。
>
>

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)

