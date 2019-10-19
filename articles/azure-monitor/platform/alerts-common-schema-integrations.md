---
title: 如何将常见的警报架构与逻辑应用集成
description: 了解如何创建利用通用警报架构处理所有警报的逻辑应用。
ms.service: azure-monitor
ms.subservice: alerts
ms.topic: conceptual
author: ananthradhakrishnan
ms.author: robb
ms.date: 05/27/2019
ms.openlocfilehash: 50a6067d271ad824f17df1ece36c3dd919c7b55b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555652"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何将常见的警报架构与逻辑应用集成

本文介绍如何创建一个逻辑应用，该应用利用常见的警报架构来处理所有警报。

## <a name="overview"></a>概述

[常见的警报架构](https://aka.ms/commonAlertSchemaDocs)在所有不同的警报类型中提供标准化且可扩展的 JSON 架构。 当以编程方式使用时，常见的警报架构最有用–通过 webhook、runbook 和逻辑应用。 本文演示如何编写单个逻辑应用来处理所有警报。 同样的原则也适用于其他编程方法。 本文中所述的逻辑应用为["基本" 字段](alerts-common-schema-definitions.md#essentials)创建了明确定义的变量，并且还介绍了如何处理特定于[警报类型](alerts-common-schema-definitions.md#alert-context)的逻辑。


## <a name="prerequisites"></a>必备组件 

本文假定读者熟悉 
* 设置预警规则（[指标](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)、[日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)和[活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)）
* 设置[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 从操作组内启用[常见警报架构](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>利用通用警报架构创建逻辑应用

1. 按照所[述步骤创建逻辑应用](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)。 

1.  选择触发器：“当收到 HTTP 请求时”。

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

1. 在此阶段，你可以根据特定的业务要求添加各种连接器（Microsoft 团队、时差、Salesforce 等）。 您可以使用现成的 "重要字段"。 

    ![重要字段](media/alerts-common-schema-integrations/logic-app-essential-fields.png "重要字段")
    
    或者，您可以使用 "Expression" 选项根据警报类型创作条件逻辑。

    ![逻辑应用表达式](media/alerts-common-schema-integrations/logic-app-expressions.png "逻辑应用表达式")
    
     您可以通过["monitoringService" 字段](alerts-common-schema-definitions.md#alert-context)来唯一标识警报类型，该类型基于您可以创建条件逻辑。

    
    例如，下面的代码段检查警报是否为基于 Application Insights 的日志警报，如果是，则打印搜索结果。 否则，它将打印 "NA"。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     详细了解如何[编写逻辑应用表达式](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)。

    


## <a name="next-steps"></a>后续步骤

* [了解有关操作组的详细信息](../../azure-monitor/platform/action-groups.md)。
* [了解有关常见警报架构的详细信息](https://aka.ms/commonAlertSchemaDocs)。

