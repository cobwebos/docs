---
title: 如何使用 MMLSpark 机器学习 | Microsoft Docs
description: 指导如何将 MMLSpark 库与 Azure 机器学习搭配使用。
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 253f58721b709b044069c388521daf351264f74d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>如何使用适用于 Apache Spark 的 Microsoft 机器学习库

## <a name="introduction"></a>介绍

[适用于 Apache Spark 的 Microsoft 机器学习库](https://github.com/Azure/mmlspark) (MMLSpark) 提供一些工具，可为大型数据集轻松创建可缩放的机器学习模型。 它将 SparkML 管道与 [Microsoft 认知工具包](https://github.com/Microsoft/CNTK)和 [OpenCV](http://www.opencv.org/) 相集成，允许： 
 * 流入和预处理图像数据
 * 使用预先定型的深度学习模型特征化图像和文本
 * 使用隐式特征化对分类和回归模型进行定型和评分。

## <a name="prerequisites"></a>先决条件

若要逐步执行本操作方法指南，需要：
- [安装 Azure Machine Learning Workbench](../service/quickstart-installation.md)
- [设置 Azure HDInsight Spark 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>在 Docker 容器中运行试验

在本地或远程 VM 中的 Docker 容器中运行试验时，Azure Machine Learning Workbench 配置为使用 MMLSpark。 在群集上大规模运行 PySpark 模型之前，可利用此功能轻松调试和试验这些模型。 

若要使用示例开始操作，请创建一个新项目，然后选择“MMLSpark on Adult Census”库示例。 从项目仪表板中选择“Docker”作为计算上下文，单击“运行”。 Azure Machine Learning Workbench 生成用于运行 PySpark 程序的 Docker 容器，然后执行该程序。

运行完毕后，可以在 Azure Machine Learning Workbench 的运行历史记录视图中查看结果。

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>在 Azure HDInsight Spark 群集上安装 MMLSpark。

若要完成这一步和下一步，需要先[创建 Azure HDInsight Spark 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)。

运行试验时，Azure Machine Learning Workbench 会在群集上默认安装 MMLSpark 包。 用户可以控制此行为，通过在项目文件夹中编辑名为 _aml_config/spark_dependencies.yml_ 的文件来安装其他 Spark 包。

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

也可以使用[脚本操作](https://github.com/Azure/mmlspark#hdinsight)直接在 HDInsight Spark 群集上安装 MMLSpark。

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>将 Azure HDInsight Spark 群集设置为计算目标

从 Azure Machine Learning Workbench 打开 CLI 窗口，方式如下：转到“文件”菜单，单击“打开命令提示符”

在 CLI 窗口中运行以下命令：

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

该群集现在即可用作项目的计算目标。

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>在 Azure HDInsight Spark 群集上运行试验。

返回到“MMLSpark on Adult Census”示例的项目仪表板。 选择群集作为计算目标，并单击“运行”。

Azure Machine Learning Workbench 将 spark 作业提交到群集。 用户可以监视进度，在运行历史记录视图中查看结果。

## <a name="next-steps"></a>后续步骤
有关 MMLSpark 库的信息和示例，请参阅 [MMLSpark GitHub 存储库](https://github.com/Azure/mmlspark)

*Apache®、Apache Spark 和 Spark® 是 Apache Software Foundation 在美国和/或其他国家/地区的商标或注册商标。*
