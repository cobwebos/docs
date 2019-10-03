---
title: 支持的数据平台
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Azure Data Science Virtual Machine 支持的数据平台和工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 5dbaf969420f066698a07b8d137d2ba44fc99080
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208131"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Data Science Virtual Machine 支持的数据平台

使用 Data Science Virtual Machine (DSVM), 你可以针对各种数据平台构建你的分析。 除远程数据平台接口外，DSVM 还提供用于快速开发和原型制作的本地实例。

DSVM 支持以下数据平台工具。

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| 它是什么？   | 本地关系数据库实例      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 使用小型数据集在本地进行快速开发 <br/> 运行数据库内 R   |
| 指向示例的链接      |    将纽约数据集的一个小示例加载到 SQL 数据库中:<br/>  `nyctaxi` <br/> 可在以下位置找到 Microsoft Machine Learning Server 和数据库内分析的 Jupyter 示例:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 上的相关工具       | SQL Server Management Studio <br/> ODBC/JDBC 驱动程序<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer Edition 只能用于开发和测试目的。 需要许可证或一个 SQL Server VM 才能在生产中运行。


### <a name="setup"></a>安装

数据库服务器已预先配置, 与 SQL Server 相关的 Windows 服务 (如`SQL Server (MSSQLSERVER)`) 设置为自动运行。 唯一的手动步骤涉及使用 Microsoft Machine Learning Server 启用数据库内分析。 为此, 可以运行以下命令作为 SQL Server Management Studio (SSMS) 中的一次性操作。 以计算机管理员身份登录后运行此命令, 在 SSMS 中打开一个新查询, 并确保所选数据库是`master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
若要运行 SQL Server Management Studio, 可以在程序列表中搜索 "SQL Server Management Studio", 或使用 Windows Search 查找并运行该程序。 系统提示输入凭据时, 请选择 " **Windows 身份验证**", ```localhost```并使用 "计算机名称" 或 " **SQL Server 名称**" 字段。

### <a name="how-to-use-and-run-it"></a>如何使用和运行它

默认情况下, 具有默认数据库实例的数据库服务器会自动运行。 可在 VM 上使用 SQL Server Management Studio 等工具在本地访问 SQL Server 数据库。 本地管理员帐户对数据库具有管理员访问权限。

此外, DSVM 附带了 ODBC 和 JDBC 驱动程序, 可与通过多种语言 (包括 Python 和 Machine Learning Server) 编写的应用程序进行 SQL Server 通信。

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上配置并安装它？ 

 SQL Server 以标准方式安装。 可在 `C:\Program Files\Microsoft SQL Server` 中找到它。 在中找到`C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`数据库内 Machine Learning Server 实例。 DSVM 还具有单独的独立 Machine Learning Server 实例, 该实例安装在`C:\Program Files\Microsoft\R Server\R_SERVER`中。 这两个 Machine Learning Server 实例不共享库。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (Standalone)

| | |
| ------------- | ------------- |
| 它是什么？   | 常用 Apache Spark 平台的独立 (单节点进程内) 实例;用于快速、大规模数据处理和机器学习的系统     |
| 支持的 DSVM 版本      | Linux <br /> Windows（试验）      |
| 典型用途      | * 使用较小的数据集在本地快速开发 Spark/PySpark 应用程序, 并在较大的 Spark 群集 (例如 Azure HDInsight) 上进行部署<br/> * 测试 Microsoft Machine Learning Server Spark 上下文 <br />* 使用 SparkML 或 Microsoft 的开源[MMLSpark](https://github.com/Azure/mmlspark)库来构建 ML 应用程序 |
| 指向示例的链接      |    Jupyter 示例： <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (Spark 上下文):/dsvm/samples/MRS/MRSSparkContextSample。R |
| DSVM 上的相关工具       | PySpark、Scala<br/>Jupyter（Spark/PySpark 内核）<br/>Microsoft Machine Learning Server、SparkR、Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>如何使用
可以通过运行`spark-submit`或`pyspark`命令在命令行上提交 Spark 作业。 还可通过使用 Spark 内核新建笔记本来创建 Jupyter 笔记本。

可以通过使用 DSVM 上提供的 SparkR、Sparklyr 和 Microsoft Machine Learning Server 库, 从 R 使用 Spark。 请参阅上表中的示例链接。

> [!NOTE]
> 仅在 Ubuntu Linux DSVM 版本上支持在 DSVM 的 Spark 上下文中运行 Microsoft Machine Learning Server。



### <a name="setup"></a>安装
在 Ubuntu Linux DSVM edition Microsoft Machine Learning Server 上的 Spark 上下文中运行之前, 必须完成一次性安装步骤, 才能启用本地单节点 Hadoop HDFS 和 Yarn 实例。 默认情况下，Hadoop 服务已安装但在 DSVM 上禁用。 若要启用它们, 请在第一次运行以下命令:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

如果不再需要 Hadoop 相关服务, 可以通过运行```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```将它们停止。

此示例演示如何在远程 Spark 上下文 (即 DSVM 上的独立 Spark 实例) 中开发和测试 MRS, 并在`/dsvm/samples/MRS`目录中提供。


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>如何在 DSVM 上配置并安装它？ 
|平台|安装位置 ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


用于从 Azure Blob 存储或 Azure Data Lake Storage 中使用 Microsoft MMLSpark 机器学习库访问数据的库预安装在 $SPARK 给家里打电话/jar 中。 Spark 启动时，这些 JAR 会自动加载。 默认情况下, Spark 使用本地磁盘上的数据。 

对于 DSVM 上的 Spark 实例, 若要访问 Blob 存储或 Azure Data Lake Storage 中存储的数据, 则必须基于 $SPARK `core-site.xml`给家里打电话//core-site.xml 中找到的模板来创建和配置文件。 你还必须具有相应的凭据才能访问 Blob 存储和 Azure Data Lake Storage。 (请注意, 模板文件使用 Blob 存储的占位符, 并 Azure Data Lake Storage 配置。)

有关创建 Azure Data Lake Storage 服务凭据的详细信息, 请参阅[Azure Data Lake Storage Gen1 身份验证](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)。 在 core-site.xml 文件中输入 Blob 存储或 Azure Data Lake Storage 的凭据后, 可以通过 wasb://或 adl://的 URI 前缀引用存储在这些源中的数据。

