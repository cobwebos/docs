---
title: "用于 B2B 监视的 X12 跟踪架构 - Azure 逻辑应用 | Microsoft 文档"
description: "使用 X12 跟踪架构通过 Azure 集成帐户中的事务监视 B2B 消息。"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 2470a8e7d82c6c01e9f32b062bc09f5a2f6ed1cd


---
# <a name="x12-tracking-schemas"></a>X12 跟踪架构
可以在 Azure 集成帐户中使用这些 X12 跟踪架构来帮助监视企业到企业 (B2B) 的事务：

* X12 事务集跟踪架构
* X12 事务集确认跟踪架构
* X12 交换跟踪架构
* X12 交换确认跟踪架构
* X12 功能组跟踪架构
* X12 功能组确认跟踪架构

## <a name="x12-transaction-set-tracking-schema"></a>X12 事务集跟踪架构
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 交换控制编号。 (可选) |
| functionalGroupControlNumber | String | 功能控制编号。 (可选) |
| transactionSetControlNumber | String | 事务集控制编号。 (可选) |
| CorrelationMessageId | String | 关联消息 ID。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} 的组合。 (可选) |
| messageType | String | 事务集或文档类型。 (可选) |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需） |
| isTechnicalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了技术确认。 （必需） |
| isFunctionalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了功能确认。 （必需） |
| needAk2LoopForValidMessages | 布尔 | 有效的消息是否需要 AK2 循环。 （必需） |
| segmentsCount | Integer | X12 事务集中的段数。 (可选) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>X12 事务集确认跟踪架构
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 功能确认的交换控制编号。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值。 (可选) |
| functionalGroupControlNumber | String | 功能确认的功能组控制编号。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值。 (可选) |
| isaSegment | String | 消息的 ISA 段。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值。 (可选) |
| gsSegment | String | 消息的 GS 段。 只有发送方已收到发送给合作伙伴的消息的功能确认时，才会为发送方填充此值。 (可选) |
| respondingfunctionalGroupControlNumber | String | 响应交换控制编号。 (可选) |
| respondingFunctionalGroupId | String | 确认中映射到 AK101 的响应功能组 ID。 (可选) |
| respondingtransactionSetControlNumber | String | 响应事务集控制编号。 (可选) |
| respondingTransactionSetId | String | 确认中映射到 AK201 的响应事务集 ID。 (可选) |
| statusCode | 布尔 | 事务集确认状态代码。 （必需） |
| segmentsCount | 枚举 | 确认状态代码。 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| processingStatus | 枚举 | 确认的处理状态。 允许的值为 **Received**、**Generated** 和 **Sent**。 （必需） |
| CorrelationMessageId | String | 关联消息 ID。 {AgreementName}{*GroupControlNumber*}{TransactionSetControlNumber} 的组合。 (可选) |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需） |
| ak2Segment | String | 对接收的功能组内的事务集的确认。 (可选) |
| ak3Segment | String | 报告数据段中的错误。 (可选) |
| ak5Segment | String | 报告是否接受或拒绝 AK2 段中标识的事务集及其原因。 (可选) |

## <a name="x12-interchange-tracking-schema"></a>X12 交换跟踪架构
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 交换控制编号。 (可选) |
| isaSegment | String | 消息 ISA 段。 (可选) |
| isTechnicalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了技术确认。 （必需） |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需） |
| isa09 | String | X12 文档交换日期。 (可选) |
| isa10 | String | X12 文档交换时间。 (可选) |
| isa11 | String | X12 交换控制标准标识符。 (可选) |
| isa12 | String | X12 交换控制版本号。 (可选) |
| isa14 | String | 请求了 X12 确认。 (可选) |
| isa15 | String | 表示测试或生产的指示符。 (可选) |
| isa16 | String | 元素分隔符。 (可选) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>X12 交换确认跟踪架构
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 从合作伙伴收到的技术确认的交换控制编号。 (可选) |
| isaSegment | String | 从合作伙伴收到的技术确认的 ISA 段。 (可选) |
| respondingInterchangeControlNumber |String | 从合作伙伴收到的技术确认的交换控制编号。 (可选) |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需） |
| statusCode | 枚举 | 交换确认状态代码。 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| processingStatus | 枚举 | 确认状态。 允许的值为 **Received**、**Generated** 和 **Sent**。 （必需） |
| ta102 | String | 交换日期。 (可选) |
| ta103 | String | 交换时间。 (可选) |
| ta105 | String | 交换注释代码。 (可选) |

