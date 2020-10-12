---
title: 执行 Azure 机器学习管道
description: 了解如何在 Azure 数据工厂管道中运行 Azure 机器学习管道。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 07/16/2020
ms.openlocfilehash: 7239c1516c4a04b57249ea4f39bff4aec9156d72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337681"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>在 Azure 数据工厂中执行 Azure 机器学习管道

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

作为 Azure 数据工厂管道的一个步骤，运行 Azure 机器学习管道。 机器学习执行管道活动支持批量预测方案，例如识别可能的贷款违约、确定情绪以及分析客户行为模式。

以下视频介绍了此功能的六分钟简介和演示。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/How-to-execute-Azure-Machine-Learning-service-pipelines-in-Azure-Data-Factory/player]

## <a name="syntax"></a>语法

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>Type 属性

属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | --------
name | 管道中活动的名称 | String | 是
type | Activity 的类型为 "AzureMLExecutePipeline" | 字符串 | 是
linkedServiceName | Azure 机器学习链接服务 | 链接服务引用 | 是
mlPipelineId | 已发布 Azure 机器学习管道的 ID | 字符串（或带有 resultType 字符串的表达式） | 是
experimentName | 机器学习管道运行的运行历史记录试验名称 | 字符串（或带有 resultType 字符串的表达式） | 否
mlPipelineParameters | 要传递给已发布 Azure 机器学习管道终结点的键/值对。 键必须与已发布机器学习管道中定义的管道参数名称一致 | 具有键/值对的对象（或具有 resultType 对象的表达式） | 否
mlParentRunId | 父 Azure 机器学习管道运行 ID | 字符串（或带有 resultType 字符串的表达式） | 否
continueOnStepFailure | 当某个步骤失败时，继续执行机器学习管道运行中的其他步骤 | boolean | 否

> [!NOTE]
> 若要填充机器学习管道名称和 ID 中的下拉项，用户需要具有列出 ML 管道的权限。 ADF UX 直接使用已登录用户的凭据调用 AzureMLService API。  

## <a name="next-steps"></a>后续步骤
参阅以下文章了解如何以其他方式转换数据：

* [执行数据流活动](control-flow-execute-data-flow-activity.md)
* [U-SQL 活动](transform-data-using-data-lake-analytics.md)
* [Hive 活动](transform-data-using-hadoop-hive.md)
* [Pig 活动](transform-data-using-hadoop-pig.md)
* [MapReduce 活动](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 流式处理活动](transform-data-using-hadoop-streaming.md)
* [Spark 活动](transform-data-using-spark.md)
* [.NET 自定义活动](transform-data-using-dotnet-custom-activity.md)
* [存储过程活动](transform-data-using-stored-procedure.md)
