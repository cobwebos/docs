---
title: 媒体服务事件的 Azure 事件网格架构
description: 介绍为 Azure 事件网格中的媒体服务事件提供的属性
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782656"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>媒体服务事件的 Azure 事件网格架构

本文介绍媒体服务事件的架构和属性。

## <a name="media-services-job-state-change-event"></a>媒体服务作业状态更改事件

本部分提供媒体服务作业状态更改事件的属性和架构。  

### <a name="available-event-types"></a>可用事件类型

媒体服务作业发出以下事件类型：

| 事件类型 | 说明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 作业状态更改时引发。 |

### <a name="example-event"></a>示例事件

以下示例显示了已完成的作业状态事件的架构： 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>事件属性

事件具有以下顶级数据：

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| 主题 | 字符串 | 事件网格提供此值。 |
| subject | 字符串 | 媒体服务帐户资源下的资源路径。 |
| eventType | 字符串 | 此事件源的一个注册事件类型。 例如，“Microsoft.Media.JobStateChange”。 |
| EventTime | 字符串 | 基于提供程序 UTC 时间的事件生成时间。 |
| id | 字符串 | 事件的唯一标识符。 |
| 数据 | 对象 | 媒体服务事件数据。 |
| dataVersion | 字符串 | 数据对象的架构版本。 发布者定义架构版本。 |
| metadataVersion | 字符串 | 事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。 |

数据对象具有以下属性：

| 属性 | Type | 说明 |
| -------- | ---- | ----------- |
| previousState | 字符串 | 事件发生前的作业状态。 |
| state | 字符串 | 此事件中通知的作业的新状态。 例如，“已排队: 作业正在等待资源”或“已计划: 作业准备就绪”。|

作业状态可以为以下值之一：已排队、已计划、正在处理、已完成、错误、已取消、正在取消。

## <a name="next-steps"></a>后续步骤

[注册作业状态更改事件](job-state-events-cli-how-to.md)
