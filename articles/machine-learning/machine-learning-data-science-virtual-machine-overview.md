---
title: "什么是数据科学虚拟机？ | Microsoft 文档"
description: "了解关键方案、功能以及如何开始使用可用于分析的环境和工具包数据科学虚拟机。"
keywords: "数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: gokuma
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 77b4f634c2a30d0cbec92d34a7f83866541d7d84
ms.lasthandoff: 04/15/2017


---
# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>适用于 Linux 和 Windows 的基于云的数据科学虚拟机简介
数据科学虚拟机 (DSVM) 是专为研究数据科学生成的 Microsoft Azure 云上的自定义 VM 映像。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 它在 Windows Server 2012 和 Linux 上可用。 我们在 Ubuntu 16.04 LTS 中或基于 OpenLogic 7.2 CentOS 的 Linux 发行版上提供了 Linux 版本的 DSVM。 

本主题讨论可对数据科学 VM 执行的操作、概述了使用 VM 的一些关键方案、详细列举了 Windows 和 Linux 版本上可用的关键功能，并说明了如何使用它们。

## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>我如何使用数据科学虚拟机？
数据科学虚拟机的目的在于向所有技能级别和角色的数据专业人员提供无摩擦的数据科学环境。 如果已自行推出可比较的环境，此 VM 可节省大量时间。 在新创建的 VM 实例中可立即启动数据科学项目。 

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
数据科学 VM 可用于评估或学习工具，例如 Microsoft R Server、SQL Server、Visual Studio 工具、Jupyter、深度学习 / ML 工具包以及社区热门且需要最少设置量的新工具。 因为数据科学 VM 可快速设置，所以可应用于其他短期使用方案，例如复制发布的实验、执行演示、遵循在线会话或会议教程中的演练。

