---
title: 如何将常见的警报架构与逻辑应用集成
description: 了解如何创建逻辑应用，可利用常见的警报架构来处理所有警报。
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734886"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>如何将常见的警报架构与逻辑应用集成

本文介绍如何创建一个逻辑应用，利用常见的警报架构来处理所有警报。

## <a name="overview"></a>概述

[常见警报架构](https://aka.ms/commonAlertSchemaDocs)跨所有不同警报类型提供标准化且可扩展的 JSON 架构。 常见的警报架构是通过 webhook、 runbook 和逻辑应用以编程方式 – 利用时非常有用。 在本文中，我们将演示如何编写单个逻辑应用来处理所有警报。 相同的原则可以应用于其他编程方法。 本文中所述的逻辑应用创建明确定义的变量[重要字段](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)，并且还描述了如何处理[警报类型](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields)特有的逻辑。


## <a name="prerequisites"></a>必备组件 

本文假定读者熟悉 
* 设置警报规则 ([指标](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)，[日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，[活动日志](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* 设置[操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* 启用[常见警报架构](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema)从操作组中

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>创建一个逻辑应用，利用常见的警报架构

1. 请按照[步骤所述，创建逻辑应用](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app)。 

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

1. 在此阶段，您可以添加基于特定的业务要求的连接器 （Microsoft Teams、 Slack、 Salesforce 等） 不同。 可以使用重要字段--现成的。 

    ![重要字段](media/alerts-common-schema-integrations/logic-app-essential-fields.png "重要字段")
    
    或者，您可以编写使用表达式选项的警报类型的条件逻辑。

    ![逻辑应用表达式](media/alerts-common-schema-integrations/logic-app-expressions.png "逻辑应用表达式")
    
     [MonitoringService 字段](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields)，可唯一标识警报的类型，基于其创建条件逻辑。

    
    例如，下面的代码段检查是否警报是基于 Application Insights 日志警报，以及如果是这样输出搜索结果。 否则，它将打印 NA。

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     详细了解如何[编写逻辑应用表达式](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)。

    


## <a name="next-steps"></a>后续步骤

* [要详细了解操作组](../../azure-monitor/platform/action-groups.md)。
* [了解有关警报的常见架构的详细信息](https://aka.ms/commonAlertSchemaDocs)。

