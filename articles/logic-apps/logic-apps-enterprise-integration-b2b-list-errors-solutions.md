---
title: "错误和解决方案的逻辑应用 B2B 列表： Azure App Service |Microsoft Docs"
description: "错误和解决方案的逻辑应用 B2B 列表"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 1865d75f1b4c2aa18d5a3130f639572d19563b3e
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017

---

# <a name="logic-apps-b2b-list-of-errors-and-solutions"></a>错误和解决方案的逻辑应用 B2B 列表  
本文可帮助你解决可能会出现在逻辑应用 B2B 方案中的错误，并提供更正这些错误的适当操作。


## <a name="agreement-resolution"></a>协议解析

### <a name="no-agreement-found"></a>* 没有找到协议 

|   |   |  
|---|---|
| 错误说明 | 没有找到具有协议解析参数的协议|    
| 用户操作 | 应将协议添加到具有议定的业务标识的集成帐户。</br> 业务标识应与输入的消息 id 相匹配|  
|   |   |

### <a name="-no-agreement-found-with-identities"></a>* 没有找到具有标识的协议

|   |   | 
|---|---|
| 错误说明 | 没有找到具有标识 'AS2Identity'::'Partner1' and'AS2Identity'::'Partner3' 的协议| 
| 用户操作 | 协议配置无效的 AS2-From 或 AS2-To。 </br> 更正 AS2 消息 AS2-From 或 AS2-To 标头或协议，以匹配在具有协议配置的 AS2 消息标头中的 AS2 ID |
|   |   |     

## <a name="as2"></a>AS2

### <a name="-missing-as2-message-headers"></a>* 缺少 AS2 消息标头  

|   |   |  
|---|---|
| 错误说明| 无效的 AS2 标头。 "AS2-To" 或 "AS2-From" 的其中一个标头为空| 
| 用户操作 | 收到的 AS2 消息不包含 AS2-From 或 AS2-To 标头或均不含。 </br> 检查 AS2 消息的 AS2-From 或 AS2-To 标头并基于协议配置进行更正 |
|  |  | 


### <a name="-missing-as2-message-body-and-headers"></a>* 缺少 AS2 消息正文和标头    

|   |   |  
|---|---|
| 错误说明| 请求内容为 null 或为空 | 
| 用户操作 | 收到的 AS2 消息不包含消息正文 |
|  |  | 

### <a name="-as2-message-decryption-failure"></a>* AS2 消息解密失败

|   |   | 
|---|---|
| 错误说明 |  [已处理/错误：解密失败] | 
| 用户操作 | 发送给伙伴之前，请添加 @base64ToBinary 到 AS2Message 
```java
            "HTTP": {
                "inputs": {
                    "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
                    "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
                    "method": "POST",
                    "uri": "xxxxx.xxx"
                },
                
``` 

### <a name="-mdn-decryption-failure"></a>* MDN 解密失败

|   |   | 
|---|---|
| 错误说明 |  [已处理/错误：解密失败] | 
| 用户操作 | 发送给伙伴之前，请添加 @base64ToBinary 到 MDN 
```java
            "Response": {
                "inputs": {
                    "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
                    "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
                    "statusCode": 200
                },
                
``` 

### <a name="-missing-signing-certificate"></a>* 缺少签名证书

|   |   |  
|---|---|
| 错误说明| 未对 AS2 参与方配置签名证书。 </br> AS2-From：partner1 AS2-To：partner2 | 
| 用户操作 | 使用正确的签名证书配置 AS2 协议设置 |
|  |  | 

## <a name="x12-and-edifact"></a>X12 和 EDIFACT

### <a name="-leading-or-trailing-space-found"></a>* 找到前导或尾部空格    
    
|   |   | 
|---|---|
| 错误说明 | 分析过程中所遇到的错误。 ID 为 "987654"、发送方 ID 为 "Partner1"、接收方 id 为 "Partner2" 的交换（没有组）中包含的 ID 为 "123456" 的 EDIFACT 事务集由于以下错误被挂起：找到前导或尾部分隔符 |
| 用户操作 | 将协议设置配置为允许前导或尾部空格。 </br> 将协议设置编辑为允许前导或尾部空格 |
|   |   |

![允许空格](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="-duplicate-check-has-enabled-in-the-agreement"></a>* 协议已启用重复项检查

|   |   | 
|---|---| 
| 错误说明 | 重复控制编号 |
| 用户操作 | 此错误表明收到的消息有重复控制编号。 </br> 更正控制编号并重新发送消息 |
|   |   |

### <a name="-missing-schema-in-the-agreement"></a>* 协议中缺少架构

|   |   | 
|---|---| 
| 错误说明 | 分析过程中所遇到的错误。 ID 为 "56422" 的功能组和 ID 为 "000056422"、发送方 ID 为 "12345678"       、接收方 id 为 "87654321       "的交换中包含的 ID 为 "564220001" 的 X12 事务集由于以下错误被挂起：“消息具有未知的文档类型，无法解析为协议中配置的任何现有架构” |
| 用户操作 | 在协议设置中配置架构  |
|   |   |

### <a name="-incorrect-schema-in-the-agreement"></a>* 协议中架构不正确

|   |   | 
|---|---| 
| 错误说明 | 消息具有未知的文档类型，无法解析为协议中配置的任何现有架构。 |
| 用户操作 | 在协议设置中配置正确的架构  |
|   |   |

## <a name="flat-file"></a>平面文件

### <a name="-input-message-with-no-body"></a>* 输入的消息没有正文

|   |   | 
|---|---|
| 错误说明 | InvalidTemplate。 无法处理操作 "Flat_File_Decoding" 输入中的行 "1" 和列 "1902" 处的模板语言表达式：所需属性“内容”应该有一个值，但为空。 路径 ''.'. |
| 用户操作 | 此错误表明输入的消息不包含正文 |
|   |   | 

## <a name="learn-more"></a>了解详细信息
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md)
