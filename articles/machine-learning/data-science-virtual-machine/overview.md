---
title: 什么是 Azure Data Science Virtual Machine？
description: 适用于 Windows 和 Linux 数据科学虚拟机的关键分析方案和组件。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 3295a59ee4496d332f0d886c89ca900ab6b4bcd1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70191912"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>什么是适用于 Linux 和 Windows 的 Azure Data Science Virtual Machine？

Data Science Virtual Machine (DSVM) 是专为开展数据科学构建的 Azure 云平台上的自定义 VM 映像。 它预装并预配了许多热门数据科学和其他工具，可为高级分析快速生成智能应用程序。 

工具配置已经过 Microsoft 数据科学家和开发人员以及更广泛的数据科学社区的严格测试。 此测试有助于确保稳定性和广泛可行性。

DSVM 在以下环境中可用：
+ Windows Server 2016、Windows Server 2012
+ Ubuntu 16.04 LTS 和 CentOS 7.4

> [!NOTE]
> 用于深度学习的所有 VM 工具都已装入到 Data Science Virtual Machine 中。 


## <a name="what-can-i-do-with-the-dsvm"></a>DSVM 有什么作用？
Data Science Virtual Machine 的目标在于向所有技能级别和各个行业的数据专业人员提供无摩擦、预配置且完全集成的数据科学环境。 可以预配一个 DSVM，而无需自行部署一个与之相当的工作区。 这种做法可以在安装、配置和包管理方面节省数天甚至数周的时间。  分配 DSVM 后，可以立即开始处理数据科学项目。

DSVM 的设计和配置意图在于与广泛的使用方案一起使用。 随着项目需求变化，可扩大或缩小环境。 还可以使用你喜欢的语言对数据科学任务进行编程，并安装其他工具以根据你的需求自定义系统。

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>在云中预配分析桌面
DSVM 为寻求使用托管的云桌面替换本地桌面的数据科学团队提供了基线配置。 此基线确保团队中的所有数据科学家具有可验证实验并促进协作的一致设置。 它还通过减轻系统管理员负担来降低成本。 这种减轻负担可以节省评估、安装和维护高级分析软件包所需的时间。

### <a name="data-science-training-and-education"></a>数据科学训练和培训
教授数据科学课程的企业培训师和教师通常提供虚拟机映像。 该映像确保学员具有一致的设置且示例以可预测方式工作。 

DSVM 创建可缓解支持和不兼容性挑战的一致设置的按需环境。 这些环境需要频繁生成，特别是短期培训课程的情况从中获益极大。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>对于大型项目的按需弹性容量
数据科学编程马拉松/竞赛或大型数据建模和浏览需要扩展的硬件容量，通常持续时间比较短。 DSVM 有助于根据需要在允许运行高性能计算资源试验的扩展服务器上快速复制数据科学环境。

### <a name="custom-compute-power-for-azure-notebooks"></a>自定义 Azure Notebooks 的计算能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) 是一项免费的托管服务，用于在云中开发、运行和共享 Jupyter 笔记本而无需进行安装。 免费服务层限制为 4 GB 内存和 1 GB 数据。 

若要放开所有限制，可以将 Notebooks 项目附加到 DSVM 或任何其他运行 Jupyter 服务器的 VM。 如果你经由使用 Azure Active Directory 的帐户（例如公司帐户）登录到 Azure Notebooks，则 Notebooks 会自动在与该帐户关联的任意订阅中显示 DSVM。 可以[将 DSVM 附加到 Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) 以扩展可用计算能力。

### <a name="short-term-experimentation-and-evaluation"></a>短期实验和评估
只需完成极少量的设置，即可使用 DSVM 来评估或学习如下所述的工具：

- Microsoft 机器学习服务器
- SQL Server
- Visual Studio 工具
- Jupyter
- 深度学习和机器学习工具包
- 社区中常用的新工具 

由于可以快速设置 DSVM，因此可以将其应用于其他短期使用方案。 这些方案包括复制已发布的试验、执行演示、跟踪在线会话和会议教程中的演练。

### <a name="deep-learning"></a>深度学习
在 DSVM 中，训练模型可以使用基于图形处理单元 (GPU) 的硬件上的深度学习算法。 利用 Azure 平台的 VM 缩放功能，DSVM 可帮助根据需要在云中使用基于 GPU 的硬件。 若要训练大型模型或者在保留相同 OS 磁盘的同时进行高速计算，可以切换到基于 GPU 的 VM。  

Windows Server 2016 版本的 DSVM 预安装了 GPU 驱动程序、框架和 GPU 版本的深度学习框架。 在 Linux 版上，在 CentOS 和 Ubuntu DSVM 中都启用了 GPU 上的深度学习。 

还可以将 Ubuntu、CentOS 或 Windows 2016 版的 DSVM 部署到非基于 GPU 的 Azure 虚拟机。 在这种情况下，所有深度学习框架都将回退到 CPU 模式。
 
