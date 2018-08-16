---
title: 使用 Databricks Jar 转换数据 - Azure | Microsoft Docs
description: 了解如何通过运行 Databricks Jar 处理或转换数据。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: a47d0130cd06a936da456ec6d78bde99907072f2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526294"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>通过运行 Azure Databricks 中的 Jar 活动转换数据

[数据工厂管道](concepts-pipelines-activities.md)中的 Azure Databricks Jar 活动在 Azure Databricks 群集中运行 Spark Jar。 本文基于[数据转换活动](transform-data.md)一文，它概述了数据转换和受支持的转换活动。 Azure Databricks 是一个用于运行 Apache Spark 的托管平台。

有关此功能的十一分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar 活动定义

下面是 Databricks Jar 活动的示例 JSON 定义：

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks Jar 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|属性|说明|必选|
|:--|---|:-:|
|名称|管道中活动的名称。|是|
|description|描述活动用途的文本。|否|
|type|对于 Databricks Jar 活动，活动类型是 DatabricksSparkJar。|是|
|linkedServiceName|Databricks 链接服务的名称，Jar 活动在其上运行。 若要了解此链接服务，请参阅[计算链接服务](compute-linked-services.md)一文。|是|
|mainClassName|类的全名，包含要执行的主要方法。 此类必须包含在作为库提供的 JAR 中。|是|
|parameters|将传递到主要方法的参数。  这是一个字符串数组。|否|
|库|要安装在将执行作业的群集上的库列表。 它可以是 <string, object> 数组|是（至少有一个包含 mainClassName 方法）|

## <a name="supported-libraries-for-databricks-activities"></a>databricks 活动支持的库

在以上 Databricks 活动定义中，指定这些库类型：jar、egg、maven、pypi、cran。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

有关详细信息，请参阅库类型的 [Databricks 文档](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)。

## <a name="how-to-upload-a-library-in-databricks"></a>如何上传 Databricks 中的库

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[使用 Databricks 工作区 UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

若要获取使用 UI 添加的库的 dbfs 路径，可以使用 [Databricks CLI（安装）](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 

使用 UI 时，Jar 库通常存储在 dbfs:/FileStore/jars 下。 可以通过 CLI 列出所有库：databricks fs ls dbfs:/FileStore/job-jars 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[使用 Databricks CLI 复制库](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
使用 Databricks CLI[（安装步骤）](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 

示例 - 将 JAR 复制到 dbfs：dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar
