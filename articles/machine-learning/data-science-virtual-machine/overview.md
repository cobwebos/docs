---
title: 适用于 Linux 和 Windows 的 Azure 数据科学虚拟机简介 | Microsoft Docs
description: 适用于 Windows 和 Linux 数据科学虚拟机的关键分析方案和组件。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 10/27/2017
ms.author: gokuma
ms.openlocfilehash: 96105931bca8c0e75fb89552eb91af47bd860e3a
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136700"
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>适用于 Linux 和 Windows 的 Azure 数据科学虚拟机简介

数据科学虚拟机 (DSVM) 是专为研究数据科学生成的 Microsoft Azure 云上的自定义 VM 映像。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 它在 Windows Server 和 Linux 上可用。 我们在 Server 2016 和 Server 2012 上提供了 Windows 版本的 DSVM。 我们在 Ubuntu 16.04 LTS 和 CentOS 7.4 上提供了 Linux 版本的 DSVM。

本主题讨论可对数据科学 VM 执行的操作、概述了使用 VM 的一些关键方案、详细列举了 Windows 和 Linux 版本上可用的关键功能，并说明了如何使用它们。


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我如何使用数据科学虚拟机？
数据科学虚拟机 (DSVM) 的目标在于向所有技能级别和所有角色的数据专业人员提供无摩擦、预配置且完全集成的数据科学环境。 可以预配 DSVM，而不是自己推出可比较的工作区 - 在安装、 配置和包管理过程中节省天数甚至_周_数。 分配 DSVM 后，可以立即开始处理数据科学项目。

数据科学 VM 的设计和配置意图在于与广泛的使用方案一起使用。 随着项目需求变化，可扩展或缩小环境。 可使用首选语言为数据科学任务编写程序。 可根据具体需要安装其他工具和自定义系统。

## <a name="key-scenarios"></a>关键方案
本部分提示一些可部署数据科学 VM 的关键方案。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在云中预配分析桌面
数据科学 VM 为寻求使用托管的云桌面替换本地桌面的数据科学团队提供了基线配置。 此基线确保团队中的所有数据科学家具有可验证实验并促进协作的一致设置。 通过减少 sysadmin 负担并节省评估、安装和维护高级分析所需各种程序包所需的时间，它还降低了成本。  

### <a name="data-science-training-and-education"></a>数据科学训练和培训
教授数据科学课程的企业培训师和教师通常会提供虚拟机映像，确保学生设置一致，并可以预测示例运行情况。 数据科学 VM 创建可缓解支持和不兼容性挑战的一致设置的按需环境。 这些环境需要频繁生成，特别是短期培训课程的情况从中获益极大。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>对于大型项目的按需弹性容量
数据科学编程马拉松/竞赛或大型数据建模和浏览需要扩展的硬件容量，通常持续时间比较短。 数据科学 VM 可有助于在支持需要运行高性能计算资源的扩展服务器上按照需要快速复制数据科学环境。

### <a name="short-term-experimentation-and-evaluation"></a>短期实验和评估
数据科学 VM 可用于评估或学习工具，例如 Microsoft ML Server、SQL Server、Visual Studio 工具、Jupyter、深度学习/ML 工具包以及社区热门且需要最少设置工作量的新工具。 因为数据科学 VM 可快速设置，所以可应用于其他短期使用方案，例如复制发布的实验、执行演示、遵循在线会话或会议教程中的演练。

### <a name="deep-learning"></a>深度学习
数据科学 VM 可用于在基于 GPU（图形处理单元）的硬件上使用深度学习算法的训练模型。 利用 Azure 云的 VM 缩放功能，DSVM 可帮助根据需要在云上使用基于 GPU 的硬件。 如果训练大型模型或在保留相同 OS 磁盘的同时需要高速计算，可以切换到基于 GPU 的 VM。  Windows Server 2016 版本的 DSVM 预安装了 GPU 驱动程序、框架和 GPU 版本的深度学习框架。 在 Linux 上，在 CentOS 和 Ubuntu DSVM 中都启用了 GPU 上的深度学习。 在所有深度学习框架都将回退到 CPU 模式的情况下，可以将 Ubuntu、CentOS 或 Windows 2016 版本的数据科学 VM 部署到非基于 GPU 的 Azure 虚拟机。 

