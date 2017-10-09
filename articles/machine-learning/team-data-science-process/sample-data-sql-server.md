---
title: "对 Azure 上 SQL Server 中的数据进行采样 | Microsoft Docs"
description: "对 Azure 上 SQL Server 中的数据进行采样"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: fashah;garye;bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fbd83ad59a9db1daca4ba16402031e2c1c5b7991
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="heading"></a>对 Azure 上 SQL Server 中的数据进行采样
本文档介绍了如何使用 SQL 或 Python 编程语言对存储在 Azure 上 SQL Server 中的数据进行采样。 还介绍了如何通过将采样数据保存到文件、上传到 Azure blob，然后读取到 Azure 机器学习工作室，将数据移至 Azure 机器学习中。

Python 采样使用要连接到 Azure 上 SQL Server 的 [pyodbc](https://code.google.com/p/pyodbc/) ODBC 和 [Pandas](http://pandas.pydata.org/) 库进行采样。

> [!NOTE]
> 本文档中的示例 SQL 代码假设该数据在 Azure 上的 SQL Server 中。 如果不存在，请参阅[将数据移至 Azure 上的 SQL Server](move-sql-server-virtual-machine.md)，获取有关如何将数据移至 Azure 上 SQL Server 的说明的主题。
> 
> 

以下**菜单**所链接到的主题将描述如何从不同的存储环境采样数据。 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**为什么对数据进行采样？**
如果计划要分析的数据集很大，通常最好是对数据进行向下采样，以将数据减至较小但具备代表性且更易于管理的规模。 这有利于数据了解、探索和功能设计。 它在[团队数据科学过程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的作用是启用数据处理功能和机器学习模型的快速原型设计。

此采样任务是[团队数据科学流程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

## <a name="SQL"></a>使用 SQL
本部分介绍了几种使用 SQL 针对数据库中的数据执行简单随机采样的方法。 请根据数据大小及其分发方式选择一种方法。

下面的两个项目显示了如何使用 SQL Server 中的 newid 执行采样。 选择的方法取决于想要进行采样的随机程度（假设以下示例代码中的 pk_id 是自动生成的主密钥）。

1. 不太严格的随机采样
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. 更随机的采样 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample 也可用于采样，如下所示。 如果数据大小较大（假设不同页面上的数据各不相关）并且想要在一个合理时间完成查询，此方法可能更好。

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> 可以通过将采样数据存储在新表中从其浏览和生成功能
> 
> 

### <a name="sql-aml"></a>连接到 Azure 机器学习
可直接在 Azure 机器学习[导入数据][import-data]模块中使用上述采样查询，对数据进行联机低采样并将其引入 Azure 机器学习实验。 使用读取器模块读取采样数据的屏幕截图如下所示：

![读取器 SQL][1]

## <a name="python"></a>使用 Python 编程语言
本部分演示了如何使用 [pyodbc 库](https://code.google.com/p/pyodbc/)建立 ODBC 与 Python 中 SQL Server 数据库的连接。 数据库连接字符串如下所示：（将 servername、dbname、username 和 password 替换为配置）：

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python 中的 [Pandas](http://pandas.pydata.org/) 库提供一组丰富的数据结构，以及针对 Python 编程的数据操作的数据分析工具。 以下代码将 Azure SQL 数据库表中的 0.1% 数据采样读取到 Pandas 数据中：

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

现在，可以在 Pandas 数据帧中处理采样的数据。 

### <a name="python-aml"></a>连接到 Azure 机器学习
可以使用以下示例代码将低采样的数据保存到文件，并将其上传到 Azure blob。 可以使用[导入数据][import-data]模块将 blob 中的数据直接读取到 Azure 机器学习实验。 步骤如下： 

1. 将 Pandas 数据帧写入本地文件
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. 将本地文件上传到 Azure Blob
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. 使用 Azure 机器学习[导入数据][import-data]模块从 Azure blob 读取数据，如下方屏幕截图所示：

![blob 读取器][2]

## <a name="the-team-data-science-process-in-action-example"></a>运行中的团队数据科学过程示例
有关使用公用数据集的团队数据科学过程的端到端演练示例，请参阅[运行中的团队数据科学过程：使用 SQL Server](sql-walkthrough.md)。

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

