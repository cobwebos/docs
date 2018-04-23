---
title: Azure HDInsight 工具 - 为 Visual Studio Code 设置 PySpark 交互式环境 | Microsoft Docs
description: 了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>为 Visual Studio Code 设置 PySpark 交互式环境

以下步骤显示如何通过运行“HDInsight: PySpark Interactive”安装 Python 包。


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>在 macOS 和 Linux 上设置 PySpark 交互式环境
如果使用 **python 3.x**，需要将命令 **pip3** 用于以下步骤：

1. 请确保已安装“Python”和“pip”。
 
    ![Python pip 版本](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  安装 Jupyter。
    ```
    sudo pip install jupyter
    ```
   Linux 和 macOS 上可能会显示以下错误消息：

   ![错误 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. 安装 **libkrb5-dev**（仅适用于 Linux）。 可能会显示以下错误消息：

   ![错误 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. 安装 **sparkmagic**。
   ```
   sudo pip install sparkmagic
   ```

4. 通过运行以下命令，确保 **ipywidgets** 已正确安装：
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![安装包装器内核](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. 安装包装器内核。 运行 **pip show sparkmagic**。 输出显示 **sparkmagic** 安装的路径。 

    ![sparkmagic 位置](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. 转到该位置并运行：

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![jupyter kernelspec 安装](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. 检查安装状态。

    ```
    jupyter-kernelspec list
    ```
    ![jupyter kernelspec 列表](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    对于可用内核： 
    - **python2** 和 **pysparkkernel** 对应于 **python 2.x**。 
    - **python3** 和 **pyspark3kernel** 对应于 **python 3.x**。 

8. 重新启动 VS Code 并回到运行“HDInsight: PySpark Interactive”的脚本编辑器。

## <a name="next-steps"></a>后续步骤

### <a name="demo"></a>演示
* 用于 VS Code 的 HDInsight：[视频](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 Visual Studio Code 的 Azure HDInsight 工具](hdinsight-for-vscode.md)
* [使用用于 IntelliJ 的 Azure 工具包创建和提交 Spark Scala 应用程序](spark/apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](spark/apache-spark-eclipse-tool-plugin.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](spark/apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](spark/apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 可视化 Hive 数据](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](hdinsight-connect-hive-zeppelin.md)