### <a name="deep-learning"></a>深度学习
数据科学 VM 可用于在基于 GPU（图形处理单元）的硬件上使用深度学习算法的训练模型。 当需要训练大型模型或者需要高速计算来利用 GPU 的强大功能时，DSVM 可根据需要帮助仅在云上使用基于 GPU 的硬件。  在 Windows 上，我们当前提供 [DSVM 的深入学习工具包](http://aka.ms/dsvm/deeplearning)作为位于 DSVM 顶部的单独加载项。 在创建 VM 实例时，此加载项会自动安装 GPU 驱动程序、框架和 GPU 版本的深度学习算法。 在 Linux 上，仅在 [Linux (Ubuntu) 版本的数据科学虚拟机](http://aka.ms/dsvm/ubuntu)上支持 GPU 的深度学习。 在所有深度学习框架都将回退到 CPU 模式的情况下，可以将 Ubuntu 版本的数据科学 VM 部署到基于非 GPU 的 Azure 虚拟机。 基于 CentOS 的 DSVM Linux 版只包含一些深度学习工具（CNTK、Tensorflow、MXNet）的 CPU 生成，但未预安装 GPU 驱动程序和框架。 

## <a name="whats-included-in-the-data-science-vm"></a>数据科学 VM 中包含什么内容？
数据科学虚拟机已安装并配置了许多热门数据科学工具和深度学习工具。 它还包括简化使用各种 Azure 数据和分析产品的工具。 可使用 Microsoft R Server 或 SQL Server 2016 在大型数据集上浏览和生成预测模型。 还包括开源社区和 Microsoft 的其他工具主机，示例代码和笔记本也包括在内。 下表详细列举并比较了包括在数据科学虚拟机 Windows 和 Linux 版中的主要组件。


| **工具**                                                           | **Windows 版** | **Linux 版** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 预安装了常用包的 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft R Server](https://msdn.microsoft.com/microsoft-r/) Developer Edition 包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-getting-started) 并行和分布式高性能 R 框架<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml-introduction) - Microsoft 提供的最先进的全新 ML 算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [R Operationalization](https://msdn.microsoft.com/microsoft-r/operationalize/about)                                            |Y                      | Y <br/> （MicrosoftML 尚不可用）|
| 预安装了常用包的 [Anaconda Python](https://www.continuum.io/) 2.7、3.5    |Y                      |Y              |
| 预安装了具有 Julia 语言的常用包的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 关系数据库                                                            | [SQL Server 2016 SP1](https://www.microsoft.com/sql-server/sql-server-2016) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) |
| 数据库工具                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> * ODBC/JDBC 驱动程序| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)（查询工具）， <br /> * bcp、sqlcmd <br /> * ODBC/JDBC 驱动程序|
| 在带有 SQL Server R 服务的数据库内分析中可扩展 | Y     |N              |
| **带有以下内核的 [Jupyter Notebook Server](http://jupyter.org/)：**                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 和 3.5 | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | N | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | 是（仅适用于 Ubuntu） |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | Y |
| JupyterHub（多用户笔记本服务器）| N | Y |
| **开发工具、IDE 和代码编辑器**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2015 (Community Edition)](https://www.visualstudio.com/community/)，包括 Git 插件、Azure HDInsight (Hadoop)、Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](http://aka.ms/ptvs) 和 [R Tools for Visual Studio (RTVS)](http://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | Y |
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
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azure CLI](https://docs.microsoft.com/cli/azure/overview) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure Powershell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [DocDB 数据迁移工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft 数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)：在 OnPrem 和云之间移动数据 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Unix/Linux 命令行实用程序 | Y | Y |
| 用于数据浏览的 [Apache Drill](http://drill.apache.org) | Y | Y |
| **机器学习工具** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* 与 [Azure 机器学习集成](https://azure.microsoft.com/services/machine-learning/)（R、Python） | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | 是（仅适用于 Ubuntu） |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | 是（仅适用于 Ubuntu） |
| **基于 GPU 的深度学习工具** |使用[适用于 DSVM 的深度学习工具包](http://aka.ms/dsvm/deeplearning) |仅限 Ubuntu 版本|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Microsoft 认知工具包 (CNTK)](http://cntk.ai) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Tensorflow](https://www.tensorflow.org/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | Y | Y|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe 和 Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA、CUDNN、Nvidia 驱动程序](https://developer.nvidia.com/cuda-toolkit) | Y | Y |
| **大数据平台（仅限 Devtest）**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* 本地独立 [Spark](http://spark.apache.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp;* 本地 [Hadoop](http://hadoop.apache.org/)（HDFS、YARN） | N | Y |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>如何使用 Windows 数据科学 VM
* 导航到[此页](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)，然后选择绿色的“创建虚拟机”按钮，在 Windows 上创建 VM 实例。
* 使用创建 VM 时指定的凭据从远程桌面登录 VM。
* 若要发现和启动可用工具，请单击“启动”菜单。

## <a name="get-started-with-the-linux-data-science-vm"></a>使用 Linux 数据科学 VM
* 在 Linux 上创建 VM 的实例
  * 对于基于 OpenLogic CentOS 的版本，导航到[此页](http://aka.ms/dsvm/centos)并选择“立即获取”按钮。
  * 对于 Ubuntu 版本，导航到[此页](http://aka.ms/dsvm/ubuntu)并选择“立即获取”按钮。
* 使用创建 VM 时指定的凭据从 SSH 客户端（例如 Putty 或 SSH 命令）登录 VM。
* 在 Shell 提示符中，输入 dsvm-more-info。
* 对于图形桌面，请在[此处](http://wiki.x2go.org/doku.php/doc:installation:x2goclient)下载用于客户端平台的 X2Go 客户端，并遵循 Linux 数据科学 VM 文档[预配 Linux 数据科学虚拟机](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client)中的说明操作。

## <a name="next-steps"></a>后续步骤
### <a name="for-the-windows-data-science-vm"></a>对于 Windows 数据科学 VM
* 有关如何运行 Windows 版本提供的特定工具的详细信息，请参阅[预配 Microsoft 数据科学虚拟机](machine-learning-data-science-provision-vm.md)
* 有关如何在 Windows VM 上执行数据科学项目所需的各种任务的详细信息，请参阅[数据科学虚拟机的十大功能](machine-learning-data-science-vm-do-ten-things.md)。

### <a name="for-the-linux-data-science-vm"></a>对于 Linux 数据科学 VM
* 有关如何运行 Linux 版本提供的特定工具的详细信息，请参阅[预配 Linux 数据科学虚拟机](machine-learning-data-science-linux-dsvm-intro.md)。
* 有关介绍如何通过 Linux VM 执行多个常见数据科学任务的演示，请参阅 [Linux 数据科学虚拟机上的数据科学](machine-learning-data-science-linux-dsvm-walkthrough.md)。