## <a name="whats-included-in-the-data-science-vm"></a>数据科学 VM 中包含什么内容？
数据科学虚拟机已安装并配置了许多热门数据科学工具和深度学习工具。 它还包括简化使用各种 Azure 数据和分析产品的工具。 可使用 Microsoft ML Server（R、Python）或 SQL Server 2017 在大型数据集上浏览和生成预测模型。 还包括开源社区和 Microsoft 的其他工具主机，示例代码和笔记本也包括在内。 下表详细列举并比较了包括在数据科学虚拟机 Windows 和 Linux 版中的主要组件。


| **工具**                                                           | **Windows 版** | **Linux 版** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 预安装了常用包的 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft ML Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) 开发者版包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 并行分布式高性能框架（R 和 Python）<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) - Microsoft 提供的最先进的全新 ML 算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R 和 Python 操作化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) 专业且具有共享激活功能 - Excel、Word 和 PowerPoint   |Y                      |N              |
| 预安装了常用包的 [Anaconda Python](https://www.continuum.io/) 2.7、3.5    |Y                      |Y              |
| 预安装了具有 Julia 语言的常用包的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 关系数据库                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| 数据库工具                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC 驱动程序| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)（查询工具）、 <br /> * bcp、sqlcmd <br /> * ODBC/JDBC 驱动程序|
| 在带有 SQL Server ML 服务（R、Python）的数据库内分析中可扩展 | Y     |N              |
| **带有以下内核的 [Jupyter Notebook Server](http://jupyter.org/)：**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | 是（仅适用于 Ubuntu） |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub（多用户笔记本服务器）| N | Y |
| JupyterLab（多用户笔记本服务器） | N | 是（仅适用于 Ubuntu） |
| **开发工具、IDE 和代码编辑器**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017（社区版）](https://www.visualstudio.com/community/)，包括 Git 插件、Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](http://aka.ms/ptvs) 和[针对 Visual Studio 的 R 工具 (RTVS)](http://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (Julia IDE)](http://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim 和 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git 和 GitBash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | Y | N |
| PowerBI Desktop | Y | N |
| 访问服务的 Azure 和 Cortana Intelligence Suite 的 SDK | Y | Y |
| **数据移动和管理工具** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure 存储资源管理器 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Blob FUSE 驱动程序](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB 数据迁移工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft 数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)：在本地和云之间移动数据 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux 命令行实用程序 | Y | Y |
| 用于数据浏览的 [Apache Drill](http://drill.apache.org) | Y | Y |
| **机器学习工具** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* 与 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)集成（R、Python） | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | 是（仅适用于 Ubuntu） |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/)、[Deep Water](https://www.h2o.ai/deep-water/) | N | 是（仅适用于 Ubuntu） |
| **深度学习工具** <br>所有工具均可在 GPU 或 CPU 上正常工作 |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe 和 Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyTorch](http://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet 模型服务器](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow 服务](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA、CUDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **大数据平台（仅限 Devtest）**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* 本地独立 [Spark](http://spark.apache.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* 本地 [Hadoop](http://hadoop.apache.org/)（HDFS、YARN） | N | Y |

## <a name="get-started"></a>入门

### <a name="windows-data-science-vm"></a>Windows 数据科学 VM
* 有关如何创建 Windows DSVM 并使用它的详细信息，请参阅[预配 Windows 数据科学虚拟机](provision-vm.md)。 有关如何在 Windows DSVM 上执行数据科学项目所需的各种任务的详细信息，请参阅[数据科学虚拟机的十大功能](vm-do-ten-things.md)。

### <a name="linux-data-science-vm"></a>Linux 数据科学 VM
* 有关如何创建 Ubuntu DSVM 并使用它的详细信息，请参阅[预配适用于 Linux (Ubuntu) 的数据科学虚拟机](dsvm-ubuntu-intro.md)。 有关如何创建 CentOS DSVM 并使用它的详细信息，请参阅[在 Azure 上预配 Linux CentOS 数据科学虚拟机](linux-dsvm-intro.md)。
* 有关介绍如何通过 Linux VM（CentOS 和 Ubuntu）执行多个常见数据科学任务的演示，请参阅 [Linux 数据科学虚拟机上的数据科学](linux-dsvm-walkthrough.md)。

