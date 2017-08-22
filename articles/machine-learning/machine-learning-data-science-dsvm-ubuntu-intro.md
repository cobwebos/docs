---
title: "在 Azure 上预配适用于 Linux (Ubuntu) 的数据科学虚拟机 | Microsoft Docs"
description: "在 Azure 上配置和创建适用于 Linux (Ubuntu) 的数据科学虚拟机，进行分析和机器学习。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 3bab0ab9-3ea5-41a6-a62a-8c44fdbae43b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 148603618a7da05d250a6c4f789a212e98791e53
ms.contentlocale: zh-cn
ms.lasthandoff: 08/01/2017

---
# <a name="provision-the-data-science-virtual-machine-for-linux-ubuntu"></a>在 Azure 上预配适用于 Linux (Ubuntu) 的数据科学虚拟机
适用于 Linux 的数据科学虚拟机是基于 Ubuntu 的虚拟机映像，使用它可以轻松地开始在 Azure 上进行深度学习。 深度学习工具包括：

  * [Caffe](http://caffe.berkeleyvision.org/)：一种以高速、清晰表达和模块化为理念建立起来的深度学习框架
  * [Caffe2](https://github.com/caffe2/caffe2)：Caffe 的跨平台版本
  * [计算网络工具包 (CNTK)](https://github.com/Microsoft/CNTK)：Microsoft Research 的深度学习软件工具包
  * [H2O](https://www.h2o.ai/)：开源大数据平台和图形用户界面
  * [Keras](https://keras.io/)：Python 中基于 Theano 和 TensorFlow 的高级神经网络 API
  * [MXNet](http://mxnet.io/)：灵活、高效的深度学习库，具有许多语言绑定
  * [NVIDIA DIGITS](https://developer.nvidia.com/digits)：一种简化常见深度学习任务的图形系统
  * [TensorFlow](https://www.tensorflow.org/)：来自 Google 的机器智能的开源库
  * [Theano](http://deeplearning.net/software/theano/)：用于定义、优化和高效评估涉及多维数组的数学表达式的 Python 库
  * [Torch](http://torch.ch/)：一种广泛支持机器学习算法的科学计算框架
  * CUDA、cuDNN 和 NVIDIA 驱动程序
  * 许多示例 Jupyter 笔记本

所有库都是 GPU 版本，但也可在 CPU 上运行。

适用于 Linux 的数据科学虚拟机还包含数据科学和开发活动的常用工具，包括：

* Microsoft R Server Developer Edition with Microsoft R Open
* Anaconda Python 分发版（2.7 和 3.5 版），包括常用数据分析库
* JuliaPro - 具有常用科学和数据分析库的 Julia 语言的特选分发
* 独立 Spark 实例和单节点 Hadoop（HDFS、Yarn）
* JupyterHub - 支持 R、Python、PySpark 和 Julia 内核的多用户 Jupyter 笔记本服务器
* Azure 存储资源管理器
* 用于管理 Azure 资源的 Azure 命令行接口 (CLI)
* 机器学习工具
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit)：一种快速机器学习系统，支持在线、哈希、allreduce、缩减、learning2search、主动和交互式学习等技术
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/)：一种提供快速、准确的提升树实现的工具
  * [Rattle](http://rattle.togaware.com/)：一种图形工具，可帮助在 R 中轻松开始进行数据分析和机器学习
  * [LightGBM](https://github.com/Microsoft/LightGBM)：一种快速、分布式、高性能的梯度提升框架
* Java、Python、node.js、Ruby 和 PHP 中的 Azure SDK
* R 和 Python 中的库，供 Azure 机器学习和其他 Azure 服务使用
* 开发工具和编辑器（RStudio、PyCharm、IntelliJ、Emacs、vim）


执行数据科学涉及对一系列任务的迭代：

1. 查找、加载和预处理数据
2. 构建和测试模型
3. 部署模型以在智能应用程序中使用

数据科学家使用各种工具完成这些任务。 找到软件的适当版本，并下载、编译和安装这些版本，这一过程可能耗时较长。

适用于 Linux 的数据科学虚拟机可大大减轻这种负担。 使用它快速开始分析项目。 它支持处理各种语言版本的任务，包括 R、Python、SQL、Java 和 C++。 使用 VM 中包含的 Azure SDK，可在适用于 Microsoft 云平台的 Linux 上使用各种服务来生成应用程序。 此外，还可以访问其他预安装语言，如 Ruby、Perl、PHP 和 node.js。

此数据科学 VM 映像不产生软件费用。 只需根据预配的虚拟机大小，支付相应的 Azure 硬件使用费。 有关费用计算的更多详细信息，请访问 [Azure 应用商店上的 VM 列表页](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)。

## <a name="other-versions-of-the-data-science-virtual-machine"></a>其他版本的数据科学虚拟机
[CentOS](machine-learning-data-science-linux-dsvm-intro.md) 映像同样可用，它包含多种与 Ubuntu 映像相同的工具。 [Windows](machine-learning-data-science-provision-vm.md) 映像同样可用。

## <a name="prerequisites"></a>先决条件
创建适用于 Linux 的数据科学虚拟机之前，必须具备 Azure 订阅。 若要获取一项订阅，请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/free/)。

## <a name="create-your-data-science-virtual-machine-for-linux"></a>创建适用于 Linux 的数据科学虚拟机
以下步骤用于创建适用于 Linux 的数据科学虚拟机的实例：

1. 导航到 [Azure 门户](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu)上的虚拟机列表。
2. 单击底部的“创建”打开向导。![configure-data-science-vm](./media/machine-learning-data-science-dsvm-ubuntu-intro/configure-data-science-virtual-machine.png)
3. 以下部分提供用于创建 Microsoft 数据科学虚拟机的向导中每个步骤的输入（在上一图片的右侧枚举）。 以下是配置每个步骤所需的输入：
   
   a. **基本信息**：
   
   * **名称**：待创建的数据科学服务器的名称。
   * **用户名称**：第一个帐户登录 ID。
   * **密码**：第一个帐户密码（可以使用 SSH 公钥代替密码）。
   * **订阅**：如果有多个订阅，请选择要在其上创建虚拟机并对其计费的订阅。 必须具有此订阅的资源创建权限。
   * **资源组**：可以创建新组或使用现有组。
   * **位置**：选择最适合的数据中心。 通常，最合适的数据中心应拥有大部分数据，或者最接近物理位置以实现最快的网络访问。
   
   b. **大小**：
   
   * 选择能满足功能需求和成本约束的服务器类型。 选择“查看全部”查看 VM 大小的更多选择。 选择 GPU 训练的 NC 类 VM。
   
   c. **设置**：
   
   * **磁盘类型**：如果喜欢固态硬盘 (SSD)，请选择“高级”。 否则，请选择“标准”。 GPU VM 要求使用一个标准磁盘。
   * **存储帐户**：可以在订阅中创建新的 Azure 存储帐户，或者使用位于与向导的**基本信息**步骤中选择的相同位置的现有帐户。
   * **其他参数**：大多数情况下，只需使用默认值。 若考虑使用非默认值，请将鼠标悬停在信息链接上获取特定字段的帮助。
   
   d.单击“下一步”。 **汇总**：
   
   * 验证输入的所有信息是否正确。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 **购买**：
   
   * 若要开始预配，请单击“购买”。 提供交易条款的链接。 除计算**大小**步骤中选择的服务器大小所产生的费用外，VM 没有任何其他费用。

预配大约需要 5 到 10 分钟。 预配的状态在 Azure 门户上显示。

## <a name="how-to-access-the-data-science-virtual-machine-for-linux"></a>如何访问适用于 Linux 的数据科学虚拟机
创建 VM 后，可使用 SSH 登录。 使用在步骤 3 的**基本信息**部分中为文本 shell 接口创建的帐户凭据。 可在 Windows 上下载 [Putty](http://www.putty.org) 之类的 SSH 客户端工具。 如果喜欢图形桌面（X Windows系统），可以在 Putty 上使用 X11 转发或安装 X2Go 客户端。

> [!NOTE]
> 在测试方面，X2Go 客户端的性能明显优于 X11 转发。 建议对图形桌面界面使用 X2Go 客户端。
> 
> 

## <a name="installing-and-configuring-x2go-client"></a>安装和配置 X2Go 客户端
Linux VM 已通过 X2Go 服务器进行预配并且可接受客户端连接。 若要连接到 Linux VM 图形桌面，请在客户端上执行以下操作：

1. 从 [X2Go ](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) 为客户端平台下载并安装 X2Go 客户端。    
2. 运行 X2Go 客户端，并选择“新建会话”。 这会打开具有多个选项卡的配置窗口。 输入下列配置参数:
   * **会话选项卡**：
     * **主机**：主机名或 Linux 数据科学 VM 的 IP 地址。
     * **登录名**：Linux VM 上的用户名。
     * **SSH 端口**：保留默认值 22。
     * **会话类型**：将值更改为 XFCE。 Linux VM 目前仅支持 XFCE 桌面。
   * **媒体选项卡**：如果无需使用声音支持和客户端打印功能，则可将其关闭。
   * **共享文件夹**：如果希望将目录从客户端计算机装入 Linux VM，则在此选项卡上添加要与 VM 共享的客户端计算机目录。

通过 X2Go 客户端使用 SSH 客户端或 XFCE 图形桌面登录 VM 后，即可开始使用 VM 上安装和配置的工具。 在 XFCE 上，可看到许多工具的应用程序菜单快捷方式和桌面图标。

## <a name="tools-installed-on-the-data-science-virtual-machine-for-linux"></a>在适用于 Linux 的数据科学虚拟机上安装的工具
### <a name="deep-learning-libraries"></a>深度学习库

#### <a name="cntk"></a>CNTK
Microsoft Cognitive Toolki 也称为 CNTK，是一种开放源的深度学习工具包。 Python 绑定位于根环境和 py35 Conda 环境中。 它还具有已存在于 PATH 中的命令行工具 (cntk)。

示例 Python 笔记本位于 JupyterHub。 若要在命令行中运行基础示例，请在 shell 中执行以下命令：

    cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
    cntk configFile=lr_bs.cntk makeMode=false command=Train

有关详细信息，请参阅 [GitHub](https://github.com/Microsoft/CNTK) 的 CNTK 部分，以及 [CNTK wiki](https://github.com/Microsoft/CNTK/wiki)。

#### <a name="caffe"></a>Caffe
Caffe 是美国伯克利视觉与学习中心的深度学习框架。 位于 /opt/caffe。 示例位于 /opt/caffe/examples。

#### <a name="caffe2"></a>Caffe2
Caffe2 是 Facebook 基于 Caffe 构建的深度学习框架。 它可在 Conda 根环境中的 Python 2.7 中使用。 若要激活它，请从 shell 运行以下命令：

    source /anaconda/bin/activate root

可在 JupyterHub 中获取一些示例笔记本。

#### <a name="h2o"></a>H2O
H2O 是一种快速的内存中分布式机器学习和预测分析平台。 根环境和 py35 Anaconda 环境中都安装有 Python 包。 同时也会安装 R 包。 从命令行启动 H2O，运行 `java -jar /dsvm/tools/h2o/current/h2o.jar`；可能需要配置各种[命令行选项](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line)。 首选可以浏览到 http://localhost:54321 访问 Flow Web UI。 示例笔记本也位于 JupyterHub。

#### <a name="keras"></a>Keras
Keras 是 Python 中的高级神经网络 API，能够在 Tensorflow 或 Theano 上运行。 位于根环境和 py35 Python 环境中。 

#### <a name="mxnet"></a>MXNet
MXNet 是专为提高效率和灵活性而设计的深度学习框架。 具有 DSVM 上包含的 R 和 Python 绑定。 示例笔记本位于 JupyterHub 中，示例代码位于 /dsvm/samples/mxnet。

#### <a name="nvidia-digits"></a>NVIDIA DIGITS
NVIDIA 深度学习 GPU 训练系统也称为 DIGITS，该系统用于简化常见深度学习任务，比如管理数据，在 GPU 系统上设计和训练神经网络，以及实时监视高级可视化效果的性能。 

DIGITS 可用作一项服务，称为数字。 启动服务，并浏览到 http://localhost:5000 开始体验。

DIGITS 也可在 Conda 根环境中作为 Python 模块安装。

#### <a name="tensorflow"></a>TensorFlow
TensorFlow 是 Google 的深度学习库。 它是使用数据流关系图进行数值计算的开源软件库。 TensorFlow 位于 py35 Python 环境中，一些示例笔记本位于 JupyterHub 中。

#### <a name="theano"></a>Theano
Theano 是用于高效数值计算的 Python 库。 位于根环境和 py35 Python 环境中。 

#### <a name="torch"></a>Torch
Torch 是广泛支持机器学习算法的科学计算框架。 位于 /dsvm/tools/torch，交互式会话和 luarocks 程序包管理器可在命令行中使用。 示例位于 /dsvm/samples/torch。

PyTorch 也位于根 Anaconda 环境中。 示例位于 /dsvm/samples/pytorch。

### <a name="microsoft-r-server"></a>Microsoft R Server
R 是数据分析和机器学习的最常用语言之一。 如果要使用 R 进行分析，则 VM 需具有带 Microsoft R Open (MRO) 和数学内核库 (MKL) 的 Microsoft R Server (MRS)。 MKL 优化分析算法中常用的数学运算。 MRO 与 CRAN-R 100％ 兼容，在 CRAN 中发布的任何 R 库都可以安装在 MRO 上。 使用 MRS 可将 R 模型缩放和实施为 Web 服务。 可以在其中一个默认编辑器（如 RStudio、vi 或 Emacs）中编辑 R 程序。 如果使用 Emacs 编辑器，请注意，确保已预安装了 Emacs 包 ESS (Emacs Speaks Statistics)，其可简化 Emacs 编辑器中处理 R 文件的工作。

若要启动 R 控制台，只需在 shell 中键入 **R**。 执行该操作将进入交互式环境。 要开发 R 程序，通常使用 Emacs 或 vi 等编辑器，并在 R 中运行脚本。使用 RStudio，便拥有一个完整的图形 IDE 环境来开发 R 程序。

还提供一个 R 脚本，可用于安装[前 20 个 R 程序包](http://www.kdnuggets.com/2015/06/top-20-r-packages.html)（如果需要）。 此脚本可以在 R 交互式界面中运行，可以通过在 shell 中键入 **R** 来输入此脚本（如前所述）。  

### <a name="python"></a>Python
为方便使用 Python 进行开发，已安装 Anaconda Python 分发版 2.7 和 3.5。 此分发版包含基本 Python 以及约 300 种最常用的数学、工程和数据分析包。 可以使用默认文本编辑器。 此外，可以使用 Spyder，它是与 Anaconda Python 分发版捆绑在一起的 Python IDE。 Spyder 需要图形桌面或 X11 转发。 图形桌面中提供了 Spyder 的快捷方式。

由于同时拥有 Python 2.7 和 Python 3.5，因此需专门激活要在当前会话中使用的所需 Python 版本（conda 环境）。 激活过程会将 PATH 变量设置为所需的 Python 版本。

若要激活 Python 2.7 conda 环境，请从 shell 运行以下命令：

    source /anaconda/bin/activate root

Python 2.7 安装在 */anaconda/bin* 中。

若要激活 Python 3.5 conda 环境，请从 shell 运行以下命令：

    source /anaconda/bin/activate py35


Python 3.5 安装在 */anaconda/envs/py35/bin* 中。

若要调用 Python 交互式会话，只需在 shell 中键入 **python**。 如果在图形界面上或已设置 X11 转发，则可以通过键入 **pycharm** 启动 PyCharm Python IDE。

若要安装其他 Python 库，需要在 sudo 下运行 ```conda``` 或 ````pip```` 命令，并提供 Python 包管理器（conda 或 pip）的完整路径，以便安装到正确的 Python 环境。 例如：

    sudo /anaconda/bin/pip install <package> #for Python 2.7 environment
    sudo /anaconda/envs/py35/bin/pip install <package> # for Python 3.5 environment


### <a name="jupyter-notebook"></a>Jupyter 笔记本
Anaconda 分发版还附带 Jupyter 笔记本 - 用于共享代码和分析的环境。 可通过 JupyterHub 访问 Jupyter notebook。 使用本地 Linux 用户名和密码登录。

已使用 Python 2、Python 3 和 R 内核预配置 Jupyter 笔记本服务器。 可通过名为“Jupyter Notebook”的桌面图标启动浏览器，以访问该笔记本服务器。 如果通过 SSH 或 X2Go 客户端登录 VM，则还可以通过访问 [https://localhost:8000/](https://localhost:8000/) 来访问 Jupyter 笔记本服务器。

> [!NOTE]
> 如果收到任何证书警告，请选择继续。
> 
> 

可以从任何主机访问 Jupyter 笔记本服务器。 只需键入 *https://\<VM DNS name or IP Address\>:8000/*

> [!NOTE]
> 默认情况下，配置 VM 时，防火墙中会打开端口 8000。
> 
> 

我们已经打包了两个示例笔记本（分别在 Python 和 R 中）。通过使用本地 Linux 用户名和密码向 Jupyter 笔记本进行身份验证后，可以在笔记本主页上看到示例链接。 通过选择“新建”并选择相应的语言内核，可创建新笔记本。 如果没有看到“新建”按钮，请点击左上角的“Jupyter”图标转到笔记本服务器的主页。

### <a name="apache-spark-standalone"></a>Apache Spark Standalone 
Apache Spark 的独立实例预安装在 Linux DSVM 上，帮助用户先在本地开发 Spark 应用程序，再在大型群集上进行测试和部署。 可以通过 Jupyter 内核运行 PySpark 程序。 打开 Jupyter 并单击“新建”按钮时，将看到可用内核的列表。 “Spark - Python”是 PySpark 内核，可让你使用 Python 语言生成 Spark 应用程序。 还可以使用 Python IDE（如 PyCharm 或 Spyder）生成 Spark 程序。 由于这是独立实例，因此 Spark 堆栈会在调用方客户端程序中运行。 与在 Spark 群集上开发相比，这样可以更快、更轻松地排查问题。 

Jupyter 上提供了一个示例 PySpark 笔记本，该笔记本可以在 Jupyter 主目录下的“SparkML”目录 ($HOME/notebooks/SparkML/pySpark) 中找到。 

如果要用 R for Spark 编程，可以使用 Microsoft R Server、SparkR 或 sparklyr。 

在 Microsoft R Server 的 Spark 上下文中运行之前，需要执行一次性设置步骤以启用本地单节点 Hadoop HDFS 和 Yarn 实例。 默认情况下，Hadoop 服务已安装但在 DSVM 上禁用。 若要启用它，需要首次以 root 身份运行以下命令：

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

不需要 Hadoop 相关服务时，可以通过运行 ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` 停止这些服务。演示如何在远程 Spark 上下文（即，DSVM 上的独立 Spark 实例）中开发和测试 MRS 的示例在 `/dsvm/samples/MRS` 目录中提供。 

### <a name="ides-and-editors"></a>IDE 和编辑器
可以选择多个代码编辑器。 这包括 vi/VIM、Emacs、PyCharm、RStudio 和 IntelliJ。 IntelliJ、RStudio 和 PyCharm 是图形编辑器，需登录图形桌面才能使用。 这些编辑器具有用以启动的桌面和应用程序菜单快捷方式。

**VIM** 和 **Emacs** 是基于文本的编辑器。 Emacs 上已安装名为 Emacs Speaks Statistics (ESS) 的附加包，使得在 Emacs 编辑器中使用 R 更轻松。 可在 [ESS](http://ess.r-project.org/) 了找到更多信息。

**LaTex** 通过 texlive 包和 Emacs 外接程序 [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) 包进行安装，这简化了在 Emacs 中创作 LaTex 文档的过程。  

### <a name="databases"></a>数据库

#### <a name="graphical-sql-client"></a>图形化 SQL 客户端
SQuirrel SQL 是一个图形化 SQL 客户端，用于连接到不同的数据库（如 Microsoft SQL Server 和 MySQL）和运行 SQL 查询。 可从图形桌面会话运行（例如使用 X2Go 客户端）。 要调用 SQuirrel SQL，可通过桌面上的图标将其启动，或者在 shell 上运行以下命令。

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

首次使用前，需设置驱动程序和数据库别名。 JDBC 驱动程序位于：

*/usr/share/java/jdbcdrivers*

有关详细信息，请参阅 [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)。

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>用于访问 Microsoft SQL Server 的命令行工具
SQL Server 的 ODBC 驱动程序包还附带两个命令行工具：

**bcp**：bcp 实用工具在 Microsoft SQL Server 实例与用户指定格式的数据文件之间批量复制数据。 bcp 实用工具可用于将大量新行导入 SQL Server 表，或将表中的数据导出到数据文件。 要将数据导入表，必须使用为该表创建的格式文件，或了解表的结构以及对其列有效的数据类型。

有关详细信息，请参阅[使用 bcp 连接](https://msdn.microsoft.com/library/hh568446.aspx)。

**sqlcmd**：可以在命令提示符中，使用 sqlcmd 实用工具输入 Transact-SQL 语句以及系统过程和脚本文件。 此实用工具使用 ODBC 执行 Transact-SQL 批处理。

有关详细信息，请参阅[使用 sqlcmd 连接](https://msdn.microsoft.com/library/hh568447.aspx)。

> [!NOTE]
> 此实用工具在 Linux 和 Windows 平台之间存在差异。 有关详细信息，请参阅文档。
> 
> 

#### <a name="database-access-libraries"></a>数据库访问库
R 和 Python 中提供可访问数据库的库。

* 在 R 中，**RODBC** 包或 **dplyr** 包允许在数据库服务器上查询或执行 SQL 语句。
* 在 Python 中，**pyodbc** 库提供使用 ODBC 作为基础层的数据库访问。  

### <a name="azure-tools"></a>Azure 工具
VM 上安装有以下 Azure 工具：

* **Azure 命令行接口**：Azure CLI 允许通过 shell 命令创建和管理 Azure 资源。 若要调用 Azure 工具，只需键入 **azure 帮助**。 有关详细信息，请参阅 [Azure CLI 文档页](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。
* **Microsoft Azure 存储资源管理器**：Microsoft Azure 存储资源管理器是一个图形工具，用于浏览在 Azure 存储帐户中存储的对象，以及将数据上传到 Azure Blob 和从中下载数据。 可通过桌面快捷方式图标访问存储资源管理器。 可以通过键入 **StorageExplorer** 从 shell 提示符调用。 需从 X2Go 客户端登录，或者安装 X11 转发。
* **Azure 库**：以下是一些预安装库。
  
  * **Python**： Python 中的已安装 Azure 相关库包括 **azure****azureml**、**pydocumentdb** 和 **pyodbc**。 使用前三个库，可以访问 Azure 存储服务、Azure 机器学习和 Azure Cosmos DB（Azure 上的 NoSQL 数据库）。 使用第四个库 pyodbc（以及 SQL Server 的 Microsoft ODBC 驱动程序），可以通过使用 ODBC 接口从 Python 访问 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库。 输入 **pip 列表**查看所有列出的库。 请确保在 Python 2.7 和 3.5 环境中都运行此命令。
  * **R**：R 中的已安装 Azure 相关库包括 **AzureML** 和 **RODBC**。
  * **Java**：可在 VM 上的 **/dsvm/sdk/AzureSDKJava** 目录中找到 Azure Java 库列表。 密钥库是 Azure 存储和用于 SQL Server 的管理 API、Azure Cosmos DB 和 JDBC 驱动程序。  

可以从预安装的 Firefox 浏览器访问 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中，可以创建、管理和监视 Azure 资源。

### <a name="azure-machine-learning"></a>Azure 机器学习
Azure 机器学习是完全托管的云服务，允许构建、部署和共享预测分析解决方案。 从 Azure 机器学习工作室中构建实验和模型。 可从数据科学虚拟机上的 Web 浏览器，通过访问 [Microsoft Azure 机器学习](https://studio.azureml.net)来访问 Azure 机器学习工作室。

登录 Azure 机器学习工作室后，可以访问实验画布，可在其中生成机器学习算法的逻辑流。 还可以访问在 Azure 机器学习上托管的 Jupyter 笔记本，并且可以无缝使用机器学习工作室中的实验。 通过将已构建的机器学习模型包装在 Web 服务接口中，来对它们执行操作。 这使得以任何语言编写的客户端都能从机器学习模型中调用预测。 有关详细信息，请参阅[机器学习文档](https://azure.microsoft.com/documentation/services/machine-learning/)。

还可以在 VM 上的 R 或 Python 中构建模型，并在 Azure 机器学习上将其部署到生产中。 我们已在 R (**AzureML**) 和 Python (**azureml**) 中分别安装了库以启用此功能。

若要深入了解如何将 R 和 Python 中的模型部署到 Azure 机器学习，请参阅[可在数据科学虚拟机上执行的十项操作](machine-learning-data-science-vm-do-ten-things.md)（特别是“使用 R 或 Python 构建模型并使用 Azure 机器学习对其进行操作”部分）。

> [!NOTE]
> 这些说明专为 Windows 版本的数据科学 VM 编写。 但是其中提供的有关将模型部署到 Azure 机器学习的信息也适用于 Linux VM。
> 
> 

### <a name="machine-learning-tools"></a>机器学习工具
VM 附带一些已经预编译并已在本地预安装的机器学习工具和算法。 其中包括：

* **Vowpal Wabbit**：快速在线学习算法。
* **xgboost**：提供经过优化的提升树算法的工具。
* Rattle：基于 R 的图形工具，可用于简单的数据浏览和建模。
* **Python**：Anaconda Python 附带机器学习算法，这些算法含有库（如 Scikit-learn）。 可以通过使用 `pip install` 命令安装其他库。
* LightGBM：快速、分布式、高性能的梯度提升框架，基于决策树算法。
* **R**：有可供 R 使用的丰富的机器学习函数库。一些预安装的库包括lm、glm、randomForest 和 rpart。 可通过运行以下内容安装其他库：
  
        install.packages(<lib name>)

以下是一些关于列表中前三个机器学习工具的附加信息。

#### <a name="vowpal-wabbit"></a>Vowpal Wabbit
Vowpal Wabbit 是一种使用在线、哈希、allreduce、缩减、learning2search、主动和交互式学习等技术的机器学习系统。

若要在非常基础的示例上运行该工具，请执行以下操作：

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

该目录中存在其他更大的演示。 有关 VW 的详细信息，请参阅 [GitHub 的本部分内容](https://github.com/JohnLangford/vowpal_wabbit)，以及 [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)。

#### <a name="xgboost"></a>xgboost
这是一个为提升（树）算法设计和优化的库。 此库的目标是将计算机的计算限制推向极致，以满足提供可缩放、可移植且精确的大规模树提升的需求。

它作为命令行及 R 库提供。

要在 R 中使用此库，可以启动交互式 R 会话（只需在 shell 中键入**R**），并加载库。

下面是可以在 R 提示符中运行的一个简单示例：

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

若要运行 xgboost 命令行，下面是要在 shell 中执行的命令：

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


将 .Model 文件写入到指定目录。 有关此演示示例的信息，可在 [GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification) 上了解。

有关 xgboost 的详细信息，请参阅 [xgboost 文档页](https://xgboost.readthedocs.org/en/latest/)及其 [GitHub 存储库](https://github.com/dmlc/xgboost)。

#### <a name="rattle"></a>Rattle
Rattle (**R** **A**nalytical **T**ool **T**o **L**earn **E**asily) 使用基于 GUI 的数据浏览和建模。 它提供数据的统计和可视化摘要，转换可轻松建模的数据，从数据构建不受监督和受监督的模型，以图形方式呈现模型的性能，以及对新数据集进行评分。 它还生成 R 代码，用于复制 UI 中可直接在 R 中运行或用作进一步分析的起点的操作。

若要运行 Rattle，需进入图形桌面登录会话。 在终端上键入 ```R```，进入 R 环境。 在 R 提示符中，输入以下命令：

    library(rattle)
    rattle()

现在，将打开具有一组选项卡的图形界面。 下面是 Rattle 中使用示例天气数据集并构建模型所需的快速入门步骤。 以下某些步骤中，系统会提示自动安装并加载尚未安装在系统上的某些必需 R 包。

> [!NOTE]
> 如果无权在系统目录（默认）中安装包，可能会在 R 控制台窗口中看到一个提示，提醒将包安装到个人库中。 如果看到这些提示，请回复 y。
> 
> 

1. 单击“执行” 。
2. 这会弹出一个对话框，询问是否要使用示例气象数据集。 单击“是”加载示例。
3. 单击“模型”选项卡。
4. 单击“执行”生成决策树。
5. 单击“绘制”显示决策树。
6. 单击“林”单选按钮，并单击“执行”生成随机林。
7. 单击“评估”选项卡。
8. 单击“风险”单选按钮，并单击“执行”，以显示两个风险（累积）性能绘图。
9. 单击“日志”选项卡，显示上述操作生成的 R 代码。
   （由于当前版本 Rattle 中的 bug，需在日志文本中的“导出此日志...”前插入 *#* 字符。）
10. 单击“导出”按钮，将名为 *weather_script.R* 的 R 脚本文件保存到主文件夹。

可以退出 Rattle 和 R。现在，可以修改生成的 R 脚本或者直接使用，可使其随时运行以重复在 Rattle UI 中完成的所有操作。 特别是对于 R 的初学者而言，这是一种简单方法，可用于在图形界面中快速进行分析和机器学习，同时在 R 中自动生成可修改和/或用于学习的代码。

## <a name="next-steps"></a>后续步骤
以下是继续学习和探索的方法：

* [适用于 Linux 的数据科学虚拟机上的数据科学](machine-learning-data-science-linux-dsvm-walkthrough.md)演练展示了如何使用此处预配的 Linux 数据科学 VM 执行数个常见的数据科学任务。 
* 通过尝试本文中介绍的工具，探索数据科学 VM 上的各种数据科学工具。 还可以在虚拟机内的 shell 上运行 *dsvm-more-info*，获取有关 VM 上安装的工具的基本介绍和详细信息。  
* 通过使用 [Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)，了解如何系统地构建端到端分析解决方案。
* 若要了解使用 Cortana Analytics 套件的机器学习和数据分析示例，请访问 [Cortana Analytics 库](http://gallery.cortanaanalytics.com)。


