---
title: 数据科学虚拟机的数据平台 - Azure | Microsoft Docs
description: 数据科学虚拟机的数据平台。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: b3f340006801287383c2afb2924706affbd77a51
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="data-platforms"></a>数据平台

借助数据科学虚拟机 (DSVM)，可生成针对各种数据平台的分析。 除远程数据平台接口外，DSVM 还提供用于快速开发和原型制作的本地实例。 

以下数据平台工具在 DSVM 上受到支持。 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| 它是什么？   | 本地关系数据库实例      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 使用小型数据集在本地进行快速开发 <br/> 运行数据库内 R   |
| 示例链接      |    New York City 数据集的小型示例在 SQL 数据库 `nyctaxi` 中加载。 <br/> 可在以下位置找到显示 Microsoft R 和数据库内分析的 Jupyter 示例：<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| DSVM 上的相关工具       | SQL Server Management Studio <br/> ODBC/JDBC 驱动程序<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer Edition 只能用于开发和测试。 需要许可证或一个 SQL Server VM 才能在生产中运行。 


### <a name="setup"></a>设置

数据库服务器已预先配置，与 SQL Server 相关的 Windows 服务（例如 `SQL Server (MSSQLSERVER)`）设置为自动运行。 唯一需要手动执行的步骤是使用 Microsoft R 启用数据库内分析。在 SQL Server Management Studio (SSMS) 中作为一次性操作运行以下命令即可，方法是以计算机管理员身份登录后，在 SSMS 中打开“新建查询”，确保所选数据库为 `master`，然后运行： 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
若要运行 SQL Server Management Studio，可在程序列表中搜索“SQL Server Management Studio”，或使用 Windows 搜索来查找并运行它。 系统提示输入凭据时，选择“Windows 身份验证”，然后使用计算机名称或 SQL Server 名称中的 ```localhost```。 

### <a name="how-to-use--run-it"></a>如何使用/运行它？  

默认情况下，自动运行具有默认数据库实例的数据库服务器。 可在 VM 上使用 SQL Server Management Studio 等工具在本地访问 SQL Server 数据库。 本地管理员帐户在数据库中拥有管理员访问权限。 

ODBC 驱动程序和 JDBC 驱动程序随附的 DSVM 还会通过使用多种语言（包括 Python、R）编写的应用程序与 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库通信。 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>如何在 DSVM 上配置/安装它？ 

SQL Server 采用标准方式安装。 可在 `C:\Program Files\Microsoft SQL Server` 中找到它。 数据库内 R 实例位于 `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`。 DSVM 还拥有在 `C:\Program Files\Microsoft\R Server\R_SERVER` 中安装的独立 R Server 实例。 这两个 R 实例不共享库。


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (Standalone)

| | |
| ------------- | ------------- |
| 它是什么？   | 常用 Apache Spark 平台的独立（单个进程内节点）实例，用于快速大规模数据处理和机器学习的系统     |
| 支持的 DSVM 版本      | Linux <br /> Windows（试验）      |
| 典型用途      | *使用小型数据集在本地快速开发 Spark/PySpark 应用程序，稍后在大型 Spark 群集（比如 Azure HDInsight）上部署<br/> *测试 Microsoft R Server Spark 上下文 <br />*使用 SparkML 或 Microsoft 的开源 [MMLSpark](https://github.com/Azure/mmlspark) 库来生成 ML 应用程序  |
| 示例链接      |    Jupyter 示例： <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server（Spark 上下文）：/dsvm/samples/MRS/MRSSparkContextSample.R |
| DSVM 上的相关工具       | PySpark、Scala<br/>Jupyter（Spark/PySpark 内核）<br/>Microsoft R Server、SparkR、Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>如何使用
可通过使用 `spark-submit` 或 `pyspark` 命令在命令行中提交 Spark 作业来运行 Spark。 还可通过使用 Spark 内核新建笔记本来创建 Jupyter 笔记本。 

可使用 DSVM 上可用的库（比如 SparkR、Sparklyr 或 Microsoft R Server）来通过 R 使用 Spark。 请参阅上表中的示例链接。 

> [!NOTE]
> 仅在 Ubuntu Linux DSVM 版本上支持在 DSVM 的 Spark 上下文中运行 Microsoft R Server。 



### <a name="setup"></a>设置
在 Ubuntu Linux DSVM 版本上在 Microsoft R Server 的 Spark 上下文中运行前，需要执行一次性设置步骤来启用本地单节点 Hadoop HDFS 和 Yarn 实例。 默认情况下，Hadoop 服务已安装但在 DSVM 上禁用。 若要启用它，需要首次以 root 身份运行以下命令：

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

不需要 Hadoop 相关服务时，可通过运行 ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```` 停止这些服务。演示如何在远程 Spark 上下文（即 DSVM 上的独立 Spark 实例）中开发和测试 MRS 的示例在 `/dsvm/samples/MRS` 目录中提供。 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>如何在 DSVM 上配置/安装它？ 
|平台|安装位置 ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


用于通过 Azure Blob 或 Azure Data Lake 存储 (ADLS) 访问数据并使用 Microsoft MMLSpark 机器学习库的库已在 $SPARK_HOME/jars 中预先安装。 Spark 启动时，这些 JAR 会自动加载。 默认情况下，Spark 使用本地磁盘上的数据。 为了让 DSVM 上的 Spark 实例能够访问存储在 Azure blob 或 ADLS 上的数据，需要根据 $SPARK_HOME/conf/core-site.xml.template（其中有用于 Blob 和 ADLS 配置的占位符）中找到的模板，使用 Azure blob 和 Azure Data Lake 存储的正确凭据创建/配置 `core-site.xml` 文件。 可在[此处](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application)找到创建 ADLS 服务凭据的详细步骤。 在 core-site.xml 文件中输入 Azure blob 或 ADLS 的凭据后，可使用 wasb:// 和 adl:// 这两个 URI 前缀引用存储在这些源中的数据。 

