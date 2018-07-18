---
title: 如何获取有关知识库的分析 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何获取有关知识库的分析
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 1588d0c5a8eaf4e161b5319c9f33a772dc56b247
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366267"
---
# <a name="get-analytics-on-your-knowledge-base"></a>获取有关知识库的分析

如果你在[创建 QnA Maker 服务](./set-up-qnamaker-service-azure.md)期间已启用了 App Insights，则 QnA Maker 会存储所有聊天日志和其他遥测数据。 运行示例查询来从 App Insights 获取聊天日志。

1. 转到你的 App Insights 资源。

    ![选择你的 Application Insights 资源](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. 选择“分析”。 此时将打开一个新窗口，可以在其中查询 QnA Maker 遥测数据。

    ![选择“分析”](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. 粘贴以下查询并运行它。

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    选择“运行”以运行查询。

    ![运行查询](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>运行查询来获取有关 QnA Maker 知识库的其他分析

### <a name="total-90-day-traffic"></a>90 天流量总计

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### <a name="total-question-traffic-in-a-given-time-period"></a>给定时间段内的问题流量总计

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>用户流量

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>问题的延迟分布

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理密钥](./key-management.md)