## <a name="x12-functional-group-tracking-schema"></a>X12 功能组跟踪架构
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 交换控制编号。 (可选) |
| functionalGroupControlNumber | String | 功能控制编号。 (可选) |
| gsSegment | String | 消息 GS 段。 (可选) |
| isTechnicalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了技术确认。 （必需） |
| isFunctionalAcknowledgmentExpected | 布尔 | 是否已在 X12 协议中配置了功能确认。 （必需） |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需）|
| gs01 | String | 功能标识符代码。 (可选) |
| gs02 | String | 应用程序发送者的代码。 (可选) |
| gs03 | String | 应用程序接收者的代码。 (可选) |
| gs04 | String | 功能组日期。 (可选) |
| gs05 | String | 功能组时间。 (可选) |
| gs07 | String | 责任代理代码。 (可选) |
| gs08 | String | 版本/发行版/行业标识符代码。 (可选) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>X12 功能组确认跟踪架构
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| 属性 | 类型 | 说明 |
| --- | --- | --- |
| senderPartnerName | String | X12 消息发送者的合作伙伴名称。 (可选) |
| receiverPartnerName | String | X12 消息接收者的合作伙伴名称。 (可选) |
| senderQualifier | String | 发送合作伙伴限定符。 （必需） |
| senderIdentifier | String | 发送合作伙伴标识符。 （必需） |
| receiverQualifier | String | 接收合作伙伴限定符。 （必需） |
| receiverIdentifier | String | 接收合作伙伴标识符。 （必需） |
| agreementName | String | 用于解析消息的 X12 协议的名称。 (可选) |
| direction | 枚举 | 消息流的方向，接收或发送。 （必需） |
| interchangeControlNumber | String | 交换控制编号，当从合作伙伴收到了技术确认时，会为发送方填充此值。 (可选) |
| functionalGroupControlNumber | String | 技术确认的功能组控制编号，当从合作伙伴收到了技术确认时，会为发送方填充此值。 (可选) |
| isaSegment | String | 与交换控制编号相同，但仅在特定情况下填充。 (可选) |
| gsSegment | String | 与功能组控制编号相同，但仅在特定情况下填充。 (可选) |
| respondingfunctionalGroupControlNumber | String | 原始功能组的控制编号。 (可选) |
| respondingFunctionalGroupId | String | 在确认功能组 ID 中映射到 AK101。 (可选) |
| isMessageFailed | 布尔 | X12 消息是否失败。 （必需） |
| statusCode | 枚举 | 确认状态代码。 允许的值为 **Accepted**、**Rejected** 和 **AcceptedWithErrors**。 （必需） |
| processingStatus | 枚举 | 确认的处理状态。 允许的值为 **Received**、**Generated** 和 **Sent**。 （必需） |
| ak903 | String | 收到的事务集的数量。 (可选) |
| ak904 | String | 在标识的功能组中接受的事务集的数量。 (可选) |
| ak9Segment | String | 是接受还是拒绝 AK1 段中标识的功能组，以及原因是什么。 (可选) |

## <a name="next-steps"></a>后续步骤
* 了解有关[监视 B2B 消息](logic-apps-monitor-b2b-message.md)的详细信息。
* 了解有关 [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)的详细信息。
* 了解有关 [B2B 自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)的详细信息。
* 了解有关[在 Operations Management Suite 门户中跟踪 B2B 消息](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)的信息。
* 了解有关 [Enterprise Integration Pack 的详细信息](../logic-apps/logic-apps-enterprise-integration-overview.md)。  



<!--HONumber=Jan17_HO4-->


