---
title: "使用 SQL 和 Python 在 SQL Server 中为数据创建特征 | Microsoft Docs"
description: "处理 SQL Azure 中的数据"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: 
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;fashah;garye
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f0ac2799e2d8f18b2dd5b633555bfca08a44ba27
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>使用 SQL 和 Python 在 SQL Server 中为数据创建功能
本文档演示如何在 Azure 上为存储于 SQL Server VM 中的数据生成功能，用于帮助算法更有效地从数据中进行学习。 可通过使用 SQL 或 Python 等编程语言（都会在此演示）来实现上述目的。

[!INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

该**菜单**链接到介绍如何在各种环境中为数据创建特征的主题。 此任务是[团队数据科学过程 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) 中的一个步骤。

> [!NOTE]
> 有关实际的示例，可以参阅 [NYC 出租车数据集](http://www.andresmh.com/nyctaxitrips/) [使用 IPython Notebook 和 SQL Server 处理 NYC 数据](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb)获取端到端的演练。
> 
> 

## <a name="prerequisites"></a>先决条件
本文假设用户具备以下条件：

* 已创建 Azure 存储帐户。 如果需要说明，请参阅[创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)
* 在 SQL Server 中存储的数据。 如果尚未存储，请参阅[将数据移到 Azure 机器学习 Azure SQL Database](machine-learning-data-science-move-sql-azure.md) 以便获取有关如何移动数据的说明。

## <a name="sql-featuregen"></a>使用 SQL 生成功能
在本部分中，介绍使用 SQL 生成功能的方法：  

1. [生成基于计数的功能](#sql-countfeature)
2. [生成装箱功能](#sql-binningfeature)
3. [从单个列推出功能](#sql-featurerollout)

> [!NOTE]
> 一旦生成其他功能，可将它们作为列添加到现有表格，或使用其他功能和主键来创建可与原始表结合的新表格。
> 
> 

### <a name="sql-countfeature"></a>基于计数生成功能
本文档演示两种生成计数功能的方法。 第一种方法是使用条件求和，第二种方法是使用 where 子句。 之后这些新表格可与原始表结合（使用主键列），使其具有原始数据的计数功能。

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>生成装箱功能
下面的示例演示如何通过将可用作函数的数值列装箱（使用 5 箱），从而生成装箱函数：

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>从单个列推出功能
在此部分中，将演示如何在表格中推出单列以生成其他功能。 该示例假定用户尝试在其中生成功能的表中，具有一个纬度或经度列。

下面简要介绍纬度/经度位置数据（来自 stackoverflow 的资源`http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`）。 在特征化位置字段之前，了解以上知识会很有用：

* 符号指示地球上的四个方向：东、南、西、北。
* 非零的百位数代表经度，而不是纬度！
* 十位数提供约 1000 公里的位置。 提供处于哪个大洲或海洋的有用信息。
* 个位（十进制度）代表 111 公里以上的（60 海里，约 69 英里）位置。 它会告诉用户大致处于哪一个州或国家/地区。
* 第一个小数位值达 11.1 km：可将相邻的大城市区分开。
* 第二位小数值达 1.1 km：可将村庄分开。
* 第三位小数值达 110 m：可以定大型农业区域或工业园区。
* 第四位小数值达 11 m：可识别小块土地。 其准确性相当于未更正的、无干扰的 GPS 部件的典型准确性。
* 第五位小数值达 1.1 m：可将树与树区分开。 可通过差异更正获得该级别的、商用 GPS 计价单位的准确性。
* 第六个小数位值达 0.11 m：可用于详细布局结构、设计景观和修建道路等。 对于追踪冰川和河流的运动，它是不二之选。 可通过差异更正 GPS 等获得以上数值。

位置信息还具有以下特征：分离地区、位置和城市信息。 请注意：也可以调用 `https://msdn.microsoft.com/library/ff701710.aspx` 上的 Bing 地图 API 等 REST 终结点以获取区地区/区域信息。

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

如前面所述：可以进一步使用上述基于位置的功能来生成其他计数功能。

> [!TIP]
> 可以使用设定语言，以编程方式插入记录。 可能需要以块的形式插入数据以提高写入效率[签出如何使用此处的 pyodbc，执行操作的示例](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)。
> 另一种方法是采用 [BCP 实用工具](https://msdn.microsoft.com/library/ms162802.aspx)将数据插入数据库中
> 
> 

### <a name="sql-aml"></a>连接到 Azure 机器学习
新生成的功能可作为列添加到现有表或存储在新表中，也可与原始表结合以进行机器学习。 如果已经创建，可使用 Azure ML 中的[导入数据](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/)模块生成或访问生成功能，如下所示：

![azureml 读取器](./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>使用 Python 等编程语言
如果数据位于 SQL Server 中，使用 Python 生成函数类似于使用 Python处理 Azure blob 中的数据，如[处理数据科学环境中的过程 Azure Blob 数据](machine-learning-data-science-process-data-blob.md)中所述。 需要将数据从数据库加载到 pandas 数据帧，然后才可以进行进一步的处理。 在本部分中，我们记录连接到数据库并将数据加载到的数据帧的过程。

以下连接字符串格式可用于使用 pyodbc 从 Python 连接到 SQL Server 数据库（替换服务器名、dbname、用户名和密码替换为特定值）：

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

Python 中的 [Pandas 库](http://pandas.pydata.org/)提供一组丰富的数据结构，以及针对 Python 编程的数据操作的数据分析工具。 下面的代码读取 SQL Server 数据库返回的结果到 Pandas 数据帧:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

现在可根据 [Panda 创建 Azure blob 存储数据](machine-learning-data-science-create-features-blob.md) 主题中的说明来使用 Pandas 数据框架。


