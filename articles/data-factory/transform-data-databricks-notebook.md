---
title: 用 Databricks 笔记本转换数据
description: 了解如何通过运行 Databricks Notebook 处理或转换数据。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
author: nabhishek
ms.author: abnarain
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 0768c16562a3931249dbbbc8f836ae14e7d0958b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893988"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>通过运行 Databricks Notebook 转换数据

[数据工厂管道](concepts-pipelines-activities.md)中的 Azure Databricks Notebook 活动在 Azure Databricks 工作区中运行 Databricks Notebook。 本文基于 [数据转换活动](transform-data.md) 一文，后者概述了数据转换和受支持的转换活动。 Azure Databricks 是用于运行 Apache Spark 的托管平台。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook 活动定义

下面是 Databricks Notebook 活动的示例 JSON 定义：

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook 活动属性

下表描述了 JSON 定义中使用的 JSON 属性：

|properties|描述|需要|
|---|---|---|
|name|管道中活动的名称。|是|
|description|描述活动用途的文本。|No|
|type|对于 Databricks Notebook 活动，活动类型是 DatabricksNotebook。|是|
|linkedServiceName|Databricks 链接服务的名称，Databricks Notebook 在其上运行。 若要了解此链接服务，请参阅 [计算链接服务](compute-linked-services.md) 一文。|是|
|notebookPath|要在 Databricks 工作区中运行的 Notebook 的绝对路径。 此路径必须以斜杠开头。|是|
|baseParameters|一个键/值对的数组。 基参数可用于运行每个活动。 如果 Notebook 采用的参数未指定，则将使用 Notebook 中的默认值。 有关参数的更多信息，请参阅 [Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)。|No|
|库|要安装在将执行作业的群集上的库列表。 它可以是 \<string, object> 数组。|No|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks 活动支持的库

在上面的 Databricks 活动定义中，可以指定以下库类型： *jar*、*蛋状物*、 *whl*、 *maven*、 *pypi*、 *cran*。

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

有关详细信息，请参阅库类型的 [Databricks 文档](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)。

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>在笔记本和数据工厂之间传递参数

可以使用 databricks 活动中的*baseParameters*属性将数据工厂参数传递到笔记本。 

在某些情况下，你可能需要将笔记本中的某些值传递回数据工厂，该数据工厂可用于数据工厂中的控制流（条件检查）或下游活动（大小限制为2MB）使用。 

1. 在笔记本中，可以调用[dbutils （"returnValue"）](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) ，并将相应的 "returnValue" 返回到数据工厂。

2. 您可以使用表达式（如 `'@activity('databricks notebook activity name').output.runOutput'`）在数据工厂中使用输出。 

   > [!IMPORTANT]
   > 如果要传递 JSON 对象，可以通过追加属性名称来检索值。 示例： `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>如何上传 Databricks 中的库

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[使用 Databricks 工作区 UI](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

若要获取使用 UI 添加的库的 dbfs 路径，可以使用 [Databricks CLI（安装）](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)。 

使用 UI 时，Jar 库通常存储在 dbfs:/FileStore/jars 下。 可以通过 CLI 列出所有库：databricks fs ls dbfs:/FileStore/jars。



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[使用 Databricks CLI 复制库](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

示例：databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars
