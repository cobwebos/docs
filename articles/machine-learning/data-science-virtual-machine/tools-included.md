---
title: DSVM 中包含的工具
titleSuffix: Azure Data Science Virtual Machine tools
description: Windows 和 Ubuntu DSVM 映像中包含的工具列表
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 09/27/2019
ms.openlocfilehash: 79de3406e47b84a6120496acce5bba948fbb1a6d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803543"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 中包含哪些工具？

下面是包含在 Data Science Virtual Machine 中的工具的最新列表，以及用于了解每个工具的配置方式的链接。


| **工具**                                                           | **Windows DSVM** | **Linux DSVM** | **用法说明** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| 预安装了常用包的 [Microsoft R Open](https://mran.microsoft.com/open/)   |<span class='green-check'>&#9989;</span>                     |<span class='green-check'>&#9989;</span>  | [在 DSVM 上使用 R](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server（R、Python）](https://docs.microsoft.com/machine-learning-server/)开发人员版包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 并行分布式高性能框架（R 和 Python）<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package)：Microsoft 提供的全新一流机器学习算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R 和 Python 操作化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span> | |
| 支持共享激活的 [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus：Excel、Word 和 PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| 预装了常用包的 [Anaconda Python](https://www.continuum.io/) 2.7 和 3.5    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | |
| 预安装了具有 Julia 语言的常用包的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span>               | [在 DSVM 上使用 Julia](./dsvm-languages.md#julia) |
| 关系数据库                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | (./dsvm-data-platforms#sql-server-2016-developer-edition.md) |
| 数据库工具                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC 驱动程序|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)（查询工具） <br />  bcp、sqlcmd <br />  ODBC/JDBC 驱动程序| |
| 包含 SQL Server 机器学习服务（R、Python）的可缩放数据库内分析 |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| 带有以下内核的 [Jupyter Notebook Server](https://jupyter.org/)                                  |<span class='green-check'>&#9989;</span>     |<span class='green-check'>&#9989;</span> | [Jupyter Notebook 示例](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [R Jupyter 示例](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Python Jupyter 示例](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Julia Jupyter 示例](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [pySpark Jupyter 示例](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>（仅适用于 Ubuntu） | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub（多用户 Notebook 服务器）|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab（多用户 Notebook 服务器） |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>（仅适用于 Ubuntu） | |
| 开发工具、IDE 和代码编辑器：| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019（社区版）](https://www.visualstudio.com/community/)，包括 Git 插件、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs) 和[针对 Visual Studio 的 R 工具 (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Visual Studio 2019](./dsvm-tools-development.md#visual-studio-2019) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 RStudio Desktop](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 RStudio Server](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 PyCharm](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Juno](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; Vim 和 Emacs |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Git 和 Git Bash |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| Power BI Desktop |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   |
| 访问服务的 Azure 和 Cortana Intelligence Suite 的 SDK |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Power BI Desktop](./dsvm-tools-development.md#power-bi-desktop) |
| 数据移动和管理工具： | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure 存储资源管理器 |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Azcopy](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Blob FUSE 驱动程序](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [DSVM 上的 blobfuse](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Adlcopy](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB 数据迁移工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 上的 Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft 数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)：在本地与云之间移动数据 |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [DSVM 用法](./dsvm-tools-ingestion.md#microsoft-data-management-gateway) |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix/Linux 命令行工具 |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| 用于数据探索的 [Apache Drill](https://drill.apache.org) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Apache 演练](./dsvm-tools-explore-and-visualize.md#apache-drill)
| 机器学习工具： ||||
| &nbsp;&nbsp;&nbsp;&nbsp; 与 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)（R、Python）集成 |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Azure ML SDK](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 XGBoost](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Vowpal Wabbit](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [DSVM 上的 Weka](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>（仅适用于 Ubuntu） | [DSVM 上的 LightGBM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>（仅适用于 Ubuntu） | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span>（仅适用于 Ubuntu） | [DSVM 上的 H20](./dsvm-ml-data-science-tools.md#h2o) |
| 使用 GPU 或 CPU 的深度学习工具： |  |  | 有关用法的详细信息，请单击下面的工具名称 |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) (Windows 2016) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |<span class='green-check'>&#9989;</span> (Windows 2016) |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](./dsvm-deep-learning-ai-frameworks.md#horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> (Ubuntu) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe 和 Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](./dsvm-deep-learning-ai-frameworks.md#chainer) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](./dsvm-deep-learning-ai-frameworks.md#keras)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](./dsvm-deep-learning-ai-frameworks.md#pytorch)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA 驱动程序](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
