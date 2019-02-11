---
title: Azure HDInsight 工具 - 为 Visual Studio Code 设置 PySpark 交互式环境
description: 了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,交互式 Hive,交互式查询
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 45ba049717f2b9874bbac8d6493e13c2afc4b8f2
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910642"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>为 Visual Studio Code 设置 PySpark 交互式环境

以下步骤显示如何在 VS Code 中设置 PySpark 交互环境。

我们使用 python/pip 命令在 Home 路径中生成虚拟环境。 如果要使用其他版本，则需要手动更改默认版的 python/pip 命令。 有关详细信息，请参阅[更新替代项](https://linux.die.net/man/8/update-alternatives)。

1. 安装 [Python](https://www.python.org/downloads/) 和 [pip](https://pip.pypa.io/en/stable/installing/).
   
   + 从 [https://www.python.org/downloads/](https://www.python.org/downloads/) 安装 Python。
   + 从 [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) 安装 pip。 （如果未从“Python 安装”进行安装）
   + 使用以下命令验证 Python 和 pip 是否成功安装。 (可选)
 
        ![Python pip 版本](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

    > [!NOTE]
    > 建议手动而不是使用 MacOS 默认版本安装 Python。


2. 通过运行以下命令，安装 virtualenv。
   
   ```
   pip install virtualenv
   ```

3. 仅对于 Linux，如果遇到错误消息，请通过运行以下命令安装所需的包。
   
    ![Python pip 版本](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. 重启 VS Code 并回到运行以下命令的脚本编辑器：“HDInsight: PySpark Interactive”。

## <a name="next-steps"></a>后续步骤

### <a name="demo"></a>演示
* 用于 VS Code 的 HDInsight：[视频](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用 Azure Toolkit for IntelliJ 创建和提交 Apache Spark Scala 应用程序](spark/apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Apache Spark 应用程序](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](spark/apache-spark-eclipse-tool-plugin.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](spark/apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](spark/apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](hdinsight-connect-hive-zeppelin.md)
