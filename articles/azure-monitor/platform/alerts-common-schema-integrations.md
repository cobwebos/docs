---
title: 如何将常见警报架构与逻辑应用集成
description: 了解如何创建一个逻辑应用，以利用常见警报架构来处理所有警报。
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 569b97ecc24306741c3323ce5bc526f88645c1dd
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702953"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何将常见警报架构与逻辑应用集成

本文介绍如何创建一个逻辑应用，以利用常见警报架构来处理所有警报。

## <a name="overview"></a>概述

[常见警报架构](https://aka.ms/commonAlertSchemaDocs)跨各种不同警报类型提供一种标准化的可扩展 JSON 架构。 在通过 Webhook、Runbook 和逻辑应用以编程方式利用时，常见警报架构最有用。 本文演示如何编写单个逻辑应用以处理所有警报。 对于其他编程方法，可以运用相同的原则。 本文中所述的逻辑应用为[“重要”字段](alerts-common-schema-definitions.md#essentials)创建了明确定义的变量，并且还描述了如何处理特定于[警报类型](alerts-common-schema-definitions.md#alert-context)的逻辑。


## <a name="prerequisites"></a>先决条件 

本文假设读者熟悉以下操作 
* 设置警报规则（[指标](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)、[日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)、[活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)）
* 设置[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 从操作组中启用[常见警报架构](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>创建一个利用常见警报架构的逻辑应用

1. 请按照[概述的步骤创建逻辑应用](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)。 

1.  选择触发器：**当收到 HTTP 请求时**。

    ![逻辑应用触发器](media/action-groups-logic-app/logic-app-triggers.png "逻辑应用触发器")

1.  选择“编辑”更改 HTTP 请求触发器。

    ![HTTP 请求触发器](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP 请求触发器")


1.  复制并粘贴以下架构：

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. 依次选择“+ 新建步骤”、“添加操作”。

    ![添加操作](media/action-groups-logic-app/add-action.png "添加操作")

1. 在此阶段，可以根据具体的业务要求添加各种连接器（Microsoft Teams、Slack、Salesforce 等）。 可以使用现成的“重要字段”。 

    ![重要字段](media/alerts-common-schema-integrations/logic-app-essential-fields.png "重要字段")
    
    或者，可以使用“表达式”选项基于警报类型编写条件逻辑。

    ![逻辑应用表达式](media/alerts-common-schema-integrations/logic-app-expressions.png "逻辑应用表达式")
    
     [“monitoringService”字段](alerts-common-schema-definitions.md#alert-context)可用于唯一标识警报类型，基于该警报类型，可以创建条件逻辑。

    
    例如，以下代码片段会检查警报是否为基于 Application Insights 的日志警报，如果是，则会输出搜索结果。 否则，输出“不可用”。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     详细了解如何[编写逻辑应用表达式](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)。

    


## <a name="next-steps"></a>后续步骤

* [详细了解操作组](../../azure-monitor/platform/action-groups.md)。
* [详细了解常见警报架构](https://aka.ms/commonAlertSchemaDocs)。

