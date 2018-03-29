---
title: 用于数据科学团队项目的平台和工具 - Azure | Microsoft Docs
description: 列举并讨论基于 Team Data Science Process 实现标准化的企业可用的数据和分析资源。
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev
ms.openlocfilehash: 404e3dd106edf82f4f22e4c6a17987bd0bc51f65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="platforms-and-tools-for-data-science-team-projects"></a>用于数据科学团队项目的平台和工具

Microsoft 为云或本地平台提供整套数据和分析服务与资源。 部署这些服务和资源可让数据科学项目的执行变得有效且可缩放。 [Team Data Science Process](overview.md) (TDSP) 为团队以可跟踪、版本受控和协作的方式实施数据科学项目提供指导。  有关致力于标准化此流程的数据科学团队要处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。

可让数据科学团队配合 TDSP 使用的数据和分析服务包括：

- 数据科学虚拟机（Windows 和 Linux CentOS）
- HDInsight Spark 群集
- SQL 数据仓库
- Azure Data Lake
- HDInsight Hive 群集
- Azure 文件存储
- SQL Server 2016 R 服务

本文档简要介绍上述资源，并提供 TDSP 团队发布的教程和演练的链接。 可以借助这些参考材料了解如何逐步使用这些资源，并开始使用它们来生成智能应用程序。 这些资源的产品页上提供了其详细信息。 

## <a name="data-science-virtual-machine-dsvm"></a>数据科学虚拟机 (DSVM)

Microsoft 在 Windows 和 Linux 上提供的数据科学虚拟机包含用于数据科学建模与开发活动的热门工具。 这些工具包括：

- Microsoft R Server Developer Edition 
- Enthought Python 分发版
- 用于 Python 和 R 的 Jupyter Notebook 
- Windows/Eclipse on Linux 上包含 Python 和 R 工具的 Visual Studio Community Edition
- 适用于 Windows 的 Power BI Desktop
- Windows/Postgres on Linux 上的 SQL Server 2016 Developer Edition

此外，还包括 **ML 和 AI 工具**，例如 CNTK（Microsoft 开发的开源深度学习工具包）、xgboost、mxnet 和 Vowpal Wabbit。

DSVM 目前可在 **Windows** 和 **Linux CentOS** 操作系统中使用。 根据计划在其上执行的数据科学项目的需求，选择 DSVM 的大小（CPU 核心数和内存量）。 

有关 Windows 版 DSVM 的详细信息，请参阅 Azure Marketplace 中的 [Microsoft 数据科学虚拟机](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/)。 有关 Linux 版 DSVM，请参阅 [Linux 数据科学虚拟机](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/)。

