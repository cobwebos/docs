---
title: Azure HDInsight 上的 Jupyter 的 Python 包的脚本操作
description: 逐步说明如何使用脚本操作配置可在 HDInsight Spark 群集中使用的 Jupyter 笔记本，以使用外部 python 包。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 98326d23f5aca1264bc47168cc25b427c3db331d
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135949"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>使用脚本操作在 Azure HDInsight 上安全管理 Python 环境

> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)

HDInsight 在 Spark 群集中有两个内置的 Python 安装，Anaconda Python 2.7 和 Python 3.5。 在某些情况下，客户需要自定义 Python 环境，如安装外部 Python 包或其他 Python 版本。 本文介绍如何安全地管理 HDInsight 上的[Apache Spark](https://spark.apache.org/)群集的 Python 环境。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

   > [!NOTE]  
   > 如果 HDInsight Linux 上还没有 Spark 群集，则可以在群集创建过程中运行脚本操作。 访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅[Azure 支持常见问题网站](https://azure.microsoft.com/support/faq/)。 HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

* **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 例如，Apache Hadoop YARN 资源管理器，则 Apache Hive 查询语言（HiveQL）和 Mahout 库属于此类别。 [HDInsight 提供的 Apache Hadoop 群集版本的新增功能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)中提供了群集组件的完整列表。
* **自定义组件** - 作为群集用户，可以安装，或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!IMPORTANT]
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 项目在 [https://apache.org](https://apache.org) 上提供了项目站点，例如 [Hadoop](https://hadoop.apache.org/)。

## <a name="understand-default-python-installation"></a>了解默认 Python 安装

HDInsight Spark 群集是通过 Anaconda 安装创建的。 群集中有两个 Python 安装，Anaconda Python 2.7 和 Python 3.5。 下表显示了 Spark、Livy 和 Jupyter 的默认 Python 设置。

| |Python 2.7|Python 3.5|
|----|----|----|
|路径|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|默认设置为2。7|空值|
|Livy|默认设置为2。7|空值|
|Jupyter|PySpark 内核|PySpark3 内核|

## <a name="safely-install-external-python-packages"></a>安全安装外部 Python 包

HDInsight 群集依赖于内置的 Python 环境，即 Python 2.7 和 Python 3.5。 直接在这些默认的内置环境中安装自定义包可能会导致意外的库版本更改，并进一步打破群集。 若要为 Spark 应用程序安全安装自定义外部 Python 包，请执行以下步骤。

1. 使用 conda 创建 Python 虚拟环境。 虚拟环境为项目提供隔离空间，而不会中断其他用户。 创建 Python 虚拟环境时，可以指定要使用的 python 版本。 请注意，即使要使用 Python 2.7 和3.5，仍需要创建虚拟环境。 这是为了确保群集的默认环境不会中断。 使用以下脚本在群集上运行脚本操作，以创建 Python 虚拟环境。 

    -   `--prefix` 指定 conda 虚拟环境所在的路径。 需要根据此处指定的路径进一步更改某些配置。 在此示例中，我们使用 py35new，因为群集已有一个名为 py35 的现有虚拟环境。
    -   `python=` 指定虚拟环境的 Python 版本。 在此示例中，我们使用版本3.5，该版本与内置群集的版本相同。 你还可以使用其他 Python 版本来创建虚拟环境。
    -   `anaconda` 将 package_spec 指定为 anaconda，以便在虚拟环境中安装 Anaconda 包。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. 如果需要，请在创建的虚拟环境中安装外部 Python 包。 在群集上对具有以下脚本的所有节点运行脚本操作以安装外部 Python 包。 若要将文件写入虚拟环境文件夹，需要在此处具有 sudo 权限。

    可以在[包索引](https://pypi.python.org/pypi)中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，可以安装通过 [conda-forge](https://conda-forge.org/feedstocks/) 提供的包。

    如果要安装具有最新版本的库，请使用以下命令：
    
    - 使用 conda 通道：

        -   `seaborn` 是要安装的包名称。
        -   `-n py35new` 指定刚创建的虚拟环境名称。 请确保根据虚拟环境创建相应地更改名称。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 或者，使用 PyPi 存储库，相应地更改 `seaborn` 和 `py35new`：
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```        

    如果要安装具有特定版本的库，请使用以下命令：

    - 使用 conda 通道：

        -   `numpy=1.16.1` 是要安装的包名称和版本。
        -   `-n py35new` 指定刚创建的虚拟环境名称。 请确保根据虚拟环境创建相应地更改名称。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 或者，使用 PyPi 存储库，相应地更改 `numpy==1.16.1` 和 `py35new`：

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    如果不知道虚拟环境名称，可以通过 SSH 连接到群集的头节点并运行 `/usr/bin/anaconda/bin/conda info -e` 以显示所有虚拟环境。

3. 更改 Spark 和 Livy 配置，并指向创建的虚拟环境。

    1. 打开 Ambari UI，中转到 Custom-spark2-defaults 页，"配置" 选项卡。
    
        ![通过 Ambari 更改 Spark 和 Livy config](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. 展开 Advanced livy2-env，将下面的语句添加到底部。 如果使用不同的前缀安装虚拟环境，请相应地更改路径。

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![通过 Ambari 更改 Livy config](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 展开 Advanced custom-spark2-defaults-env，替换底部的现有 export PYSPARK_PYTHON 语句。 如果使用不同的前缀安装虚拟环境，请相应地更改路径。

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![通过 Ambari 更改 Spark 配置](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 保存更改并重新启动受影响的服务。 这些更改需要重新启动 Custom-spark2-defaults 服务。 Ambari UI 将提示需要重新启动提醒，单击 "重新启动" 以重新启动所有受影响的服务。

        ![通过 Ambari 更改 Spark 配置](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  如果要在 Jupyter 上使用新创建的虚拟环境。 需要更改 Jupyter 配置并重新启动 Jupyter。 在包含以下语句的所有标头节点上运行脚本操作，使 Jupyter 指向新创建的虚拟环境。 请确保修改为虚拟环境指定的前缀的路径。 运行此脚本操作后，通过 Ambari UI 重启 Jupyter 服务，以使此更改可用。

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    可以通过运行以下代码，在 Jupyter Notebook 中加倍确认 Python 环境：

    ![在 Jupyter Notebook 中检查 Python 版本](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知问题

Anaconda 版本4.7.11、4.7.12 和4.8.0 有一个已知的 bug。 如果你的脚本操作在 `"Collecting package metadata (repodata.json): ...working..."` 挂起，但未能通过 `"Python script has been killed due to timeout after waiting 3600 secs"`。 您可以下载[此脚本](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)并将其作为脚本操作运行，以解决此问题。

若要检查 Anaconda 版本，可以通过 SSH 连接到群集标头节点并运行 `/usr/bin/anaconda/bin/conda --v`。

## <a name="seealso"></a>另请参阅

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案

* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展

* [在 HDInsight 上的 Apache Spark 群集中将外部包与 Jupyter 笔记本配合使用](apache-spark-jupyter-notebook-use-external-packages.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
