---
title: 参考：CentOS DSVM
description: CentOS 中包含的工具的详细信息 Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 9366eb1bde05d80b8882ee28aa9eb03a75f75964
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174716"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>参考：CentOS （Linux） Data Science Virtual Machine

Linux Data Science Virtual Machine (DSVM) 是基于 CentOS 的 Azure 虚拟机。 Linux DSVM 随附一系列预安装的工具，用于进行数据分析和机器学习。 

Linux DSVM 包含的关键软件组件包括：

* Linux CentOS 分发版操作系统。
* Microsoft Machine Learning Server。
* Anaconda Python 分发版（3.5 和 2.7 版），包括常用数据分析库。
* JuliaPro：Julia 语言和常用科学与数据分析库的特选分发版。
* Spark 独立实例和单节点 Hadoop（HDFS、YARN）。
* JupyterHub：支持 R、Python、PySpark 和 Julia 内核的多用户 Jupyter Notebook 服务器。
* Azure 存储资源管理器下载。
* Azure CLI：用于管理 Azure 资源的 Azure 命令行接口。
* PostgresSQL 数据库。
* 机器学习工具：
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK)：Microsoft Research 开发的深度学习软件工具包。
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)：一种快速机器学习系统，支持在线哈希、allreduce、化简、learning2search、主动和交互式学习等技术。
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)：一种可提供快速、准确地提升树实现的工具。
  * [Rattle](https://togaware.com/rattle/)：一种可帮助在 R 中轻松开始进行数据分析和机器学习的工具。 Rattle 使用自动 R 代码生成提供基于 GUI 的数据探索和建模。
* Java、Python、Node.js、Ruby 和 PHP 中的 Azure SDK。
* R 和 Python 中的库，供 Azure 机器学习和其他 Azure 服务使用。
* 开发工具和编辑器（RStudio、PyCharm、IntelliJ、Emacs、gedit、vi）。

数据科学涉及对一系列任务的迭代：

1. 查找、加载和预处理数据。
1. 生成和测试模型。
1. 部署模型以在智能应用程序中使用。

数据科学家使用各种工具完成这些任务。 找到适当软件的版本，然后下载、编译并安装这些版本，可能是一个相当耗时的工作。

Linux DSVM 可大大减轻这种负担。 使用 Linux DSVM 可以快速启动分析项目。 Linux DSVM 可帮助你处理各种语言版本的任务，包括 R、Python、SQL、Java 和 C++。 Eclipse 提供一个易于使用的 IDE 来开发和测试代码。 使用 DSVM 中包含的 Azure SDK，可在适用于 Microsoft 云平台的 Linux 上使用各种服务来生成应用程序。 已预装其他语言，包括 Ruby、Perl、PHP 和 Node.js。

DSVM 映像不会产生软件费用。 只需根据使用该 DSVM 映像预配的虚拟机大小，支付相应的 Azure 硬件使用费。 有关计算费的详细信息，请参阅 Azure 市场中的 [Data Science Virtual Machine for Linux (CentOS) 列表](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)。


## <a name="machine-learning-server"></a>Machine Learning Server

R 是数据分析和机器学习的最常用语言之一。 若要使用 R 进行分析，可以利用 DSVM 中带有 Microsoft R Open 和数学内核库的 Machine Learning Server。 数学内核库可优化分析算法中常用的数学运算。 R Open 与 CRAN R 完全兼容。在 CRAN 中发布的任何 R 库可安装在 R Open 上。 

可以使用 Machine Learning Server 将 R 模型缩放和实施为 Web 服务。 可以在其中一个默认编辑器（如 RStudio、vi 或 Emacs）中编辑 R 程序。 Emacs 编辑器已预装在 DSVM 上。 Emacs ESS (Emacs Speaks Statistics) 可简化 Emacs 编辑器中 R 文件的处理。

若要打开 R 控制台，请在 shell 中输入 **R**。执行此命令将进入交互式环境。 若要开发 R 程序，通常会使用 Emacs 或 vi 等编辑器，然后在 R 中运行脚本。RStudio 提供一个完整的图形 IDE 来开发 R 程序。

DSVM 中包含了一个用于安装[最重要的 20 个 R 包](https://www.kdnuggets.com/2015/06/top-20-r-packages.html)的 R 脚本。 在 R 交互式界面中可以运行此脚本。 如前所述，若要打开该界面，请在 shell 中输入 **R**。  

## <a name="python"></a>Python

Python 3.5 和 2.7 环境中已安装 Anaconda Python。 2\.7 环境称为“根”，3.5 环境称为“py35”。 此分发版包含基本 Python 以及约 300 种最常用的数学、工程和数据分析包。

默认为 py35 环境。 若要激活根 (2.7) 环境，请使用以下命令：

```bash
source activate root
```

若要再次激活 py35 环境，请使用以下命令：

```bash
source activate py35
```

若要调用 Python 交互式会话，请在 shell 中输入 **python**。 

使用 Conda 或 pip 安装其他 Python 库。 对于 pip，如果不想要使用默认值，请先激活正确的环境：

```bash
source activate root
pip install <package>
```

或者，指定到 pip 的完整路径：

```bash
/anaconda/bin/pip install <package>
```

对于 Conda，始终应指定环境名称（py35 或根）：

```bash
conda install <package> -n py35
```

如果在图形界面上操作或者已设置 X11 转发，可以输入 **pycharm** 打开 PyCharm Python IDE。 可以使用默认文本编辑器。 此外，可以使用 Spyder，它是与 Anaconda Python 分发版捆绑在一起的 Python IDE。 Spyder 需要图形桌面或 X11 转发。 图形桌面中提供了 Spyder 的快捷方式。

## <a name="jupyter-notebook"></a>Jupyter Notebook

Anaconda 分发版还附带 Jupyter Notebook - 用于共享代码和分析的环境。 通过 JupyterHub 访问 Jupyter Notebook。 使用本地 Linux 用户名和密码登录。

Jupyter Notebook 服务器中已预配置 Python 2、Python 3 和 R 内核。 使用“Jupyter Notebook”桌面图标打开浏览器并访问 Jupyter Notebook 服务器。 如果通过 SSH 或 X2Go 客户端访问 DSVM，则还可以通过 https:\//localhost:8000/ 访问 Jupyter Notebook 服务器。

> [!NOTE]
> 如果收到任何证书警告，请选择继续。

可以从任何主机访问 Jupyter 笔记本服务器。 输入 **https:\//\<DSVM DNS 名称或 IP 地址\>:8000/** 。

> [!NOTE]
> 默认情况下，配置 DSVM 时，防火墙中会打开端口 8000。 

Microsoft 已打包两个示例笔记本（分别在 Python 和 R 中）。使用本地 Linux 用户名和密码对 Jupyter Notebook 进行身份验证后，可以在 Jupyter Notebook 主页上看到示例的链接。 若要创建新的笔记本，请选择“新建”，然后选择要使用的语言内核。 如果未看到“新建”按钮，请选择左上角的“Jupyter”图标转到 Notebook 服务器的主页。

## <a name="spark-standalone"></a>Spark 独立版 

一个 Spark 独立版模式实例已预装在 Linux DSVM 上，以帮助你在本地开发 Spark 应用程序，然后在大型群集上对其进行测试和部署。 

可以通过 Jupyter 内核运行 PySpark 程序。 打开 Jupyter 时，选择“新建”按钮即可看到可用内核的列表。 “Spark - Python”是 PySpark 内核。借助它可以使用 Python 语言生成 Spark 应用程序。 还可以使用 Python IDE（如 PyCharm 或 Spyder）生成 Spark 程序。 

在此独立实例中，Spark 堆栈会在调用方客户端程序中运行。 与在 Spark 群集上进行开发相比，使用此功能可以更快、更轻松地排查问题。

Jupyter 提供一个示例 PySpark 笔记本。 可以在 Jupyter 主目录下的 SparkML 目录中找到该笔记本 ($HOME/notebooks/SparkML/pySpark)。 

若要以 R for Spark 编程，可以使用 Machine Learning Server、SparkR 或 sparklyr。 

在 Machine Learning Server 的 Spark 上下文中运行之前，需要执行一次性的设置步骤来启用本地单节点 Hadoop HDFS 和 YARN 实例。 默认情况下，Hadoop 服务已安装但在 DSVM 上禁用。 若要启用 Hadoop 服务，请首次以 root 身份运行以下命令：

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

不需要 Hadoop 相关的服务时，可以通过运行 `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn` 来停止这些服务。

/dsvm/samples/MRS 目录中提供了一个示例，演示如何在远程 Spark 上下文（即，DSVM 上的独立 Spark 实例）中开发和测试 Machine Learning Server。

## <a name="ides-and-editors"></a>IDE 和编辑器

可以从多个代码编辑器中进行选择，包括 vi/VIM、Emacs、gedit、PyCharm、RStudio、Eclipse、LaTeX 和 IntelliJ。 

* gedit、Eclipse、IntelliJ、R Studio 和 PyCharm 是图形编辑器。 若要使用它们，必须登录到图形桌面。 可以使用桌面和应用程序菜单中的快捷方式打开它们。

* Vim 和 Emacs 是基于文本的编辑器。 Emacs 上的 ESS 附加包可以简化 Emacs 编辑器中 R 的处理。 可在 [ESS 网站](https://ess.r-project.org/)上找到更多信息。

* Eclipse 是支持多种语言的开源可扩展 IDE。 面向 Java 开发人员的 Eclipse IDE 是安装在 DSVM 上的版本。 可安装适用于多种常用语言的插件来扩展环境。 

  DSVM 上还随 Eclipse 一起安装了 Azure Toolkit for Eclipse 插件。 借助 Azure Toolkit for Eclipse，可以使用支持 Java 等语言的 Eclipse 开发环境来创建、开发、测试和部署 Azure 应用程序。

  DSVM 上还随 Azure Toolkit for Eclipse 一起安装了 Azure SDK for Java。 使用 Azure SDK for Java 可从 Java 环境内部访问不同的 Azure 服务。 
  
  有关详细信息，请参阅[用于 Eclipse 的 Azure 工具包](/java/azure/eclipse/azure-toolkit-for-eclipse)。

* LaTeX 是通过 texlive 包连同名为 [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) 的 Emacs 附加包一起安装的。 此包简化了 Emacs 中 LaTeX 文档的创作。 

## <a name="databases"></a>数据库

在 Linux DSVM 中可以访问多个数据库和命令行工具。

### <a name="postgressql"></a>PostgresSQL

在服务正在运行时且 initdb 已完成的情况下，可在 DSVM 上使用开放源数据库 PostgresSQL。 必须创建数据库和用户。 有关详细信息，请参阅 [PostgresSQL 文档](https://www.postgresql.org/docs/)。  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL 是一个图形 SQL 客户端，可以连接到各种数据库（包括 SQL Server、PostgresSQL 和 MySQL）并运行 SQL 查询。 可以使用桌面图标从图形桌面会话运行 SQuirreL SQL（例如，通过 X2Go 客户端）。 或者，可以在 shell 中使用以下命令运行该客户端：

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

首次使用前，需设置驱动程序和数据库别名。 JDBC 驱动程序位于 /usr/share/java/jdbcdrivers 中。

有关详细信息，请参阅 [SQuirreL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)。

### <a name="command-line-tools-for-accessing-sql-server"></a>用于访问 SQL Server 的命令行工具

SQL Server 的 ODBC 驱动程序包还附带两个命令行工具：

* **bcp**：bcp 工具可在 SQL Server 实例与用户指定格式的数据文件之间批量复制数据。 可以使用 bcp 工具将大量新行导入 SQL Server 表，或者将表中的数据导出到数据文件。 要将数据导入表中，必须使用为该表创建的格式文件。 或者，必须了解表的结构，以及对其列有效的数据类型。

  有关详细信息，请参阅[使用 bcp 进行连接](https://msdn.microsoft.com/library/hh568446.aspx)。

* **sqlcmd**：可以在命令提示符下，使用 sqlcmd 实用工具输入 Transact-SQL 语句、系统过程和脚本文件。 sqlcmd 实用工具使用 ODBC 执行 Transact-SQL 批处理。

  有关详细信息，请参阅[使用 sqlcmd 进行连接](https://msdn.microsoft.com/library/hh568447.aspx)。

  > [!NOTE]
  > 此工具在 Linux 和 Windows 平台之间存在差异。 有关详细信息，请参阅文档。

### <a name="database-access-libraries"></a>数据库访问库

在 R 和 Python 中可以使用数据库访问库。

* 在 R 中，可以使用 RODBC 包或 dplyr 包在数据库服务器上查询或运行 SQL 语句。
* 在 Python 中，pyodbc 库提供使用 ODBC 作为基础层的数据库访问。

## <a name="azure-tools"></a>Azure 工具

DSVM 上已安装以下 Azure 工具：

* **Azure CLI**：可以使用 Azure 中的命令行接口通过 shell 命令创建和管理 Azure 资源。 若要打开 Azure 工具，请输入 **azure help**。 有关详细信息，请参阅 [Azure CLI 文档页](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。
* **Azure 存储资源管理器**：Azure 存储资源管理器是一个图形工具，用于浏览在 Azure 存储帐户中存储的对象，以及将数据上传到 Azure Blob 和从中下载数据。 可通过桌面快捷方式图标访问存储资源管理器。 还可以通过输入 **StorageExplorer** 从 shell 提示符打开此工具。 必须从 X2Go 客户端登录，或设置 X11 转发。
* **Azure 库**：DSVM 上已预装以下库：
  
  * **Python**：Python 中的 Azure 相关库包括 *azure*、*azureml*、*pydocumentdb* 和 *pyodbc*。 使用前三个库，可以访问 Azure 存储服务、Azure 机器学习和 Azure Cosmos DB（Azure 上的 NoSQL 数据库）。 使用第四个库 pyodbc（以及 SQL Server 的 Microsoft ODBC 驱动程序），可以通过使用 ODBC 接口从 Python 访问 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库。 输入 **pip 列表**查看所有列出的库。 请确保在 Python 2.7 和 3.5 环境中都运行此命令。
  * **R**：R 中的 Azure 相关库包括 AzureML 和 RODBC。
  * **Java**：可在 DSVM 上的 /dsvm/sdk/AzureSDKJava 目录中找到 Azure Java 库列表。 密钥库是 Azure 存储和用于 SQL Server 的管理 API、Azure Cosmos DB 和 JDBC 驱动程序。  

可以从预装的 Firefox 浏览器访问 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中，可以创建、管理和监视 Azure 资源。

## <a name="azure-machine-learning"></a>Azure 机器学习

Azure 机器学习是完全托管的云服务，可用于生成、部署和共享预测分析解决方案。 从 Azure 机器学习工作室中构建实验和模型。 若要从 DSVM 上的 Web 浏览器访问 Azure 机器学习，请参阅 [Microsoft Azure 机器学习](https://studio.azureml.net)。

登录到 Azure 机器学习工作室后，可以使用试验画布来生成机器学习算法的逻辑流。 还可以访问 Azure 机器学习上托管的 Jupyter Notebook。 Notebook 可与机器学习工作室中的试验无缝配合。 

将生成的机器学习模型包装在 Web 服务接口中可将其操作化。 实施机器学习模型使得以任何语言编写的客户端都能从这些模型中调用预测。 有关详细信息，请参阅[机器学习文档](https://azure.microsoft.com/documentation/services/machine-learning/)。

还可以在 DSVM 上的 R 或 Python 中生成模型，然后在 Azure 机器学习中将其部署到生产环境。 Microsoft 已安装 R 中的库 (**AzureML**) 和 Python 中的库 (**azureml**) 以支持此功能。

有关如何将 R 和 Python 中的模型部署到 Azure 机器学习的信息，请参阅 [Data Science Virtual Machine 的十大功能](vm-do-ten-things.md)。

> [!NOTE]
> [Data Science Virtual Machine 的十大功能](vm-do-ten-things.md)中的说明适用于 Windows 版 DSVM。 但是，有关将模型部署到 Azure 机器学习的信息也适用于 Linux DSVM。

## <a name="machine-learning-tools"></a>机器学习工具

DSVM 随附一些已预编译并已在本地预装的机器学习工具和算法。 这些问题包括：

* **Microsoft 认知工具包**：深度学习工具包。
* **Vowpal Wabbit**：一种快速的在线学习算法。
* **XGBoost**：提供经过优化的提升树算法的工具。
* **Python**：Anaconda Python 附带机器学习算法，这些算法含有库（如 Scikit-learn）。 可以通过使用 `pip install` 命令安装其他库。
* **R**：有丰富的机器学习函数库可供 R 使用。预装的库包括 lm、glm、randomForest 和 rpart。 运行 `install.packages(<lib name>)` 可安装其他库。

后续部分将更详细介绍 Microsoft Cognitive Toolkit、Vowpal Wabbit 和 XGBoost。

### <a name="microsoft-cognitive-toolkit"></a>Microsoft 认知工具包

Microsoft Cognitive Toolkit 是一个开源深度学习工具包。 它是一个命令行工具 (CNTK)，已在 PATH 中指定。

若要运行基本示例，请在 shell 中运行以下命令：

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

有关详细信息，请参阅 [GitHub CNTK 存储库](https://github.com/Microsoft/CNTK)和 [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)。

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit 是一种使用 online、hashing、allreduce、reductions、learning2search、主动和交互式学习等技术的机器学习系统。

若要对基本示例运行该工具，请运行以下命令：

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Vowpal Wabbit 演示目录包含其他更详细的演示。 有关 Vowpal Wabbit 的详细信息，请参阅 [GitHub Vowpal Wabbit 存储库](https://github.com/JohnLangford/vowpal_wabbit)和 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)。

### <a name="xgboost"></a>XGBoost

XGBoost 库是为提升（树）算法设计和优化的库。 XGBoost 库的目标是将计算机的计算限制推向极致，以满足提供可缩放、可移植且精确的大规模树提升的需求。

XGBoost 作为命令行和 R 库提供。

若要在 R 中使用 XGBoost 库，请启动交互式 R 会话（在 shell 中输入 **R**），然后加载该库。

下面是可以在 R 提示符下运行的一个简单示例：

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

若要运行 XGBoost 命令行，请在 shell 中运行以下命令：

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

一个 .model 文件将写入到指定的目录。 有关 GitHub 上的此演示示例的信息，请参阅[二元分类](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification)。

有关 XGBoost 的详细信息，请参阅 [XGBoost 文档](https://xgboost.readthedocs.org/en/latest/)和 [XGBoost GitHub 存储库](https://github.com/dmlc/xgboost)。

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) 使用基于 GUI 的数据浏览和建模。 Rattle：
- 呈现数据的统计和可视化摘要。
- 转换可随时建模的数据。
- 基于数据生成非监督式和监督式模型。
- 以图形方式呈现模型性能。
- 对新数据集评分。
- 生成 R 代码。
- 复制 UI 中可直接在 R 中运行或用作其他分析的起点的操作。

若要运行 Rattle，必须登录到图形桌面会话。 在终端中，输入 **R** 打开 R 环境。 在 R 提示符中，输入以下命令：

```R
library(rattle)
rattle()
```

此时会打开包含一组选项卡的图形界面。 在 Rattle 中执行以下快速入门步骤，使用示例天气数据集并生成模型。 在某些步骤中，系统会提示自动安装并加载尚未安装在系统上的某些必需 R 包。

> [!NOTE]
> 如果你无权在系统目录（默认目录）中安装包，可能会在 R 控制台窗口中看到一个提示，指出包将安装到个人库中。 如果看到这些提示，请输入 **y**。

1. 选择“执行”。
1. 此时会出现一个对话框，提示是否要加载示例气象数据集。 选择“是”以加载示例。
1. 选择“模型”选项卡。
1. 选择“执行”以生成决策树。
1. 选择“绘制”以显示决策树。
1. 选择“林”选项，然后选择“执行”以生成随机林。
1. 选择“评估”选项卡。
1. 选择“风险”选项，然后选择“执行”以显示两个“风险(累积)”性能绘图。
1. 选择“日志”选项卡以显示为上述操作生成的 R 代码。 （由于当前 Rattle 版本中的 bug，必须在日志文本中的“导出此日志”前面插入 **#** 字符。）
1. 选择“导出”按钮，将名为 *weather_script.R* 的 R 脚本文件保存到主文件夹。

可以退出 Rattle 和 R。现在，可以修改生成的 R 脚本。 或者，可以按原样使用该脚本，并可随时运行它来重复 Rattle UI 中的所有操作。 尤其是对于 R 初学者而言，使用此方法可在简单的图形界面中快速执行分析和机器学习，同时在 R 中自动生成代码来修改项目或用于学习。
