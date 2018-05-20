---
title: 在 Azure API 管理中配置通知和电子邮件模板 | Microsoft 文档
description: 了解如何在 Azure API 管理中配置通知和电子邮件模板。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 60788f76dac58ead10e43e892d587a86bdd3fcad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>如何在 Azure API 管理中配置通知和电子邮件模板
API 管理提供的功能为特定事件配置通知，以及配置用于和 API 管理实例的管理员及开发人员通信的电子邮件模板。 本文演示如何为可用事件配置通知，并提供配置用于这些事件的电子邮件模板的概述。

## <a name="prerequisites"></a>先决条件

如果没有 API 管理服务实例，请完成以下快速入门：[创建 Azure API 管理实例](get-started-create-service-instance.md)。

## <a name="publisher-notifications"></a>配置通知

1. 选择 **API 管理**实例。
2. 单击“通知”即可查看可用的通知。

    ![发布者通知][api-management-publisher-notifications]

    为通知可以配置以下事件的列表。

    * **订阅请求（需要批准）** - 指定的电子邮件收件人和用户将收到关于需要批准的 API 产品的订阅请求的电子邮件通知。
    * **新订阅** - 指定的电子邮件收件人和用户将收到关于新 API 产品订阅的电子邮件通知。
    * **应用程序库请求** - 指定电子邮件收件人和用户会在新的应用程序提交到应用程序库时收到电子邮件通知。
    * **BCC** - 指定电子邮件收件人和用户将收到发送给开发人员的所有电子邮件的电子邮件密件副本。
    * **新的问题或评论** - 在开发人员门户上提交新问题或注释时，指定电子邮件收件人和用户将收到电子邮件通知。
    * **关闭帐户消息** - 指定电子邮件收件人和用户会在关闭帐户时收到电子邮件通知。
    * **接近订阅配额限制** - 以下电子邮件收件人和用户会在订阅使用量接近使用量配额时收到电子邮件通知。

    对于每个事件，可以指定电子邮件收件人使用电子邮件地址文本框，或从列表中选择用户。

3. 要指定被通知的电子邮件地址，请在电子邮件地址文本框中输入。 如果有多个电子邮件地址，使用逗号分隔它们。

    ![通知收件人][api-management-email-addresses]
4. 按“添加”。

## <a name="email-templates"></a>配置通知模板
API 管理提供了在管理和使用服务的过程中发送的电子邮件的通知模板。 提供以下电子邮件模板。

* 批准的应用程序库提交
* 开发人员告别字母
* 开发人员配额限制接近通知
* 邀请用户
* 添加到问题的新注释
* 接收到的新问题
* 激活的新订阅
* 订阅续订确认
* 订阅请求拒绝
* 接收的订阅请求

可按需修改这些模板。

若要查看和配置 API 管理实例的电子邮件模板，请单击“通知模板”。

![电子邮件模板][api-management-email-templates]

每个电子邮件模板都有纯文本格式的主题，和 HTML 格式的正文定义。 可按需自定义每一项。

![电子邮件模板编辑器][api-management-email-template]

“参数”列表包含参数列表，插入到主题或正文时，会在发送电子邮件时替换为指定的值。 要插入一个参数，将光标置于要存放参数的位置，然后单击参数名称左侧的箭头。

> [!NOTE] 
> 预览或发送测试时该参数不会替换为实际值。

若要将更改保存到电子邮件模板，请单击“保存”，或要取消更改时单击“放弃”。
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
