---
title: "在 Azure 逻辑应用中为 AS2 消息解码 | Microsoft 文档"
description: "如何使用 Enterprise Integration Pack 和逻辑应用随附的 AS2 解码器"
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
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 37562ff385305088590c793147b8ad268148c40b


---
# <a name="get-started-with-decoding-as2-message-in-your-logic-apps"></a>在逻辑应用中开始解码 AS2 消息
传输消息时连接到解码 AS2 消息以建立安全性和可靠性。 解码 AS2 消息通过邮件处置通知 (MDN) 提供数字签名、解密和确认等功能。

## <a name="prereqs"></a>先决条件
* Azure 帐户；可以创建[免费帐户](https://azure.microsoft.com/free)
* 使用解码 AS2 消息连接器需要有集成帐户。 请参阅有关如何创建[集成帐户](logic-apps-enterprise-integration-create-integration-account.md)、[合作伙伴](logic-apps-enterprise-integration-partners.md)和 [AS2 协议](../logic-apps/logic-apps-enterprise-integration-as2.md)的详细信息

## <a name="decode-as2-messages"></a>为 AS2 消息解码
1. [创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。
2. 此连接器没有任何触发器。 使用其他触发器启动逻辑应用（如请求触发器）。  在逻辑应用设计器中，添加触发器和操作。  在下拉列表中选择“显示 Microsoft 托管的 API”，然后在搜索框中输入“AS2”。  选择“AS2 - 解码 AS2 消息”：
   
    ![搜索 AS2](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)
3. 如果事先未与集成帐户建立任何连接，系统会提示输入连接详细信息：
   
    ![创建集成连接](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)
4. 输入集成帐户详细信息。  带星号的属性是必填的：
   
   | 属性 | 详细信息 |
   | --- | --- |
   | 连接名称 * |为连接输入任何名称 |
   | 集成帐户 * |输入集成帐户名称。 确保集成帐户和逻辑应用处于相同 Azure 位置 |
   
      完成之后，连接详细信息与下面的内容类似
   
      ![集成连接](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)
5. 选择“创建” 。
6. 请注意，连接已创建。  现在，继续在逻辑应用中执行其他步骤：
   
    ![已创建集成连接](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 
7. 从请求输出中选择正文和标头：
   
    ![提供必填字段](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 解码器详细信息
解码 AS2 连接器执行以下操作： 

* 处理 AS2/HTTP 标头
* 验证签名（如果已配置）
* 对消息进行解密（如果已配置）
* 解压缩消息（如果已配置）
* 协调收到的 MDN 和原始的出站消息
* 更新并关联不可否认数据库中的记录
* 写入 AS2 状态报告的记录
* 输出有效负载内容采用了 base64 编码
* 根据 AS2 协议中的配置确定是否需要 MDN，以及 MDN 应该同步还是异步
* 生成同步或异步 MDN（基于协议的配置）
* 在 MDN 上设置关联令牌和属性

## <a name="try-it-yourself"></a>自行试用
试试看。 使用 [AS2 逻辑应用模板和方案](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)部署完全正常运行的逻辑应用。

## <a name="next-steps"></a>后续步骤
[了解有关 Enterprise Integration Pack 的详细信息](logic-apps-enterprise-integration-overview.md) 




<!--HONumber=Jan17_HO5-->


