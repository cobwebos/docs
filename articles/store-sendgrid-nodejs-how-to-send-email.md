---
title: "如何使用 SendGrid 电子邮件服务 (Node.js) | Microsoft Docs"
description: "了解如何在 Azure 上使用 SendGrid 电子邮件服务发送电子邮件。 代码示例用 Node.js API 编写。"
services: 
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: 
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>如何使用 SendGrid 从 Node.js 发送电子邮件
本指南演示了如何在 Azure 上使用 SendGrid 电子邮件服务执行常见编程任务。 相关示例是使用 Node.js API 编写的。 涉及的任务包括**创建电子邮件**、**发送电子邮件**、**添加附件**、**使用筛选器**和**更新属性**。 有关 SendGrid 和发送电子邮件的详细信息，请参阅[后续步骤](#next-steps)部分。

## <a name="what-is-the-sendgrid-email-service"></a>什么是 SendGrid 电子邮件服务？
SendGrid 是一项[基于云的电子邮件服务]，该服务提供了可靠的[事务电子邮件传递]、伸缩性、实时分析以及可用于简化自定义集成的灵活的 API。 常见 SendGrid 使用方案包括：

* 自动向客户发送收据
* 管理用于每月向客户发送电子传单和特惠产品/服务的通讯组列表
* 收集诸如已阻止的电子邮件和客户响应性等项目的实时度量值
* 生成用于帮助确定趋势的报告
* 转发客户查询
* 以电子邮件的形式从应用程序发送通知

有关详细信息，请参阅 [https://sendgrid.com](https://sendgrid.com)。

## <a name="create-a-sendgrid-account"></a>创建 SendGrid 帐户
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>引用 SendGrid Node.js 模块
可使用以下命令通过 Node 包管理器 (npm) 安装用于 Node.js 的 SendGrid 模块：

    npm install sendgrid

安装之后，可使用以下代码要求应用程序中的模块：

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

SendGrid 模块可导出 **SendGrid** 和 **Email** 函数。
**SendGrid** 负责通过 Web API 发送电子邮件，而 **Email** 负责封装电子邮件。

## <a name="how-to-create-an-email"></a>如何：创建电子邮件
要使用 SendGrid 模块创建电子邮件，需要先使用 Email 函数创建电子邮件，然后使用 SendGrid 函数发送该邮件。 以下是使用 Email 函数创建新邮件的示例：

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

通过设置 html 属性，可以为支持 HTML 邮件的客户端指定该邮件。 例如：

    html: This is a sample <b>HTML<b> email message.

同时设置文本和 html 属性可以为无法支持 HTML 邮件的客户端提供文本内容的正常反馈。

有关 Email 函数支持的所有属性的详细信息，请参阅[sendgrid nodejs][sendgrid-nodejs]。

## <a name="how-to-send-an-email"></a>如何：发送电子邮件
使用 Email 函数创建电子邮件后，可使用 SendGrid 提供的 Web API 发送该邮件。 

### <a name="web-api"></a>Web API
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> 上面的示例演示传入电子邮件对象和回调函数，还可通过直接指定电子邮件属性来直接调用 send 函数。 例如：  
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>如何：添加附件
可通过在 **files** 属性中指定文件名和路径来将附件添加到邮件中。 下面的示例演示如何发送附件：

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> 使用 **files** 属性时，必须可通过[fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile) 访问文件。 如果要附加的文件托管在 Azure 存储中（如 Blob 容器中），必须先将该文件复制到本地存储或 Azure 驱动器，然后才能使用 **files** 属性将该文件作为附件发送。
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>如何：使用筛选器启用页脚和跟踪
SendGrid 可通过使用筛选器来提供其他电子邮件功能。 可将这些设置添加到电子邮件以启用特定功能（例如启用单击跟踪、Google 分析、订阅跟踪等）。 有关筛选器的完整列表，请参阅[筛选器设置][Filter Settings]。

可使用 **filters** 属性将筛选器应用于邮件。
每个筛选器均由一个包含特定于筛选器的设置的哈希指定。
以下示例演示了脚注和单击跟踪筛选器：

### <a name="footer"></a>脚注
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>单击跟踪
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>如何：更新电子邮件属性
可以使用覆盖一些电子邮件属性**设置*属性** * 或追加使用**添加*属性** *。 例如，可使用以下命令添加更多收件人：

    email.addTo('jeff@contoso.com');

或使用以下命令设置筛选器：

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

有关详细信息，请参阅[sendgrid nodejs][sendgrid-nodejs]。

## <a name="how-to-use-additional-sendgrid-services"></a>如何：使用其他 SendGrid 服务
SendGrid 提供了基于 Web 的 API，可通过这些 API 从 Azure 应用程序中使用其他 SendGrid 功能。 有关完整详细信息，请参阅 [SendGrid API 文档][SendGrid API documentation]。

## <a name="next-steps"></a>后续步骤
此时，已了解 SendGrid 电子邮件服务的基础知识，请访问以下链接以了解更多信息。

* SendGrid Node.js 模块存储库： [sendgrid nodejs][sendgrid-nodejs]
* SendGrid API 文档：<https://sendgrid.com/docs>
* 面向 Azure 客户的 SendGrid 特惠产品/服务：[http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[基于云的电子邮件服务]: https://sendgrid.com/email-solutions
[事务电子邮件传递]: https://sendgrid.com/transactional-email
