---
title: 用于 Azure HDInsight 上包含 Jupyter 的 Python 包的脚本操作
description: 逐步说明如何使用脚本操作配置 HDInsight Spark 群集随附的 Jupyter notebook，以使用外部 python 包。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 13ea1043d05c9f349e25623086c2908e176772a8
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583951"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>使用脚本操作在 Azure HDInsight 上安全管理 Python 环境

> [!div class="op_single_selector"]
> * [使用单元格 magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [使用脚本操作](apache-spark-python-package-installation.md)

HDInsight 在 Spark 群集中包含两个内置的 Python 安装：Anaconda Python 2.7 和 Python 3.5。 客户可能需要自定义 Python 环境。 例如安装外部 Python 包或其他 Python 版本。 在这里，我们将介绍安全管理 HDInsight 上 Apache Spark 群集的 Python 环境的最佳做法。

## <a name="prerequisites"></a>先决条件

HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。 如果 HDInsight 上还没有 Spark 群集，可以在创建群集期间运行脚本操作。 访问有关[如何使用自定义脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)的文档。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>支持 HDInsight 群集上使用的开源软件

Microsoft Azure HDInsight 服务使用围绕 Apache Hadoop 形成的开源技术的环境。 Microsoft Azure 为开源技术提供常规级别的支持。 有关详细信息，请参阅 [Azure 支持常见问题解答网站](https://azure.microsoft.com/support/faq/)。 HDInsight 服务为内置组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

|组件 |说明 |
|---|---|
|内置|这些组件已预先安装在 HDInsight 群集上，并提供群集的核心功能。 例如，Apache Hadoop YARN 资源管理器、Apache Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。 [HDInsight 提供的 Apache Hadoop 群集版本的新增功能](../hdinsight-component-versioning.md)中提供了群集组件的完整列表。|
|“自定义”|群集用户可以安装或者在工作负荷中使用由社区提供的或自己创建的任何组件。|

> [!IMPORTANT]
> 完全支持通过 HDInsight 群集提供的组件。 Microsoft 支持部门可帮助找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，有助于进一步解决问题。 Microsoft 支持部门也许能够解决问题，也可能要求你参与可用的开放源代码技术渠道，获取该技术的深入专业知识。 有许多可以使用的社区站点，例如：[面向 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)、`https://stackoverflow.com`。 此外，Apache 项目在上`https://apache.org`有项目站点。

## <a name="understand-default-python-installation"></a>了解默认的 Python 安装

HDInsight Spark 群集是通过 Anaconda 安装创建的。 群集中有两个 Python 安装：Anaconda Python 2.7 和 Python 3.5。 下表显示了 Spark、Livy 和 Jupyter 的默认 Python 设置。

| |Python 2.7|Python 3.5|
|----|----|----|
|`Path`|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark|默认设置为 2.7|不适用|
|Livy|默认设置为 2.7|不适用|
|Jupyter|PySpark 内核|PySpark3 内核|

## <a name="safely-install-external-python-packages"></a>安全安装外部 Python 包

HDInsight 群集依赖于内置的 Python 环境，即 Python 2.7 和 Python 3.5。 在这些默认的内置环境中直接安装自定义包可能会导致意外的库版本更改。 并进一步打破群集。 若要为 Spark 应用程序安全安装自定义外部 Python 包，请执行以下步骤。

1. 使用 conda 创建 Python 虚拟环境。 虚拟环境为你的项目提供隔离的空间，且不会破坏其他项目。 创建 Python 虚拟环境时，可以指定要使用的 Python 版本。 即使要使用 Python 2.7 和3.5，仍需要创建虚拟环境。 此要求用于确保群集的默认环境不会中断。 使用以下脚本针对群集上的所有节点运行脚本操作，以创建 Python 虚拟环境。

    -   `--prefix` 指定 conda 虚拟环境所在的路径。 需要根据此处指定的路径进一步更改几项配置。 本示例使用 py35new，因为群集中已包含名为 py35 的现有虚拟环境。
    -   `python=` 指定虚拟环境的 Python 版本。 本示例使用版本 3.5，即群集的内置版本。 也可以使用其他 Python 版本来创建虚拟环境。
    -   `anaconda` 将 package_spec 指定为 anaconda，以在虚拟环境中安装 Anaconda 包。
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. 根据需要在创建的虚拟环境中安装外部 Python 包。 使用以下脚本针对群集上的所有节点运行脚本操作，以安装外部 Python 包。 此处需要有 sudo 权限，才能将文件写入虚拟环境文件夹。

    在[包索引](https://pypi.python.org/pypi)中搜索可用包的完整列表。 也可以从其他源获取可用包的列表。 例如，可以安装通过 [conda-forge](https://conda-forge.org/feedstocks/) 提供的包。

    如果要安装其最新版本的库，请使用以下命令：

    - 使用 conda 通道：

        -   `seaborn` 要安装的包名称。
        -   `-n py35new` 指定刚刚创建的虚拟环境名称。 请务必根据虚拟环境创建过程相应地更改此名称。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - 或者使用 PyPi 存储库，请相应地更改 `seaborn` 和 `py35new`：
        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install seaborn
        ```

    如果要安装特定版本的库，请使用以下命令：

    - 使用 conda 通道：

        -   `numpy=1.16.1` 是要安装的包名称和版本。
        -   `-n py35new` 指定刚刚创建的虚拟环境名称。 请务必根据虚拟环境创建过程相应地更改此名称。

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - 或者使用 PyPi 存储库，请相应地更改 `numpy==1.16.1` 和 `py35new`：

        ```bash
        sudo /usr/bin/anaconda/env/py35new/bin/pip install numpy==1.16.1
        ```

    如果你不知道虚拟环境名称，可以通过 SSH 连接到群集的头节点，然后运行 `/usr/bin/anaconda/bin/conda info -e` 以显示所有虚拟环境。

3. 更改 Spark 和 Livy 配置，并指向创建的虚拟环境。

    1. 打开 Ambari UI，转到“Spark2”页中的“配置”选项卡。

        ![通过 Ambari 更改 Spark 和 Livy 配置](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. 展开“高级 livy2-env”，在底部添加以下语句。 如果使用不同的前缀安装了虚拟环境，请相应地更改路径。

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![通过 Ambari 更改 Livy 配置](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. 展开“高级 spark2-env”，替换底部的现有 export PYSPARK_PYTHON 语句。 如果使用不同的前缀安装了虚拟环境，请相应地更改路径。

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![通过 Ambari 更改 Spark 配置](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. 保存更改并重启受影响的服务。 需要重启 Spark2 服务才能使这些更改生效。 Ambari UI 将提示需要重启。单击“重启”以重启所有受影响的服务。

        ![通过 Ambari 更改 Spark 配置](./media/apache-spark-python-package-installation/ambari-restart-services.png)

4. 若要在 Jupyter 上使用新建的虚拟环境， 更改 Jupyter 配置，然后重新启动 Jupyter。 使用以下语句针对所有头节点运行脚本操作，使 Jupyter 指向新建的虚拟环境。 请务必修改针对虚拟环境指定的前缀的路径。 运行此脚本操作后，通过 Ambari UI 重启 Jupyter 服务，使此项更改生效。

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    可以通过运行以下代码，在 Jupyter Notebook 中反复确认 Python 环境：

    ![在 Jupyter Notebook 中检查 Python 版本](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>已知问题

Anaconda 版本`4.7.11`、 `4.7.12`和`4.8.0`有一个已知的 bug。 如果发现脚本操作在执行 `"Collecting package metadata (repodata.json): ...working..."` 时挂起，同时失败并显示 `"Python script has been killed due to timeout after waiting 3600 secs"`， 可以下载[此脚本](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh)，并在所有节点上将其作为脚本操作运行，这样即可解决此问题。

若要检查 Anaconda 版本，可以通过 SSH 连接到群集头节点并运行 `/usr/bin/anaconda/bin/conda --v`。

## <a name="next-steps"></a>后续步骤

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)
* [Apache Spark 中包含 Jupyter 笔记本的外部包](apache-spark-jupyter-notebook-use-external-packages.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
