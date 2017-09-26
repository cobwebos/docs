---
title: "在 Azure API 管理中配置通知和电子邮件模板 | Microsoft 文档"
description: "了解如何在 Azure API 管理中配置通知和电子邮件模板。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: ee25f26d-4752-433b-af9c-3817db38aed5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: 3d8b74e32059cfc1a4c3a8fc7d3bd04676ee80c8
ms.contentlocale: zh-cn
ms.lasthandoff: 01/31/2017

---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>如何在 Azure API 管理中配置通知和电子邮件模板
API 管理提供的功能为特定事件配置通知，以及配置用于和 API 管理实例的管理员及开发人员通信的电子邮件模板。 本主题演示如何为可用事件配置通知，并提供配置用于这些事件的电子邮件模板的概述。

## <a name="publisher-notifications"> </a>配置发布者通知
要配置通知，请单击 API 管理服务 Azure 门户中的“发布者门户”。 这将转到 API 管理发布者门户。

![发布者门户][api-management-management-console]

> [!NOTE] 
> 如果尚未创建 API 管理服务实例，请参阅 [Azure API 管理入门][Get started with Azure API Management]教程中的[创建 API 管理服务实例][Create an API Management service instance]。

单击“左侧 API 管理”菜单中的“通知”查看可用通知。

![发布者通知][api-management-publisher-notifications]

为通知可以配置以下事件的列表。

* **订阅请求（需要批准）** - 指定的电子邮件收件人和用户将收到关于需要批准的 API 产品的订阅请求的电子邮件通知。
* **新订阅** - 指定的电子邮件收件人和用户将收到关于新 API 产品订阅的电子邮件通知。
* **应用程序库请求** - 指定电子邮件收件人和用户将在新的应用程序提交到应用程序库时收到电子邮件通知。
* **BCC** - 指定电子邮件收件人和用户将收到发送给开发人员的所有电子邮件的电子邮件密件副本。
* **新的问题或评论** - 在开发人员门户上提交新问题或注释时，指定电子邮件收件人和用户将收到电子邮件通知。
* **关闭帐户消息** - 指定电子邮件收件人和用户将在关闭帐户时收到电子邮件通知。
* **接近订阅配额限制** - 以下电子邮件收件人和用户将在订阅使用量接近使用量配额时收到电子邮件通知。

对于每个事件，您可以指定电子邮件收件人使用电子邮件地址文本框，或从列表中选择用户。

要指定被通知的电子邮件地址，请在电子邮件地址文本框中输入。 如果有多个电子邮件地址，使用逗号分隔它们。

![通知收件人][api-management-email-addresses]

若要指定被通知的用户，请单击“添加收件人”，选中要得到通知的用户旁边的框，然后单击“确定”。

> [!NOTE] 
> 只有管理员才会显示在列表中。


配置通知收件人后，单击“保存”应用更新后的通知收件人。

> [!NOTE] 
> 如果导航离开“发布者通知”选项卡，发布者门户将在存在未保存更改时发出警报。


## <a name="email-templates"> </a>配置电子邮件模板
API 管理提供了在管理和使用服务的过程中发送的电子邮件的电子邮件模板。 提供以下电子邮件模板。

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

若要查看和配置 API 管理实例的电子邮件模板，请单击左侧“API 管理”菜单的“通知”，然后选择“电子邮件模板”选项卡。

![电子邮件模板][api-management-email-templates]

若要查看或修改特定模板，可以从“模板”下拉列表选择它。

![电子邮件模板列表][api-management-email-templates-list]

每个电子邮件模板都有纯文本格式的主题，和 HTML 格式的正文定义。 可按需自定义每一项。

![电子邮件模板编辑器][api-management-email-template]

“参数”列表包含参数列表，插入到主题或正文时，将在发送电子邮件时替换为指定的值。 要插入一个参数，将光标置于要存放参数的位置，然后单击参数名称左侧的箭头。

单击“预览”或“发送测试”，了解电子邮件的外观或发送测试电子邮件。

> [!NOTE] 
> 预览或发送测试时该参数不会替换为实际值。

若要将更改保存到电子邮件模板，请单击“保存”，或取消更改时单击“取消”。
 

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

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

