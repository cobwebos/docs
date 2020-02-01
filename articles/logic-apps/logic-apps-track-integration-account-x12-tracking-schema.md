---
title: B2B 消息的 X12 跟踪架构
description: 创建跟踪架构来监视 Azure 逻辑应用的 X12 消息
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905302"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建用于跟踪 X12 消息的架构

若要帮助你监视企业到企业 (B2B) 事务的成功、错误和消息属性，可以在集成帐户中使用以下 X12 跟踪架构：

* X12 事务集跟踪架构
* X12 事务集确认跟踪架构
* X12 交换跟踪架构
* X12 交换确认跟踪架构
* X12 功能组跟踪架构
* X12 功能组确认跟踪架构

## <a name="x12-transaction-set-tracking-schema"></a>X12 事务集跟踪架构

```json
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
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，该方向是 `receive` 或 `send` |
| interchangeControlNumber | 否 | String | 交换控制编号 |
| functionalGroupControlNumber | 否 | String | 功能控制编号 |
| transactionSetControlNumber | 否 | String | 事务集控制编号 |
| CorrelationMessageId | 否 | String | 相关消息 ID，它是 {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} 的组合 |
| messageType | 否 | String | 事务集或文档类型 |
| isMessageFailed | 是 | Boolean | X12 消息是否失败 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 是否在 X12 协议中配置了技术确认 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 是否在 X12 协议中配置了功能确认 |
| needAk2LoopForValidMessages | 是 | Boolean | 有效的消息是否需要 AK2 循环 |
| segmentsCount | 否 | Integer | X12 事务集中的段数 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>X12 事务集确认跟踪架构

```json
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
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，该方向是 `receive` 或 `send` |
| interchangeControlNumber | 否 | String | 功能确认的交换控制编号。 该值仅填充发送方，其中接收到发送到合作伙伴的消息的功能确认。 |
| functionalGroupControlNumber | 否 | String | 功能确认的功能组控制编号。 该值仅填充发送方，其中接收发送到合作伙伴的消息的功能确认 |
| isaSegment | 否 | String | 消息的 ISA 段。 该值仅填充发送方，其中接收发送到合作伙伴的消息的功能确认 |
| gsSegment | 否 | String | 消息的 GS 段。 该值仅填充发送方，其中接收发送到合作伙伴的消息的功能确认 |
| respondingfunctionalGroupControlNumber | 否 | String | 响应交换控制编号 |
| respondingFunctionalGroupId | 否 | String | 在确认中映射到 AK101 的响应功能组 ID |
| respondingtransactionSetControlNumber | 否 | String | 响应的事务集控制编号 |
| respondingTransactionSetId | 否 | String | 在确认中映射到 AK201 的响应事务集 ID |
| statusCode | 是 | Boolean | 事务集确认状态代码 |
| segmentsCount | 是 | 枚举 | 确认状态代码，其中包含以下允许的值： `Accepted`、`Rejected`和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 使用以下允许的值处理确认状态： `Received`、`Generated`和 `Sent` |
| CorrelationMessageId | 否 | String | 相关消息 ID，它是 {AgreementName} {*GroupControlNumber*} {TransactionSetControlNumber} 的组合 |
| isMessageFailed | 是 | Boolean | X12 消息是否失败 |
| ak2Segment | 否 | String | 收到的功能组中的事务集的确认 |
| ak3Segment | 否 | String | 报告数据段中的错误 |
| ak5Segment | 否 | String | 报告是否接受或拒绝 AK2 段中标识的事务集，以及原因 |
|||||

## <a name="x12-interchange-tracking-schema"></a>X12 交换跟踪架构

