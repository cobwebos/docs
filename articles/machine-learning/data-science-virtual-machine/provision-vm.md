---
title: 快速入门：创建 Windows
titleSuffix: Azure Data Science Virtual Machine
description: 在 Azure 上配置和创建数据科学虚拟机，用于进行分析和机器学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 02/22/2019
ms.openlocfilehash: ac4b9b4d32d05083ceabd41207243eb483648baa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278594"
---
# <a name="quickstart-set-up-a-windows-data-science-virtual-machine-on-azure"></a>快速入门：在 Azure 上设置 Windows Data Science Virtual Machine

Microsoft Windows Data Science Virtual Machine (DSVM) 是 Azure 上的 Windows Server 2016 虚拟机 (VM) 映像。 DSVM 上已预装并配置了用于数据分析和机器学习的工具。

## <a name="included-data-science-tools"></a>随附的数据科学工具

DSVM 中随附以下工具：

* 用于 [Azure 机器学习服务](../index.yml)的 Python SDK。
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition。
* Enthought Python 分发版。
* 使用 R、Python、PySpark 内核的 Jupyter Notebook。
* Microsoft Visual Studio Community。
* Microsoft Power BI Desktop。
* Microsoft SQL Server 2017 Developer Edition。
* 用于本地开发和测试的独立 Apache Spark 实例。
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)。
* 机器学习和数据分析工具：
  * 深度学习框架：VM 中包含 [TensorFlow](https://www.tensorflow.org/)、[Chainer](https://chainer.org/)、MXNet 和 Keras AI 框架。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)：一种快速机器学习系统，支持在线哈希、allreduce、化简、learning2search、主动和交互式学习等技术。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)：一种可提供快速、 准确地提升树实现的工具。
  * [Rattle](https://togaware.com/rattle/)。 R 分析工具，使用它可在 R 中开始数据分析和机器学习。其中包括基于 GUI 的数据探索和使用自动 R 代码生成的建模。
  * [Weka](https://www.cs.waikato.ac.nz/ml/weka/)：Java 中的可视数据挖掘和机器学习软件。
  * [Apache Drill](https://drill.apache.org/)：面向 Apache Hadoop、NoSQL 和云存储的无架构 SQL 查询引擎。 它支持使用 ODBC 和 JDBC 接口通过 PowerBI、Microsoft Excel、Tableau 等标准 BI 工具查询 NoSQL 和文件。
* R 和 Python 中的库，供 Azure 机器学习和其他 Azure 服务使用。
* 包含 Git Bash 的 Git，可配合源代码存储库（例如 GitHub 和 Azure DevOps）使用。 Git 提供一些常用的 Linux 命令行工具，可通过 Git Bash 和命令提示符对其进行访问。 示例包括 awk、sed、perl、grep、find、wget 和 curl。
* 开发工具和编辑器（RStudio、PyCharm）。

### <a name="about-data-science"></a>关于数据科学

数据科学涉及对一系列任务的迭代：

1. 查找、加载和预处理数据。
1. 生成和测试模型。
1. 部署模型以在智能应用程序中使用。

数据科学家可以使用各种工具来完成这些任务。 找到软件的适当版本然后下载并安装这些版本，是一个相当耗时的工作。 DSVM 提供可在 Azure 上预配的现成映像，其中已预装并配置了多个常用工具，有助于节省时间。

使用 DSVM 可以立即开发分析项目。 可以处理各种语言版本的任务，包括 R、Python、SQL 和 C#。 Visual Studio 提供易用的集成开发环境 (IDE) 用于开发和测试代码。 VM 中包含 Azure SDK，可让你使用 Microsoft 云平台上的服务生成应用程序。

此 DSVM 映像不会产生软件费用。 只需支付 Azure 使用费。 具体费用取决于预配的虚拟机大小。 有关详细信息，请参阅 [Data Science Virtual Machine 页](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)。

### <a name="other-dsvm-versions"></a>其他 DSVM 版本

* [Ubuntu](dsvm-ubuntu-intro.md) 映像。 该映像包含类似于 DSVM 的工具，此外还有一些深度学习框架。
* [Linux CentOS](linux-dsvm-intro.md) 映像。
* Data Science Virtual Machine 的 [Windows Server 2012 版本](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)。 有些工具只能在 Windows Server 2016 版本中使用。 否则，本文也适用于 Windows Server 2012 版。

## <a name="prerequisite"></a>先决条件

若要创建 Microsoft Data Science Virtual Machine，必须有一个 Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.com/free)。

## <a name="create-your-dsvm"></a>创建 DSVM

创建 DSVM 实例：

1. 转到 [Azure 门户](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)上的虚拟机列表。 如果你尚未登录到 Azure 帐户，系统可能会提示你登录。
1. 选择底部的“创建”按钮。 

   ![Azure 门户上的虚拟机列表和“创建”按钮](./media/provision-vm/configure-data-science-virtual-machine.png)

1. 输入以下信息，以配置屏幕截图右窗格中显示的每个步骤：

   1. **基本信息**：
      * **名称**：输入 DSVM 的名称。
      * **VM 磁盘类型**：选择“SSD”或“HDD”。   对于 NC_v1 GPU 实例（例如，基于 Nvidia Tesla K80 的实例），请选择“HDD”作为磁盘类型。 
      * **用户名**：输入管理员帐户 ID。
      * **密码**：输入管理员帐户密码。
      * **订阅**：如果有多个订阅，请选择要在其上创建虚拟机并对其计费的订阅。
      * **资源组**：新建资源组，或使用现有资源组。
      * **位置**：选择最合适的数据中心。 为获得最快的网络访问速度，请选择包含大部分数据，或者最接近实际位置的数据中心。
   1. **大小**：选择能满足功能需求和成本约束的服务器类型。 若要查看 VM 大小的更多选项，请选择“全部查看”。 
   1. **设置**：  
      * **使用托管磁盘**。 如果希望 Azure 管理 VM 的磁盘，请选择“托管”。  否则，需要指定新的或现有的存储帐户。  
      * **其他参数**。 可以使用默认值。 若要使用非默认值，请将鼠标悬停在信息链接上获取特定字段的帮助。
   1. **汇总**：验证输入的所有信息是否正确。 选择“创建”  。

> [!NOTE]
> * 除计算“大小”步骤中选择的服务器大小所产生的计算费用外，VM 不产生其他任何费用。 
> * 预配过程将花费 10 到 20 分钟。 在 Azure 门户中可以查看 VM 的状态。

## <a name="access-the-dsvm"></a>访问 DSVM

创建并预配 VM 后，可以通过远程桌面连接来访问它。 请使用创建虚拟机时在“基本信息”步骤中配置的管理员帐户凭据。  

现在，可以开始使用 VM 上安装并配置的工具。 可以通过“开始”菜单磁贴和桌面图标访问许多工具。 

还可以将 DSVM 附加到 Azure Notebooks，以在 VM 上运行 Jupyter Notebook，并绕过免费服务层的限制。 有关详细信息，请参阅[管理和配置 Notebooks 项目](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)。

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>DVSM 上安装的工具

以下部分详细介绍了 Data Science Virtual Machine 上安装的工具。

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer Edition

可以使用 Microsoft 企业库进行分析，因为 VM 上已安装 Machine Learning Server Developer Edition。 Machine Learning Server 以前称为 Microsoft R Server，是一个可广泛部署的分析平台。 该平台可缩放，且支持商业用途。

Machine Learning Server 支持各种大数据统计、预测建模和机器学习任务。 它支持各种类型的分析：探索、分析、可视化和建模。 通过利用和扩展开源 R 及 Python，Machine Learning Server 与 R 和 Python 脚本与函数兼容。 此外，它还与 CRAN、pip 和 Conda 包兼容，可分析企业级数据。

Machine Learning Server 具有数据并行处理和区块式处理能力，可解决开源 R 的内存限制。 这意味着，可分析的数据量远远超过主内存的容量。 

VM 中随附了 Visual Studio Community。 该产品包含针对 Visual Studio 的 R 工具和针对 Visual Studio 的 Python 工具 (PTVS) 扩展，提供可配合 R 或 Python 使用的完整 IDE。 我们还在 VM 上提供了其他 IDE，例如 [RStudio](https://www.rstudio.com) 和 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/)。

### <a name="python"></a>Python

对于使用 Python 进行的开发，可以使用已安装的 Anaconda Python 分发版 2.7 和 3.6。 这些分发版包含基本 Python 以及约 300 种最常用的数学、工程和数据分析包。 可以使用 Visual Studio Community 2017 中安装的 PTVS。 或者，可以使用 Anaconda 捆绑的 IDE 之一，例如 IDLE 或 Spyder。 搜索并打开其中的某个包（按 Windows 徽标键+S）。

> [!NOTE]
> 要将针对 Visual Studio 的 Python 工具指向 Anaconda Python 2.7，需要为每个版本创建自定义环境。 若要在 Visual Studio 2017 Community 中设置这些环境路径，请转到“工具” > “Python 工具” > “Python 环境”。    然后选择“+ 自定义”。 

Anaconda Python 3.6 安装在 C:\Anaconda 下。 Anaconda Python 2.7 安装在 C:\Anaconda\envs\python2 下。 有关详细步骤，请参阅 [PTVS 文档](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)。

### <a name="the-jupyter-notebook"></a>Jupyter Notebook

Anaconda 分发版还附带 Jupyter Notebook - 用于共享代码和分析的环境。 Jupyter Notebook 服务器上已预先配置 Python 2.7、Python 3.x、PySpark、Julia 和 R 内核。 若要启动 Jupyter 服务器并打开浏览器来访问 Notebook 服务器，请使用“Jupyter Notebook”桌面图标。 

我们在 Python 和 R 中打包了多个 Notebook 示例。访问 Jupyter 之后，Notebook 将演示如何使用以下技术：

* Machine Learning Server
* SQL Server 机器学习服务（数据库内部分析）
* Python
* Microsoft 认知工具包
* TensorFlow
* 其他 Azure 技术

使用前面创建的密码向 Jupyter Notebook 进行身份验证后，可以在 Notebook 主页上看到示例的链接。

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

DSVM 包含 Visual Studio Community。 Visual Studio Community 是 Microsoft 免费提供的流行 IDE 版本，适合小型团队用来进行评估。 查看 [Microsoft 软件许可条款](https://www.visualstudio.com/support/legal/mt171547)。

使用桌面图标或“开始”菜单打开 Visual Studio。  依次搜索程序（按 Windows 徽标键+S）、**Visual Studio**。 然后，可以使用 C#、Python、R 和 Node.js 等语言来创建项目。 安装的插件可方便使用以下 Azure 服务：

* Azure 数据目录
* 适用于 Hadoop 和 Spark 的 Azure HDInsight
* Azure Data Lake

名为“适用于 Visual Studio Code 的 Azure 机器学习”插件可以集成到 Azure 机器学习，帮助快速生成 AI 应用程序。

> [!NOTE]
> 可能会有一条消息指出评估期已过。 在此情况下，请输入 Microsoft 帐户凭据。 或者新建一个免费帐户来获取 Visual Studio Community 的访问权限。

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition

DSVM 上提供了包含机器学习服务的 SQL Server 2017 开发人员版本。 此 SQL Server 版本附带 R 或 Python，并可在数据库内部分析中运行。 

机器学习服务提供一个用于开发和部署智能应用程序的平台。 可以使用这些语言以及来自社区的许多包来创建模型，根据 SQL Server 数据生成预测。 可以让分析贴近数据，因为机器学习服务（数据库内部）可在 SQL Server 中集成 R 和 Python 语言。 这种集成可以免除与数据移动相关的成本和安全风险。

> [!NOTE]
> SQL Server Developer Edition 只能用于开发和测试。 需要购买许可证才能在生产环境中运行它。

可以通过打开 Microsoft SQL Server Management Studio 来访问 SQL Server。 “服务器名称”中已填充 VM 名称。  请在 Windows 上以管理员身份登录时使用 Windows 身份验证。 进入 SQL Server Management Studio 后，可以创建其他用户、创建数据库、导入数据以及运行 SQL 查询。

若要使用 SQL Server 机器学习服务启用数据库内部分析，请在以服务器管理员身份登录后，在 SQL Server Management Studio 中运行以下命令（一次性操作）。

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

将 `%COMPUTERNAME%` 替换为你的 VM 名称。

### <a name="azure"></a>Azure

VM 上安装了多个 Azure 工具：

* 可以通过桌面快捷方式转到 Azure SDK 文档。
* 使用 AzCopy 可将数据移入和移出 Azure 存储帐户。 若要查看用法，请在命令提示符下输入 **Azcopy**。
* 使用 Azure 存储资源管理器可以浏览存储在 Azure 存储帐户中的对象。 它还能向/从 Azure 存储复制数据。 若要访问此工具，请在“搜索”字段中输入“存储资源管理器”。   或者在 Windows“开始”菜单中查找。 
* AdlCopy 可将数据复制到 Azure Data Lake。 若要查看用法，请在命令提示符下输入 **adlcopy**。
* dtui 工具可向/从 Azure Cosmos DB（云中的 NoSQL 数据库）复制数据。 在命令提示符下输入 **dtui**。
* 集成运行时可在本地数据源与云之间复制数据。 可在 Azure 数据工厂等工具中使用它。
* 使用 Azure PowerShell 可以管理以 PowerShell 脚本语言编写的 Azure 资源。 它也安装在 VM 上。

### <a name="power-bi"></a>Power BI

DSVM 上预装了 Power BI Desktop，用于帮助你生成仪表板和可视化效果。 使用此工具可从不同的源提取数据，创建仪表板和报告并将其发布到云中。 有关详细信息，请参阅 [Power BI 站点](https://powerbi.microsoft.com)。 可以在“开始”菜单中找到 Power BI Desktop。 

> [!NOTE]
> 需有 Microsoft Office 365 帐户才能访问 Power BI。

### <a name="azure-machine-learning-sdk-for-python"></a>适用于 Python 的 Azure 机器学习 SDK

数据科学家和 AI 开发人员可以结合 [Azure 机器学习服务](../service/overview-what-is-azure-ml.md)使用适用于 Python 的 Azure 机器学习 SDK 来构建和运行机器学习工作流。 可以使用开源框架（例如 TensorFlow 和 scikit-learn）来与 Jupyter Notebook 或其他 Python IDE 中的服务进行交互。

Microsoft Data Science Virtual Machine 上已预安装 Python SDK。 若要开始使用 Python SDK，请参阅[通过 Python 开始使用 Azure 机器学习](../service/quickstart-create-workspace-with-python.md)。

## <a name="more-microsoft-development-tools"></a>其他 Microsoft 开发工具

可以使用 [Microsoft Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)查找和下载其他 Microsoft 开发工具。 Microsoft Data Science Virtual Machine 桌面上也提供了该工具的快捷方式。  

## <a name="important-directories-on-the-vm"></a>VM 上的重要目录

| Item | Directory |
| --- | --- |
| Jupyter Notebook 服务器配置 | C:\ProgramData\jupyter |
| Jupyter 笔记本示例主目录 | C:\dsvm\notebooks and c:\users\\<用户名\>\notebooks |
| 其他示例 | C:\dsvm\samples |
| Anaconda，默认：Python 3.6 | C:\Anaconda |
| Anaconda Python 2.7 环境 | C:\Anaconda\envs\python2 |
| 适用于 Python 的 Microsoft Machine Learning Server（独立版） | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 默认 R 实例，Machine Learning Server（独立版） | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server 机器学习服务数据库内部实例目录 | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| 其他工具 | C:\dsvm\tools |

> [!NOTE]
> 在 DSVM 的 Windows Server 2012 版本和 2018 年 3 月之前的 Windows Server 2016 版本中，默认 Anaconda 环境是 Python 2.7。 辅助环境是位于 C:\Anaconda\envs\py35 中的 Python 3.5。

## <a name="next-steps"></a>后续步骤

* 打开“开始”菜单探索 DSVM 上的工具。 
* 阅读[什么是 Azure 机器学习服务？](../service/overview-what-is-azure-ml.md)并尝试通过[教程](../index.yml)来了解 Azure 机器学习服务。
* 在文件资源管理器中浏览到 C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts，获取有关在 R 中使用支持企业级数据分析的 RevoScaleR 库的示例。 
* 阅读文章：[Data Science Virtual Machine 的十大功能](https://aka.ms/dsvmtenthings)。
* 通过使用 [Team Data Science Process](../team-data-science-process/index.yml)，了解如何系统地构建端到端分析解决方案。
* 访问 [Azure AI 库](https://gallery.cortanaintelligence.com)，获取在 Azure 上使用 Azure 机器学习和相关数据服务的机器学习和数据分析示例。 我们还在虚拟机的“开始”菜单与桌面上提供了此库的图标。 