若要了解如何在 DSVM 上有效执行某些常见的数据科学任务，请参阅[数据科学虚拟机的十大功能](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark 群集

Apache Spark 是一种开源并行处理框架，支持使用内存中处理来提升大数据分析应用程序的性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中计算功能使其成为机器学习和图形计算中的迭代算法的最佳选择。 Spark 也能与 Azure Blob 存储 (WASB) 兼容，因此可以轻松使用 Spark 处理存储在 Azure 中的现有数据。

在 HDInsight 中创建 Spark 群集时，即会创建已安装并配置了 Spark 的 Azure 计算资源。 在 HDInsight 中创建 Spark 群集需要约 10 分钟。 将要处理的数据存储在 Azure Blob 存储中。 有关在群集中使用 Azure Blob 存储的信息，请参阅[将 HDFS 兼容的 Azure Blob 存储与 HDInsight 中的 Hadoop 配合使用](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)。

Microsoft 的 TDSP 团队发布了两篇端到端演练，介绍如何使用 Azure HDInsight Spark 群集生成数据科学解决方案，其中一个解决方案使用 Python，另一个使用 Scala。 有关 Azure HDInsight **Spark 群集**的详细信息，请参阅[概述：HDInsight Linux 上的 Apache Spark](../../hdinsight/spark/apache-spark-overview.md)。 若要了解如何在 Azure HDInsight Spark 群集上使用 **Python** 生成数据科学解决方案，请参阅[有关在 Azure HDInsight 上使用 Spark 展开数据科学的概述](spark-overview.md)。 若要了解如何在 Azure HDInsight Spark 群集上使用 **Scala** 生成数据科学解决方案，请参阅[在 Azure 上使用 Scala 和 Spark 展开数据科学](scala-walkthrough.md)。 


##  <a name="azure-sql-data-warehouse"></a>Azure SQL 数据仓库

使用 Azure SQL 数据仓库可以在片刻之间轻松缩放计算资源，无需过度预配或过度付费。 此外，SQL 数据仓库提供暂停使用计算资源的独特选项，为我们赋予更大的自由以更好地管理云成本。 部署可缩放计算资源的能力使我们能够将所有数据放入 Azure SQL 数据仓库。 存储成本低廉，只需针对想要分析的数据集部分运行计算资源。 

有关 Azure SQL 数据仓库的详细信息，请参阅 [SQL 数据仓库](https://azure.microsoft.com/services/sql-data-warehouse)网站。 若要了解如何使用 SQL 数据仓库生成端到端高级分析解决方案，请参阅[运行中的 Team Data Science Process：使用 SQL 数据仓库 ](sqldw-walkthrough.md)。


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake 是在施加任何正式要求或架构之前，在单个位置收集的每种数据的企业级存储库。 这种灵活性允许将每种数据保留在 Data Lake 中，而不管数据的大小或结构或引入速度如何。 然后，组织可以使用 Hadoop 或高级分析功能在这些 Data Lake 中查找模式。 在策划数据并其转移到数据仓库之前，Data Lake 还可以充当较低成本的数据准备工作的存储库。

有关 Azure Data Lake 的详细信息，请参阅 [Azure Data Lake 简介](https://azure.microsoft.com/blog/introducing-azure-data-lake/)。 若要了解如何使用 Azure Data Lake 生成可缩放的端到端数据科学解决方案，请参阅[Azure Data Lake 中可缩放的数据科学：端到端演练](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) 群集

Apache Hive 是适用于 Hadoop 的数据仓库系统，可让你使用 HiveQL（类似于 SQL 的查询语言）来进行数据汇总、查询和数据分析。 使用 Hive 能够以交互方式浏览数据，或者创建可重用的批处理作业。

Hive 允许在很大程度上未结构化的数据上投影结构。 定义结构后，可以使用 Hive 在 Hadoop 群集中查询这些数据，而无需使用甚至无需了解 Java 或 MapReduce。 借助 HiveQL（Hive 查询语言）可以使用类似于 T-SQL 的语句编写查询。

对于数据科学家而言，Hive 可以在 Hive 查询中运行 Python 用户定义的函数 (UDF) 来处理记录。 这种能力大大扩展了数据分析中的 Hive 查询功能。 具体而言，Hive 可让数据科学家使用他们最熟悉的语言（包括类似于 SQL 的 HiveQL 和 Python）开展可缩放的特征工程。 

有关 Azure HDInsight Hive 群集的详细信息，请参阅[在 HDInsight 中将 Hive 和 HiveQL 与 Hadoop 配合使用](../../hdinsight/hadoop/hdinsight-use-hive.md)。 若要了解如何使用 Azure HDInsight Hive 群集生成可缩放的端到端数据科学解决方案，请参阅[运行中的 Team Data Science Process：使用 HDInsight Hadoop 群集](hive-walkthrough.md)。


## <a name="azure-file-storage"></a>Azure 文件存储 

Azure 文件存储是一种使用标准服务器消息块 (SMB) 协议在云中提供文件共享的服务。 支持 SMB 2.1 和 SMB 3.0。 通过 Azure 文件存储，可以将依赖文件共享的旧版应用程序快速迁移到 Azure 且无成本高昂的重写。 在 Azure 虚拟机或云服务中或者从本地客户端运行的应用程序可以在云中装载文件共享，就像桌面应用程序装载典型的 SMB 共享一样。 之后，任意数量的应用程序组件可以装载并同时访问文件存储共享。

能够创建一个 Azure 文件存储作为与项目团队成员共享项目数据的位置，对于数据科学项目特别有用。 然后，每个成员可以访问 Azure 文件存储中的相同数据副本。 他们还可使用此文件存储来共享执行项目期间生成的特征集。 如果项目是客户参与项目，则客户可以在其自己的 Azure 订阅下创建一个 Azure 文件存储，用来与你共享项目数据和特征。 这样，客户便可以完全控制项目数据资产。 有关 Azure 文件存储的详细信息，请参阅 [在 Windows 上开始使用 Azure 文件存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files)和[如何通过 Linux 使用 Azure 文件存储](../../storage/files/storage-how-to-use-files-linux.md)。


## <a name="sql-server-2016-r-services"></a>SQL Server 2016 R 服务

R 服务（数据库内部）提供一个平台用于开发和部署可以发现新见解的智能应用程序。 可以使用丰富强大的 R 语言（包括 R 社区提供的许多包）来创建模型，基于 SQL Server 数据生成预测。 由于 R 服务（数据库内部）可将 R 语言与 SQL Server 集成，因此可以保持与数据接近的分析结果，同时消除与数据移动相关的成本和安全风险。

R 服务（数据库内部）凭借一套综合性的 SQL Server 工具和技术来支持开源 R 语言。 它们提供优异的性能、安全性、可靠性和可管理性。 可以使用便捷、熟悉的工具部署 R 解决方案。 生产应用程序可以使用 Transact-SQL 调用 R 运行时以及检索预测数据和视觉对象。 还可以使用 ScaleR 库来改善 R 解决方案的缩放性和性能。 有关详细信息，请参阅 [SQL Server R 服务](https://msdn.microsoft.com/library/mt604845.aspx)。

Microsoft 的 TDSP 团队发布了两篇端到端演练，介绍如何在 SQL Server 2016 R 服务中生成数据科学解决方案：一篇面向 R 程序员，另一篇面向 SQL 开发人员。 **R 程序员**可参阅[数据科学端到端演练](https://msdn.microsoft.com/library/mt612857.aspx)。 **SQL 开发人员**可参阅[面向 SQL 开发人员的数据库内部高级分析（教程）](https://msdn.microsoft.com/library/mt683480.aspx)。


## <a name="appendix"></a>附录：用于设置数据科学项目的工具

### <a name="install-git-credential-manager-on-windows"></a>在 Windows 上安装 Git 凭据管理器

如果在 **Windows** 上遵循 TDSP，需要安装 **Git 凭据管理器 (GCM)** 来与 Git 存储库通信。 若要安装 GCM，首先需要安装 **Chocolaty**。 若要安装 Chocolaty 和 GCM，请在 Windows PowerShell 中以**管理员**身份运行以下命令：  

    iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
    choco install git-credential-manager-for-windows -y
    

### <a name="install-git-on-linux-centos-machines"></a>在 Linux (CentOS) 计算机上安装 Git

运行以下 bash 命令，在 Linux (CentOS) 计算机上安装 Git：

    sudo yum install git


### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>在 Linux (CentOS) 计算机上生成公共 SSH 密钥

如果使用 Linux (CentOS) 计算机运行 git 命令，需要将计算机的公共 SSH 密钥添加到 VSTS 服务器，使 VSTS 服务器能够识别此计算机。 首先，需要生成公共 SSH 密钥，并在 VSTS 安全设置页中将该密钥添加到 SSH 公钥。 

- 若要生成 SSH 密钥，请运行以下两条命令： 

        ssh-keygen
        cat .ssh/id_rsa.pub

![](./media/platforms-and-tools/resources-1-generate_ssh.png)

- 复制整个 SSH 密钥，包括 *ssh-rsa*。 
- 登录到 VSTS 服务器。 
- 单击页面右上角的“<你的姓名\>”，再单击“安全性”。 
    
    ![](./media/platforms-and-tools/resources-2-user-setting.png)

- 依次单击“SSH 公钥”、“+添加”。 

    ![](./media/platforms-and-tools/resources-3-add-ssh.png)

- 将刚刚复制的 SSH 密钥粘贴到文本框中并保存。


## <a name="next-steps"></a>后续步骤

还提供了完整的、端到端的演练，演示**特定方案**处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关在团队数据科学过程中执行使用 Azure 机器学习工作室的步骤的示例，请参阅[使用 Azure ML](http://aka.ms/datascienceprocess) 学习路径。