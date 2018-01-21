---
title: "在 Azure 上预配 Windows 数据科学虚拟机 | Microsoft Docs"
description: "在 Azure 上配置和创建数据科学虚拟机，用于进行分析和机器学习。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: d71d8e44d0327515ed302c5c902ce87587e36c7d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2018
---
# <a name="provision-the-windows-data-science-virtual-machine-on-azure"></a>在 Azure 上预配 Windows 数据科学虚拟机
Microsoft 数据科学虚拟机是一种 Windows Azure 虚拟机 (VM) 映像，它已预装并配置了多个常用于数据分析和机器学习的热门工具。 这些工具包括：

* [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-services/) Workbench
* [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/index) Developer Edition
* Enthought Python 分发版
* Jupyter Notebook（使用 R、Python、PySpark 内核）
* Visual Studio Community Edition
* Power BI Desktop
* SQL Server 2017 Developer Edition
* 用于本地开发和测试的独立 Spark 实例
* [JuliaPro](https://juliacomputing.com/products/juliapro.html)
* 机器学习和数据分析工具
  * 深度学习框架：VM 中包含一组丰富的 AI 框架，包括 [Microsoft 认知工具包](https://www.microsoft.com/en-us/cognitive-toolkit/)、[TensorFlow](https://www.tensorflow.org/)、[Chainer](https://chainer.org/)、mxNet 和 Keras。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)：一种快速机器学习系统，支持在线、哈希、allreduce、缩减、learning2search、主动和交互式学习等技术。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)：一种提供快速、准确的提升树实现的工具。
  * [Rattle](http://rattle.togaware.com/) (R Analytical Tool To Learn Easily)（用于实现轻松学习的 R 分析工具）：该工具使在 R 中开始数据分析和机器学习变得简单，支持基于 GUI 的数据浏览和使用自动 R 代码生成进行建模。
  * [Weka](http://www.cs.waikato.ac.nz/ml/weka/)：Java 中的可视数据挖掘和机器学习软件。
  * [Apache Drill](https://drill.apache.org/)：面向 Hadoop、NoSQL 和云存储的无架构 SQL 查询引擎。  支持使用 ODBC 和 JDBC 接口通过 PowerBI、Excel、Tableau 等标准 BI 工具查询 NoSQL 和文件。
* R 和 Python 中的库，供 Azure 机器学习和其他 Azure 服务使用
* 包含 Git Bash 的 Git，可配合源代码存储库（包括 GitHub、Visual Studio Team Services）使用
* 提供 Windows 端口，包含可通过命令提示符访问的多个流行 Linux 命令行实用工具（包括 awk、sed、perl、grep、find、wget 及 curl 等）。 

执行数据科学涉及对一系列任务的迭代：

1. 查找、加载和预处理数据
2. 构建和测试模型
3. 部署模型以在智能应用程序中使用

数据科研人员可使用各种工具来完成这些任务。 找到软件的适当版本然后下载并安装这些版本，是一个相当耗时的工作。 Microsoft 数据科研虚拟机提供可在 Azure 上预配的现成映像，其中已预装并配置了多个常用工具，有助于减轻工作负担。 

Microsoft 数据科研虚拟机可快速启动分析项目。 它支持处理各种语言版本的任务，包括 R、Python、SQL 和 C#。 Visual Studio 提供 IDE，可开发易于使用的代码并对其进行测试。 使用 VM 中包含的 Azure SDK，可在 Microsoft 云平台上使用各种服务来生成应用程序。 

此数据科研 VM 映像不产生软件费用。 只需根据预配的虚拟机大小支付 Azure 使用费。 可在[数据科研虚拟机页面](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm?tab=PlansAndPrice)上的“定价详细信息”部分找到有关计算费的详细信息。 

## <a name="other-versions-of-the-data-science-virtual-machine"></a>其他版本的数据科学虚拟机
[Ubuntu](dsvm-ubuntu-intro.md) 映像同样可用，它包含多种相同的工具和一些附加的深度学习框架。 [CentOS](linux-dsvm-intro.md) 映像也可用。 我们还提供数据科学虚拟机的 [Windows Server 2012 版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.standard-data-science-vm)，不过，有些工具只能在 Windows Server 2016 版中使用。  否则，本文也适用于 Windows Server 2012 版。

## <a name="prerequisites"></a>系统必备
创建 Microsoft 数据科研虚拟机之前，必须满足以下条件：

* **Azure 订阅**：若要获取订阅，请参阅[获取 Azure 免费试用版](http://azure.com/free)。


## <a name="create-your-microsoft-data-science-virtual-machine"></a>创建 Microsoft 数据科研虚拟机
下面是创建 Microsoft 数据科研虚拟机实例的步骤：

1. 在 [Azure 门户](https://portal.azure.com/#create/microsoft-ads.windows-data-science-vmwindows2016)中导航到虚拟机列表。
2. 选择底部的“创建”按钮进入向导。![configure-data-science-vm](./media/provision-vm/configure-data-science-virtual-machine.png)
3. 用于创建 Microsoft 数据科研虚拟机的向导要求针对上图右侧列举的每个步骤（共有**四个步骤**）提供**输入**。 以下是配置每个步骤所需的输入：
   
   1. **基础知识**
      
      1. **名称**：待创建的数据科学服务器的名称。
      2. **VM 磁盘类型**：选择 SSD 或 HDD。 对于 GPU（NC 系列），请选择“HDD”作为磁盘类型。 
      3. **用户名**：管理员帐户登录 ID。
      4. **密码**：管理员帐户密码。
      5. **订阅**：如果有多个订阅，请选择要在其上创建虚拟机并对其计费的订阅。
      6. **资源组**：可以创建新组或使用现有组。
      7. **位置**：选择最适合的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近实际位置以实现最快的网络访问。
   2. **大小**：选择能满足功能需求和成本约束的服务器类型。 可以选择“查看全部”获取更多的 VM 大小选项。
   3. **设置**：
      
      1. **使用托管磁盘**：如果希望 Azure 管理 VM 的磁盘，请选择“托管”。  否则，需要指定新的或现有的存储帐户。 
      2. **其他参数**：通常只需使用默认值。 如果考虑使用非默认值，可以将鼠标悬停在特定字段的信息链接上获取帮助。
   4. **摘要**：检查输入的所有信息是否正确，并单击“创建”。 **注意**：除计算“大小”步骤中选择的服务器大小所产生的费用外，VM 没有任何其他费用。 

> [!NOTE]
> 预配约需要 10 到 20 分钟。 预配的状态在 Azure 门户上显示。
> 
> 

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>如何访问 Microsoft 数据科研虚拟机
创建 VM 后，可以使用在前面的**基本信息**部分中配置的管理员帐户凭据从远程桌面登录 VM。 

创建并预配 VM 后，便可以开始使用其上安装并配置的工具。 许多工具带有开始菜单磁贴和桌面图标。 


## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Microsoft 数据科研虚拟机上安装的工具

### <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench

Azure Machine Learning Workbench 是一个桌面应用程序和命令行接口。 Workbench 包含内置的数据准备功能，可在执行数据准备步骤时进行学习。 它还提供项目管理、运行历史记录和 Notebook 集成，大幅提高工作效率。 可以利用 TensorFlow、认知工具包、Spark ML 和 scikit-learn 等优秀开源框架来开发模型。 DSVM 上提供了一个桌面图标 (InstallAMLFromLocal)，用于在本地将 Azure Machine Learning Workbench 提取到每个用户的 %LOCALAPPDATA% 目录。 要使用 Workbench 的每个用户需要执行一次性的操作：双击 InstallAMLFromLocal 桌面图标安装 Workbench 的实例。 Azure 机器学习还会创建并使用已提取到 %LOCALAPPDATA%\amlworkbench\python 中的按用户 Python 环境。

### <a name="microsoft-ml-server-developer-edition"></a>Microsoft ML Server Developer Edition
如果想要使用可缩放 R 或 Python 的 Microsoft 企业库进行分析，VM 上已安装 Microsoft ML Server Developer Edition（以前称为 Microsoft R Server）。 Microsoft ML Server 是一个可广泛部署的企业级分析平台，可用于 R 和 Python，可缩放，受商业用途的支持且非常安全。 ML Server 支持各种大数据统计、预测建模和机器学习功能，因此可支持各种类型的分析 – 浏览、分析、可视化和建模。 通过利用和扩展开源 R 和 Python，Microsoft ML Server 与 R/Python 脚本、函数和 CRAN/pip/Conda 包完全兼容，可分析企业级数据。 此外，它具有数据并行处理和区块式处理能力，可解决开源 R 的内存限制。 因此，可分析的数据量可以远远超过主内存的容量。  VM 随附的 Visual Studio Community Edition 包含针对 Visual Studio 的 R 工具和针对 Visual Studio 的 Python 工具扩展，提供可配合 R 或 Python 使用的完整 IDE。 我们还在 VM 上其他 IDE，例如 [RStudio](http://www.rstudio.com) 和 [PyCharm Community Edition](https://www.jetbrains.com/pycharm/)。 

### <a name="python"></a>Python
为方便使用 Python 进行开发，已安装 Anaconda Python 分发版 2.7 和 3.5。 此分发版包含基本 Python 以及约 300 种最常用的数学、工程和数据分析包。 可以使用 Visual Studio 2015 Community Edition 中安装的用于 Visual Studio 的 Python 工具 (PTVS)，或 Anaconda 随附的 IDE 之一，例如 IDLE 或 Spyder。 可以通过在搜索栏（**Win** + **S** 键）中进行搜索来启动其中的某个工具。

> [!NOTE]
> 要将用于 Visual Studio 的 Python 工具指向 Anaconda Python 2.7 和 3.5，需要为每个版本创建自定义环境。 要在 Visual Studio 2015 Community Edition 中设置这些环境路径，请导航到“工具” -> “Python 工具” -> “Python 环境”，并单击“+ 自定义”。 
> 
> 

Anaconda Python 2.7 安装在 C:\Anaconda 下面，Anaconda Python 3.5 安装在 C:\Anaconda\envs\py35 下面。 有关详细步骤，请参阅 [PTVS 文档](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it)。 

### <a name="jupyter-notebook"></a>Jupyter 笔记本
Anaconda 分发版还附带 Jupyter 笔记本 - 用于共享代码和分析的环境。 Jupyter Notebook 服务器上已预先配置 Python 2.7、Python 3.5、PySpark、Julia 和 R 内核。 可通过名为“Jupyter Notebook”的桌面图标启动 Jupyter 服务器，并启动浏览器来访问 Notebook 服务器。 

> [!NOTE]
> 如果收到任何证书警告，请选择继续。 
> 
> 

我们已使用 Python 和 R 格式打包了多个 Notebook 示例。访问 Jupyter 之后，Jupyter Notebook 将演示如何使用 Microsoft ML Server、SQL Server ML Services（数据库内部分析）、Python、Microsoft 认知工具包、Tensorflow 和其他 Azure 技术。 使用在前面步骤中创建的密码向 Jupyter 笔记本进行身份验证后，可以在笔记本主页上看到示例的链接。 

### <a name="visual-studio-2017-community-edition"></a>Visual Studio 2017 Community Edition
VM 上安装的 Visual Studio Community Edition。 它是 Microsoft 免费提供的流行 IDE 版本，适合小型团队用来进行评估。 可以在[此处](https://www.visualstudio.com/support/legal/mt171547)查看许可条款。  双击桌面图标或者通过“开始”菜单打开 Visual Studio。 也可以使用 **Win** + **S** 并输入“Visual Studio”来搜索程序。 然后，便可以使用 C#、Python、R 和 node.js 等语言来创建项目。 另外，该版本中还安装了插件，以方便使用 Azure 数据目录、Azure HDInsight（Hadoop、Spark）和 Azure Data Lake 等 Azure 服务。 

> [!NOTE]
> 可能会有一条消息指出评估期已过。 在此情况下，输入 Microsoft 帐户凭据或新建一个免费帐户即可获取 Visual Studio Community Edition 的访问权限。 
> 
> 

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer Edition
VM 上提供包含 ML Services 的 SQL Server 2017 开发人员版本用于在 R 或 Python 中运行数据库内部分析。 ML Services 提供一个用于开发和部署智能应用程序的平台。 可以使用这些丰富且功能强大的语言以及来自社区的许多包来创建模型，根据 SQL Server 数据生成预测。 可以让分析贴近数据，因为 ML Services（数据库内部）可在 SQL Server 中集成 R 和 Python 语言。 这样就可免除与数据移动相关的成本和安全风险。

> [!NOTE]
> SQL Server Developer Edition 只能用于开发和测试。 需要购买许可证才能在生产环境中运行它。 
> 
> 

可以通过启动 **SQL Server Management Studio** 来访问 SQL Server。 服务器名称中已填充 VM 名称。 请在 Windows 上以管理员身份登录时使用 Windows 身份验证。 进入 SQL Server Management Studio 后，可以创建其他用户、创建数据库、导入数据以及运行 SQL 查询。 

若要使用 SQL ML Services 启用数据库内部分析，请在以服务器管理员身份登录后，在 SQL Server Management Studio 中运行以下命令（一次性操作）。 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure
VM 上安装了多个 Azure 工具：

* 可以通过一个桌面快捷方式访问 Azure SDK 文档。 
* **AzCopy**：用于将数据移入和移出 Microsoft Azure 存储帐户。 若要查看用法，请在命令提示符下键入 **Azcopy**。 
* **Microsoft Azure 存储资源管理器**：用于浏览存储在 Azure 存储帐户中的对象，以及将数据移入和移出 Azure 存储。 若要访问此工具，可在搜索中键入“存储资源管理器”或者在 Windows“开始”菜单中查找。 
* **Adlcopy**：用于将数据移入 Azure Data Lake。 若要查看用法，请在命令提示符下键入 **adlcopy**。 
* **dtui**：用于将数据移入和移出 Azure Cosmos DB（云中的 NoSQL 数据库）。 请在命令提示符下键入 **dtui**。 
* **Azure 数据工厂 Integration Runtime**：可在本地数据源与云之间移动数据。 它在 Azure 数据工厂等工具中使用。 
* **Microsoft Azure Powershell**：VM 上还安装了一个用于管理以 Powershell 脚本语言编写的 Azure 资源的工具。 

### <a name="power-bi"></a>Power BI
为了帮助用户创建仪表板和优雅的可视化效果，VM 上已安装 **Power BI Desktop**。 使用此工具可从不同的源提取数据，创建仪表板和报告并将其发布到云中。 有关详细信息，请参阅 [Power BI](http://powerbi.microsoft.com) 站点。 可以在“开始”菜单中找到 Power BI Desktop。 

> [!NOTE]
> 需有 Office 365 帐户才能访问 Power BI。 
> 
> 

## <a name="additional-microsoft-development-tools"></a>其他 Microsoft 开发工具
[**Microsoft Web 平台安装程序**](https://www.microsoft.com/web/downloads/platform.aspx)可用于发现和下载其他 Microsoft 开发工具。 Microsoft 数据科研虚拟机桌面上也提供了该工具的快捷方式。  

## <a name="important-directories-on-the-vm"></a>VM 上的重要目录
| Item | Directory |
| --- | --- |
| Jupyter 笔记本服务器配置	 |C:\ProgramData\jupyter |
| Jupyter 笔记本示例主目录 |c:\dsvm\notebooks |
| 其他示例 |c:\dsvm\samples |
| Anaconda（默认：Python 2.7） |c:\Anaconda |
| Anaconda Python 3.5 环境 |c:\Anaconda\envs\py35 |
| Microsoft ML Server 独立版 (Python)  | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| 默认 R 实例（ML Server 独立版） |C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL ML Services 数据库内部实例目录 |C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Azure Machine Learning Workbench（按用户） | %localappdata%\amlworkbench | 
| 其他工具 |c:\dsvm\tools |

> [!NOTE]
> 在低于 1.5.0 的版本（2016 年 9 月 3 日以前）中创建的 Microsoft 数据科研虚拟机实例使用的目录结构与上表中指定的结构稍有不同。 
> 
> 

## <a name="next-steps"></a>后续步骤
以下后续步骤可帮助你继续学习和探索。 

* 单击开始菜单并查看菜单中列出的工具可以浏览数据科研 VM 上的各种数据科研工具。
* 访问产品的[快速入门和教程页](https://docs.microsoft.com/azure/machine-learning/preview/)，了解 Azure 机器学习服务和 Workbench。 
* 导航到 **C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts**，获取有关在 R 中使用 RevoScaleR 库的示例，该库支持企业级数据分析。  
* 阅读文章：[数据科研虚拟机的十大功能](http://aka.ms/dsvmtenthings)
* 通过使用 [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/data-science-process/)，了解如何系统性地构建端到端分析解决方案。
* 访问 [Azure AI 库](http://gallery.cortanaintelligence.com)，获取在 Azure 上使用 Azure 机器学习和相关数据服务的机器学习和数据分析示例。 我们还在虚拟机的“开始”菜单与桌面上提供了此库的图标。

