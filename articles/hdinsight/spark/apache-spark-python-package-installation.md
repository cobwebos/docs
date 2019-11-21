---
title: Script action for Python packages with Jupyter on Azure HDInsight
description: 逐步说明如何使用脚本操作配置可在 HDInsight Spark 群集中使用的 Jupyter 笔记本，以使用外部 python 包。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: a8654f6c9c6c6d020872d2c89e0dd141db4e0451
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215559"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Safely manage Python environment on Azure HDInsight using Script Action

> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)

HDInsight has two built-in Python installations in the Spark cluster, Anaconda Python 2.7 and Python 3.5. In some cases, customers need to customize the Python environment, like installing external Python packages or another Python version. In this article, we show the best practice of safely managing Python environments for an [Apache Spark](https://spark.apache.org/) cluster on HDInsight.

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

   > [!NOTE]  
   > 如果 HDInsight Linux 上还没有 Spark 群集，则可以在群集创建过程中运行脚本操作。 访问有关[如何使用自定义脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)的文档。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 构建的开源技术生态系统。 Microsoft Azure 为开源技术提供常规级别的支持。 For more information, see [Azure Support FAQ website](https://azure.microsoft.com/support/faq/). HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

* **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。 For example, Apache Hadoop YARN Resource Manager, the Apache Hive query language (HiveQL), and the Mahout library belong to this category. [HDInsight 提供的 Apache Hadoop 群集版本的新增功能](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)中提供了群集组件的完整列表。
* **自定义组件** - 作为群集用户，可以安装，或者在工作负荷中使用由社区提供的或自己创建的任何组件。

> [!IMPORTANT]
> HDInsight 群集提供的组件受到完全支持。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [https://stackoverflow.com](https://stackoverflow.com)。 Also Apache projects have project sites on [https://apache.org](https://apache.org), for example: [Hadoop](https://hadoop.apache.org/).

## <a name="understand-default-python-installation"></a>Understand default Python installation

HDInsight Spark cluster is created with Anaconda installation. There are two Python installations in the cluster, Anaconda Python 2.7 and Python 3.5. The table below shows the default Python settings for Spark, Livy, and Jupyter.

| |Python 2.7|Python 3.5|
|----|----|----|
|路径|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|Default set to 2.7|N/A|
|Livy|Default set to 2.7|N/A|
|Jupyter|PySpark kernel|PySpark3 kernel|

## <a name="safely-install-external-python-packages"></a>Safely install external Python packages

HDInsight cluster depends on the built-in Python environment, both Python 2.7 and Python 3.5. Directly installing custom packages in those default built-in environments may cause unexpected library version changes, and break the cluster further. In order to safely install custom external Python packages for your Spark applications, follow below steps.

1. Create Python virtual environment using conda. A virtual environment provides an isolated space for your projects without breaking others. When creating the Python virtual environment, you can specify python version that you want to use. Note that you still need to create virtual environment even though you would like to use Python 2.7 and 3.5. This is to make sure the cluster’s default environment not getting broke. Run script actions on your cluster for all nodes with below script to create a Python virtual environment. 

    -   `--prefix` specifies a path where a conda virtual environment lives. There are several configs that need to be changed further based on the path specified here. In this example, we use the py35new, as the cluster has an existing virtual environment called py35 already.
    -   `python=` specifies the Python version for the virtual environment. In this example, we use version 3.5, the same version as the cluster built in one. You can also use other Python versions to create the virtual environment.
    -   `anaconda` specifies the package_spec as anaconda to install Anaconda packages in the virtual environment.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes 
    ```

2. Install external Python packages in the created virtual environment if needed. Run script actions on your cluster for all nodes with below script to install external Python packages. You need to have sudo privilege here in order to write files to the virtual environment folder.

    可以在[包索引](https://pypi.python.org/pypi)中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，可以安装通过 [conda-forge](https://conda-forge.org/feedstocks/) 提供的包。

    -   `seaborn` is the package name that you would like to install.
    -   `-n py35new` specify the virtual environment name that just gets created. Make sure to change the name correspondingly based on your virtual environment creation.

    ```bash
    sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
    ```

    if you don't know the virtual environment name, you can SSH to the header node of the cluster and run `/usr/bin/anaconda/bin/conda info -e` to show all virtual environments.

3. Change Spark and Livy configs and point to the created virtual environment.

    1. Open Ambari UI, go to Spark2 page, Configs tab.
    
        ![Change Spark and Livy config through Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)
 
    2. Expand Advanced livy2-env, add below statements at bottom. If you installed the virtual environment with a different prefix, change the path correspondingly.

        ```
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Change Livy config through Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expand Advanced spark2-env, replace the existing export PYSPARK_PYTHON statement at bottom. If you installed the virtual environment with a different prefix, change the path correspondingly.

        ```
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Change Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Save the changes and restart affected services. These changes need a restart of Spark2 service. Ambari UI will prompt a required restart reminder, click Restart to restart all affected services.

        ![Change Spark config through Ambari](./media/apache-spark-python-package-installation/ambari-restart-services.png)
 
4.  If you would like to use the new created virtual environment on Jupyter. You need to change Jupyter configs and restart Jupyter. Run script actions on all header nodes with below statement to point Jupyter to the new created virtual environment. Make sure to modify the path to the prefix you specified for your virtual environment. After running this script action, restart Jupyter service through Ambari UI to make this change available.

    ```
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    You could double confirm the Python environment in Jupyter Notebook by running below code:

    ![Check Python version in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知问题

There is a known bug for Anaconda version 4.7.11 and 4.7.12. If you see your script actions hanging at `"Collecting package metadata (repodata.json): ...working..."` and failing with `"Python script has been killed due to timeout after waiting 3600 secs"`. You can download [this script](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) and run it as script actions on all nodes to fix the issue.

To check your Anaconda version, you can SSH to the cluster header node and run `/usr/bin/anaconda/bin/conda --v`.

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
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Apache Spark applications remotely（使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序）](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
