---
title: "浏览 Azure 上 SQL Server 虚拟机中的数据 | Microsoft Docs"
description: "如何浏览存储在 Azure 上 SQL Server VM 中的数据。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: bradsev
ms.openlocfilehash: d5079fc9d9a0ccd89664407fcdba9a7958e1407d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>浏览 Azure 上 SQL Server 虚拟机中的数据
本文档介绍如何浏览存储在 Azure 上 SQL Server VM 中的数据。 可通过使用 SQL 或 Python 等编程语言的数据整理来实现上述目的。

以下**菜单**带有描述如何使用工具从不同存储环境浏览数据的主题的链接。 此任务是 Cortana Analytics 过程 (CAP) 中的一个步骤。

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> 本文档中的示例 SQL 语句假定数据在 SQL Server 中。 如果不是这样，请参阅云数据科学进程映射，了解如何将数据移到 SQL Server。
> 
> 

## <a name="sql-dataexploration"></a>使用 SQL 脚本浏览 SQL 数据
以下是几个可用于浏览存储在 SQL Server 中的数据的示例 SQL 脚本。

1. 获取每个工作日观测值的计数
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. 获取某分类列中的级别
   
    `select  distinct <column_name> from <databasename>`
3. 获取两个分类列组合中的级别数 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. 获取数字列的分布
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> 有关实际的示例，可以使用 [NYC 出租车数据集](http://www.andresmh.com/nyctaxitrips/)，并参考名为[使用 IPython Notebook 和 SQL Server 的 NYC 数据整理](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)的IPNB，以获取端到端的演练。
> 
> 

## <a name="python"></a>使用 Python 浏览 SQL 数据
如果数据位于 SQL Server 中，使用 Python 浏览数据和生成功能类似于使用 Python处理 Azure blob 中的数据，如[处理数据科学环境中的 Azure Blob 数据](data-blob.md)中所述。 数据需要从数据库加载到 pandas 数据帧，才可以进行进一步的处理。 在本部分中，我们记录连接到数据库并将数据加载到数据帧的过程。

以下连接字符串格式可用于使用 pyodbc 从 Python 连接到 SQL Server 数据库（具有特定值的替换服务器名、dbname、用户名和密码）：

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python 中的 [Pandas 库](http://pandas.pydata.org/)提供一组丰富的数据结构，以及针对 Python 编程的数据操作的数据分析工具。 以下代码读取从 SQL Server 数据库返回到 Pandas 数据帧的结果：

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

现可使用 Pandas 数据帧，如本主题中[处理数据科学环境中的 Azure Blob 数据](data-blob.md)所述。

## <a name="the-team-data-science-process-in-action-example"></a>运行中的团队数据科学过程示例
有关 Cortana Analytics 过程中使用公用数据集的端到端演练示例，请参阅[操作中的团队数据科学过程：使用 SQL Server](sql-walkthrough.md)。