[详细了解可用的深度学习和 AI 框架](dsvm-deep-learning-ai-frameworks.md)。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM 中包含哪些组件？
数据科学虚拟机已安装并配置了许多热门数据科学工具和深度学习工具。 它还包括简化使用各种 Azure 数据和分析产品的工具。 这些产品包括用于生成预测模型的 Microsoft Machine Learning Server（R、Python），以及用于大规模探索数据集的 SQL Server 2017。 DSVM 包含来自开源社区和 Microsoft 的其他工具，以及[示例代码和笔记本](dsvm-samples-and-walkthroughs.md)。 

下面是工具和平台列表：
+ [支持的编程语言](dsvm-languages.md)

+ [支持的数据平台](dsvm-data-platforms.md)

+ [开发工具和 IDE](dsvm-tools-development.md)

+ [深度学习和 AI 框架](dsvm-deep-learning-ai-frameworks.md)

+ [机器学习和数据科学工具](dsvm-ml-data-science-tools.md)

+ [数据引入工具](dsvm-tools-ingestion.md)

+ [数据浏览和可视化工具](dsvm-tools-explore-and-visualize.md)

下表详细列举并比较了包括在数据科学虚拟机 Windows 和 Linux 版中的主要组件。

| **工具**                                                           | **Windows 版** | **Linux 版** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| 预安装了常用包的 [Microsoft R Open](https://mran.microsoft.com/open/)   |Y                      | Y             |
| [Microsoft Machine Learning Server（R、Python）](https://docs.microsoft.com/machine-learning-server/)开发人员版包括： <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) 并行分布式高性能框架（R 和 Python）<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package)：Microsoft 提供的全新一流机器学习算法 <br />  &nbsp;&nbsp;&nbsp;&nbsp; [R 和 Python 操作化](https://docs.microsoft.com/machine-learning-server/what-is-operationalization)                                            |Y                      | Y |
| 支持共享激活的 [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus：Excel、Word 和 PowerPoint   |Y                      |N              |
| 预装了常用包的 [Anaconda Python](https://www.continuum.io/) 2.7 和 3.5    |Y                      |Y              |
| 预安装了具有 Julia 语言的常用包的 [JuliaPro](https://juliacomputing.com/products/juliapro.html)                         |Y                      |Y              |
| 关系数据库                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)、<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| 数据库工具                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp、sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  ODBC/JDBC 驱动程序|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)（查询工具） <br />  bcp、sqlcmd <br />  ODBC/JDBC 驱动程序|
| 包含 SQL Server 机器学习服务（R、Python）的可缩放数据库内分析 | Y     |N              |
| 带有以下内核的 [Jupyter Notebook Server](https://jupyter.org/)                                  | Y     | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | Y | Y |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | 是（仅适用于 Ubuntu） |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | N | Y |
| JupyterHub（多用户 Notebook 服务器）| N | Y |
| JupyterLab（多用户 Notebook 服务器） | N | 是（仅适用于 Ubuntu） |
| 开发工具、IDE 和代码编辑器：| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019（社区版）](https://www.visualstudio.com/community/)，包括 Git 插件、Azure HDInsight (Hadoop)、Azure Data Lake、SQL Server Data Tools、[Node.js](https://github.com/Microsoft/nodejstools)、[Python](https://aka.ms/ptvs) 和[针对 Visual Studio 的 R 工具 (RTVS)](https://microsoft.github.io/RTVS-docs/) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (Julia IDE)](https://junolab.org/)| Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim 和 Emacs | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Git 和 Git Bash | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | Y | N |
| Power BI Desktop | Y | N |
| 访问服务的 Azure 和 Cortana Intelligence Suite 的 SDK | Y | Y |
| 数据移动和管理工具： | | |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure 存储资源管理器 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure CLI](https://docs.microsoft.com/cli/azure) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Blob FUSE 驱动程序](https://github.com/Azure/azure-storage-fuse) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azure Cosmos DB 数据迁移工具](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft 数据管理网关](https://msdn.microsoft.com/library/dn879362.aspx)：在本地与云之间移动数据 | Y | N |
| &nbsp;&nbsp;&nbsp;&nbsp; Unix/Linux 命令行工具 | Y | Y |
| 用于数据探索的 [Apache Drill](https://drill.apache.org) | Y | Y |
| 机器学习工具： |||
| &nbsp;&nbsp;&nbsp;&nbsp; 与 [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/)（R、Python）集成 | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | N | 是（仅适用于 Ubuntu） |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | N | 是（仅适用于 Ubuntu） |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/)、[Sparkling Water](https://www.h2o.ai/sparkling-water/) | N | 是（仅适用于 Ubuntu） |
| 使用 GPU 或 CPU 的深度学习工具： |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Y | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | N | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | Y (Windows 2016) | Y|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe 和 Caffe2](https://github.com/caffe2/caffe2) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet Model Server](https://github.com/awslabs/mxnet-model-server) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | N | Y |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA、cuDNN、NVIDIA 驱动程序](https://developer.nvidia.com/cuda-toolkit) | Y | Y |

## <a name="next-steps"></a>后续步骤

通过以下文章，了解详细信息：

+ Windows:
  + [设置 Windows DSVM](provision-vm.md)
  + [可以在 Windows DSVM 上执行的十项操作](vm-do-ten-things.md)

+ Linux：
  + [设置 Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [设置 Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM 上的数据科学](linux-dsvm-walkthrough.md)