```json
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
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，该方向是 `receive` 或 `send` |
| interchangeControlNumber | 否 | String | 交换控制编号 |
| isaSegment | 否 | String | 消息 ISA 段 |
| isTechnicalAcknowledgmentExpected | Boolean | 是否在 X12 协议中配置了技术确认  |
| isMessageFailed | 是 | Boolean | X12 消息是否失败 |
| isa09 | 否 | String | X12 文档交换日期 |
| isa10 | 否 | String | X12 文档交换时间 |
| isa11 | 否 | String | X12 交换控制标准标识符 |
| isa12 | 否 | String | X12 交换控制版本号 |
| isa14 | 否 | String | 要求 X12 确认 |
| isa15 | 否 | String | 测试或生产的指示符 |
| isa16 | 否 | String | 元素分隔符 |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>X12 交换确认跟踪架构

```json
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
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，该方向是 `receive` 或 `send` |
| interchangeControlNumber | 否 | String | 从合作伙伴收到的技术确认的交换控制编号 |
| isaSegment | 否 | String | 从合作伙伴收到的技术确认的 ISA 段 |
| respondingInterchangeControlNumber | 否 | String | 合作伙伴收到的技术确认的交换控制编号 |
| isMessageFailed | 是 | Boolean | X12 消息是否失败 |
| statusCode | 是 | 枚举 | 交换确认状态代码，其中包含以下允许的值： `Accepted`、`Rejected`和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 具有以下允许值的确认状态： `Received`、`Generated`和 `Sent` |
| ta102 | 否 | String | 交换日期 |
| ta103 | 否 | String | 交换时间 |
| ta105 | 否 | String | 交换注释代码 |
|||||

## <a name="x12-functional-group-tracking-schema"></a>X12 功能组跟踪架构

```json
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
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 消息要解析到的 X12 协议的名称 |
| direction | 是 | 枚举 | 消息流的方向（接收或发送） |
| interchangeControlNumber | 否 | String | 交换控制编号 |
| functionalGroupControlNumber | 否 | String | 功能控制编号 |
| gsSegment | 否 | String | 消息 GS 段 |
| isTechnicalAcknowledgmentExpected | 是 | Boolean | 是否在 X12 协议中配置了技术确认 |
| isFunctionalAcknowledgmentExpected | 是 | Boolean | 是否在 X12 协议中配置了功能确认 |
| isMessageFailed | 是 | Boolean | X12 消息是否失败 |
| gs01 | 否 | String | 功能标识符代码 |
| gs02 | 否 | String | 应用程序发送方的代码 |
| gs03 | 否 | String | 应用程序接收方的代码 |
| gs04 | 否 | String | 功能组日期 |
| gs05 | 否 | String | 功能组时间 |
| gs07 | 否 | String | 责任代理代码 |
| gs08 | 否 | String | 版本、版本或行业的标识符代码 |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>X12 功能组确认跟踪架构

```json
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
```

| 属性 | 需要 | 类型 | Description |
|----------|----------|------|-------------|
| senderPartnerName | 否 | String | X12 消息发送方的伙伴名称 |
| receiverPartnerName | 否 | String | X12 消息接收者的合作伙伴名称 |
| senderQualifier | 是 | String | 发送合作伙伴限定符 |
| senderIdentifier | 是 | String | 发送合作伙伴标识符 |
| receiverQualifier | 是 | String | 接收合作伙伴限定符 |
| receiverIdentifier | 是 | String | 接收合作伙伴标识符 |
| agreementName | 否 | String | 解析消息的 X12 协议名称 |
| direction | 是 | 枚举 | 消息流的方向，该方向是 `receive` 或 `send` |
| interchangeControlNumber | 否 | String | 交换控制编号，当从合作伙伴收到技术确认时，它将填充发送方 |
| functionalGroupControlNumber | 否 | String | 技术确认的功能组控制编号，当从合作伙伴收到技术确认时，它将填充发送端 |
| isaSegment | 否 | String | 与交换控制编号相同，但仅在特定情况下填充 |
| gsSegment | 否 | String | 与功能组控制编号相同，但仅在特定情况下填充 |
| respondingfunctionalGroupControlNumber | 否 | String | 原始功能组的控制编号 |
| respondingFunctionalGroupId | 否 | String | 映射到确认功能组 ID 中的 AK101 |
| isMessageFailed | Boolean | X12 消息是否失败 |
| statusCode | 是 | 枚举 | 确认状态代码，其中包含以下允许的值： `Accepted`、`Rejected`和 `AcceptedWithErrors` |
| processingStatus | 是 | 枚举 | 使用以下允许的值处理确认状态： `Received`、`Generated`和 `Sent` |
| ak903 | 否 | String | 已接收的事务集的数目 |
| ak904 | 否 | String | 已标识功能组中接受的事务集的数目 |
| ak9Segment | 否 | String | 是接受还是拒绝 AK1 段中标识的功能组，以及为何 |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B 协议跟踪架构

有关 B2B 协议跟踪架构的信息，请参阅：

* [AS2 跟踪架构](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [B2B 自定义跟踪架构](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>后续步骤

* [利用 Azure Monitor 日志监视 B2B 消息](../logic-apps/monitor-b2b-messages-log-analytics.md)