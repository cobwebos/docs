---
title: SMTP | Microsoft Docs
description: "使用 Azure App Service 创建逻辑应用。 连接到 SMTP 以发送电子邮件。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: 3a0fdef111fbd4a9f7491e247f2236cf70b89dca


---
# <a name="get-started-with-the-smtp-connector"></a>SMTP 连接器入门
连接到 SMTP 以发送电子邮件。

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../logic-apps/logic-apps-create-a-logic-app.md)开始操作。

## <a name="connect-to-smtp"></a>连接到 SMTP
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。 例如，若要连接到 SMTP，首先需要 SMTP *连接*。 若要创建连接，需要提供通常用于访问要连接到的服务的凭据。 因此在 SMTP 示例中，需要连接名称、SMTP 服务器地址和用户登录信息的凭据，以便创建到 SMTP 的连接。 [了解有关连接的详细信息]()  

### <a name="create-a-connection-to-smtp"></a>创建到 SMTP 的连接
> [!INCLUDE [Steps to create a connection to SMTP](../../includes/connectors-create-api-smtp.md)]
> 
> 

## <a name="use-an-smtp-trigger"></a>使用 SMTP 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

在此示例中，由于 SMTP 没有自己的触发器，因此使用“Salesforce - 创建对象时”触发器。 此触发器将在 Salesforce 中新建对象时激活。 对于此示例，我们将设置为每次在 Salesforce 中新建潜在客户时，通过 SMTP 连接器发生“发送电子邮件”操作，并附带要创建的新潜在客户的通知。

1. 在逻辑应用设计器上的搜索框中输入“salesforce”，然后选择“Salesforce - 创建对象时”触发器。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-1.png)  
2. 显示“创建对象时”控件。
   ![](../../includes/media/connectors-create-api-salesforce/trigger-2.png)  
3. 选择“对象类型”，然后从对象列表中选择“潜在客户”。 在此步骤中，将指示要创建一个触发器，每当在 Salesforce 中新建潜在客户时，该触发器都将通知逻辑应用。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger3.png)  
4. 已创建触发器。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger-4.png)  

## <a name="use-an-smtp-action"></a>使用 SMTP 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。

现在已添加触发器，请按照以下步骤添加 SMTP 操作，该操作将在 Salesforce 中新建潜在客户时发生。

1. 选择“+ 新步骤”，添加要在新建潜在客户时采取的操作。  
   ![](../../includes/media/connectors-create-api-salesforce/trigger4.png)  
2. 选择“添加操作”。 这将打开可搜索要采取的任何操作的搜索框。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-2.png)  
3. 输入“smtp”搜索与 SMTP 相关的操作。  
4. 选择“SMTP - 发送电子邮件”作为新建潜在客户时要采取的操作。 操作控制块打开。 如果之前未在设计器块中建立 SMTP 连接，必须执行此操作。  
   ![](../../includes/media/connectors-create-api-smtp/smtp-2.png)    
5. 在 **SMTP - 发送电子邮件**块中输入所需的电子邮件信息。  
   ![](../../includes/media/connectors-create-api-smtp/using-smtp-action-4.PNG)  
6. 保存工作，以便激活工作流。  

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="smtp-triggers"></a>SMTP 触发器
SMTP 没有触发器。 

## <a name="smtp-actions"></a>SMTP 操作
SMTP 具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [发送电子邮件](connectors-create-api-smtp.md#send-email) |此操作将一封电子邮件发送给一个或多个收件人。 |

### <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="send-email"></a>发送电子邮件
此操作将一封电子邮件发送给一个或多个收件人。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| 如果 |如果 |指定由分号分隔的电子邮件地址，如 recipient1@domain.com;recipient2@domain.com |
| CC |cc |指定由分号分隔的电子邮件地址，如 recipient1@domain.com;recipient2@domain.com |
| 使用者 |使用者 |电子邮件主题 |
| 正文 |正文 |电子邮件正文 |
| 从 |从 |发件人的电子邮件地址，如 sender@domain.com |
| IsHtml |属于 HTML |以 HTML 格式 (true/false) 发送电子邮件 |
| Bcc |bcc |指定由分号分隔的电子邮件地址，如 recipient1@domain.com;recipient2@domain.com |
| 重要性 |重要性 |电子邮件的重要性（高、正常或低） |
| ContentData |附件内容数据 |内容数据（对于流，base64 编码，对于字符串，按原样） |
| ContentType |附件内容类型 |内容类型 |
| ContentTransferEncoding |附件内容传输编码 |内容传输编码（base64 或无） |
| FileName |附件文件名 |文件名 |
| ContentId |附件内容 ID |内容 ID |

* 指示属性是必需的

## <a name="http-responses"></a>HTTP 响应
上述操作和触发器可以返回以下一个或多个 HTTP 状态代码： 

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


