---
title: "数据工厂教程：第一个数据管道 | Microsoft Docs"
description: "此 Azure 数据工厂教程介绍如何创建和计划数据工厂，使用 Hadoop 群集上的 Hive 脚本处理数据。"
services: data-factory
keywords: "Azure 数据工厂教程, Hadoop 群集, Hadoop Hive"
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: c7bdcc824654086ea5f7a3099e95b39bca99a46b
ms.openlocfilehash: 80022c9a1983ccc53778a09d836ddfb476803dac


---
# <a name="tutorial-build-your-first-pipeline-to-process-data-using-hadoop-cluster"></a>教程：使用 Hadoop 群集构建用于处理数据的第一个管道
> [!div class="op_single_selector"]
> * [概述与先决条件](data-factory-build-your-first-pipeline.md)
> * [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

此教程介绍如何构建第一个包含数据管道的 Azure 数据工厂，该数据管道可以通过在 Azure HDInsight (Hadoop) 群集上运行 Hive 脚本来处理数据。 

> [!NOTE]
> 本文不提供 Azure 数据工厂的概念性概述。 有关服务的概念性概述，请参阅 [Azure 数据工厂简介](data-factory-introduction.md)。 如需查看推荐的浏览方法以便更好地了解数据工厂，请参阅[数据工厂学习路径](https://azure.microsoft.com/documentation/learning-paths/data-factory/)。
> 
> 

## <a name="whats-covered-in-this-tutorial"></a>本教程中涵盖的内容
**Azure 数据工厂**可使你以数据驱动型工作流（也称为数据通道）的方式，撰写数据**移动**和数据**处理**任务。 本文介绍如何使用数据处理（或数据转换）活动构建第一个数据管道。 此活动使用 HDInsight Hadoop 群集转换和分析示例 Web 日志。  

将在本教程中执行以下步骤：

1. 创建**数据工厂**。 数据工厂可包含移动和处理数据的一个或多个数据管道。 
2. 创建**链接服务**。 创建一个链接服务，将数据存储或计算服务链接到数据工厂。 数据存储（如 Azure 存储）会将输入/输出数据保留在管道中。 计算服务（如 HDInsight Hadoop 群集）会处理/转换数据。    
3. 创建输入和输出**数据集**。 输入数据集表示管道中活动的输入，输出数据集表示活动的输出。
4. 创建**管道**。 管道可以具有一个或多个活动（示例：复制活动、HDInsight Hive 活动）。 此示例使用的是 HDInsight Hive 活动，在 HDInsight Hadoop 群集上运行 Hive 脚本。 该脚本首先创建表，以引用存储在 Azure Blob 存储中的原始 Web 日志数据，然后按年和月对原始数据进行分区。
   
   Azure 数据工厂支持两种活动。 即[数据移动活动](data-factory-data-movement-activities.md)和[数据转换活动](data-factory-data-transformation-activities.md)。 只有一种数据移动活动，即复制活动。 本教程不使用复制活动。 有关使用复制活动的教程，请参阅[教程：将 Azure blob 中的数据复制到 Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 本教程中使用的 HDInsight Hive 活动是数据工厂支持的数据转换活动之一。  

下面是在本教程中构建的数据工厂示例的**图示视图**。 

![数据工厂教程中的图示视图](./media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)

在本教程中，**adfgetstarted** Azure blob 容器的 **inputdata** 文件夹包含名为 input.log 的文件。 此日志文件包含三个月的条目：2016 年 1 月、2 月和 3 月。 以下是针对输入文件中的每个月的示例行。 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

当文件由具有 HDInsight Hive 活动的管道处理时，活动会在按年份和月份分区输入数据的 HDInsight 群集上运行 Hive 脚本。 该脚本会创建三个输出文件夹，其中包含具有每个月条目的文件。  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

如上所示的示例行中，第行（具有 2016-01-01）会写入 month = 1 文件夹中的 000000_0 文件。 同样，第二行会写入 month = 2 文件夹中的文件，第三行会写入 month = 3 文件夹中的文件。  

## <a name="prerequisites"></a>先决条件
开始本教程之前，必须具有以下先决条件：

1. **Azure 订阅** - 如果没有 Azure 订阅，只需几分钟就能创建一个免费试用帐户。 如需了解如何获取免费试用帐户，请参阅[免费试用](https://azure.microsoft.com/pricing/free-trial/)一文。
2. **Azure 存储** - 在本教程中，你将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)一文。 创建存储帐户后，记下**帐户名称**和**访问密钥**。 请参阅[查看、复制和重新生成存储访问密钥](../storage/storage-create-storage-account.md#view-and-copy-storage-access-keys)。 

### <a name="upload-files-to-azure-storage-for-the-tutorial"></a>将本教程使用的文件上传到 Azure 存储
开始本教程之前，需要使用本教程的示例文件准备 Azure 存储帐户。

1. 将 Hive 查询文件 (HQL) 上传到 **adfgetstarted** blob 容器的 **script** 文件夹。
2. 将输入文件上传到 **adfgetstarted** blob 容器的 **inputdata** 文件夹。 

#### <a name="create-hql-script-file"></a>创建 HQL 脚本文件
启动**记事本**并粘贴以下 HQL 脚本。 该 Hive 脚本会创建两个表：**WebLogsRaw** 和 **WebLogsPartitioned**。 在菜单上单击“文件”并选择“另存为”。 切换到硬盘上的 **C:\adfgetstarted** 文件夹。 为“保存类型”****字段选择**所有文件 (*.*)**。在“文件名”****中输入 **partitionweblogs.hql**。确认对话框底部的“编码”****字段设置为 **ANSI**。如果没有，则将其设置为 **ANSI**。  

```SQL   
set hive.exec.dynamic.partition.mode=nonstrict;

DROP TABLE IF EXISTS WebLogsRaw; 
CREATE TABLE WebLogsRaw (
  date  date,
  time  string,
  ssitename string,
  csmethod  string,
  csuristem  string,
  csuriquery string,
  sport int,
  susername string,
  cipcsUserAgent string,
  csCookie string,
  csReferer string,
  cshost  string,
  scstatus  int,
  scsubstatus  int,
  scwin32status  int,
  scbytes int,
  csbytes int,
  timetaken int
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
LINES TERMINATED BY '\n' 
tblproperties ("skip.header.line.count"="2");

LOAD DATA INPATH '${hiveconf:inputtable}' OVERWRITE INTO TABLE WebLogsRaw;

DROP TABLE IF EXISTS WebLogsPartitioned ; 
create external table WebLogsPartitioned (  
  date  date,
  time  string,
  ssitename string,
  csmethod  string,
  csuristem  string,
  csuriquery string,
  sport int,
  susername string,
  cipcsUserAgent string,
  csCookie string,
  csReferer string,
  cshost  string,
  scstatus  int,
  scsubstatus  int,
  scwin32status  int,
  scbytes int,
  csbytes int,
  timetaken int
)
partitioned by ( year int, month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
STORED AS TEXTFILE 
LOCATION '${hiveconf:partitionedtable}';

INSERT INTO TABLE WebLogsPartitioned  PARTITION( year , month) 
SELECT
  date,
  time,
  ssitename,
  csmethod,
  csuristem,
  csuriquery,
  sport,
  susername,
  cipcsUserAgent,
  csCookie,
  csReferer,
  cshost,
  scstatus,
  scsubstatus,
  scwin32status,
  scbytes,
  csbytes,
  timetaken,
  year(date),
  month(date)
FROM WebLogsRaw
```

在运行时，数据工厂管道中的 Hive 活动会传递 **inputtable** 和 **partitionedtable** 参数的值，如以下代码段所示：  

```JSON
"inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
"partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
```

**storageaccountname** 是 Azure 存储帐户的名称。

#### <a name="create-a-sample-input-file"></a>创建输入文件示例
使用记事本，在 **c:\adfgetstarted** 中，创建包含以下内容且名为 **input.log** 的文件： 

```
#Software: Microsoft Internet Information Services 8.0
#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step5.png X-ARR-LOG-ID=9b0c14b1-434d-495a-9b0d-46775194257b 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 37931 871 32
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step6.png X-ARR-LOG-ID=f99cff81-ec38-4a13-b2fe-21b10adca996 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 27146 871 47
2016-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step1.png X-ARR-LOG-ID=af94d3b4-8e05-4871-82c4-638f866d4e83 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 66259 871 140
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-02-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
2016-03-01 02:01:10 SAMPLEWEBSITE GET /blogposts/mvc4/step7.png X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 30184 871 47
```

#### <a name="upload-input-file-and-hql-file-to-your-azure-blob-storage"></a>将输入文件和 HQL 文件上传到 Azure Blob 存储
此部分说明如何使用 **AzCopy** 工具将 input.log 和 partitionweblogs.hql 文件复制到 Azure Blob 存储。 可选择使用任意工具（如 [Microsoft Azure 存储资源管理器](http://storageexplorer.com/)、[ClumsyLeaf Software CloudXPlorer](http://clumsyleaf.com/products/cloudxplorer)）执行此任务。   

1. 下载[最新版 **AzCopy**](http://aka.ms/downloadazcopy)，或[最新预览版本](http://aka.ms/downloadazcopypr)。 如需查看有关如何使用该实用工具的说明，请参阅[如何使用 AzCopy](../storage/storage-use-azcopy.md)一文。
2. 导航到 c:\adfgetstarted 文件夹，并运行以下命令： 

    ```
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy" /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/inputdata /DestKey:<storageaccesskey>  /Pattern:input.log
    ```   
    此命令会将 **input.log** 文件上传到存储帐户（**adfgetstarted** 容器和 **inputdata** 文件夹）。 使用存储帐户名称替换 **storageaccountname**，并使用存储访问密钥替换 **storageaccesskey**。
   
   > [!NOTE]
   > 此命令会在 Azure Blob 存储中创建名为 **adfgetstarted** 的容器并将 **input.log** 文件从本地驱动器复制到容器中的 **inputdata** 文件夹。 
   > 
   > 
3. 文件上传成功后，将看到来自 AzCopy 的输出，如以下所示。

    ```   
    Finished 1 of total 1 file(s).
    [2015/12/16 23:07:33] Transfer summary:
    -----------------
    Total files transferred: 1
    Transfer successfully:   1
    Transfer skipped:        0
    Transfer failed:         0
    Elapsed time:            00.00:00:01
    ```
4. 运行以下命令将 **partitionweblogs.hql** 文件上传到 **adfgetstarted** 容器中的 **script** 文件夹中。 命令如下： 

    ```   
    AzCopy /Source:. /Dest:https://<storageaccountname>.blob.core.windows.net/adfgetstarted/script /DestKey:<storageaccesskey>  /Pattern:partitionweblogs.hql
    ```

你已完成此先决条件。 可使用以下方法之一来创建数据工厂。 单击顶部下拉列表中的其中一个选项或以下链接来执行此教程。 

* [Azure 门户](data-factory-build-your-first-pipeline-using-editor.md)
* [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
* [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
* [Resource Manager 模板](data-factory-build-your-first-pipeline-using-arm.md)
* [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)




<!--HONumber=Dec16_HO3-->


