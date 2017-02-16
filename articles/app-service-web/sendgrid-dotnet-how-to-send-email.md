---
title: "如何使用 SendGrid 电子邮件服务 (.NET) | Microsoft Docs"
description: "了解如何在 Azure 上使用 SendGrid 电子邮件服务发送电子邮件。 代码示例用 C# 编写且使用 .NET API。"
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: dwrede
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/14/2016
ms.author: team-pi@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: dc1dac6e590088b45aa7afb87cf2d41027506635
ms.openlocfilehash: 5ad1d7f026836e90d04d493bd1c0454b83f936ef


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>如何在 Azure 中使用 SendGrid 发送电子邮件
## <a name="overview"></a>概述
本指南演示了如何在 Azure 上使用 SendGrid 电子邮件服务执行常见编程任务。 相关示例用 C\#
编写并使用 .NET API。 涉及的任务包括**创建电子邮件**、**发送电子邮件**、**添加附件**和**使用筛选器**。 有关 SendGrid 和发送电子邮件的详细信息，请参阅[后续步骤][后续步骤]部分。

## <a name="what-is-the-sendgrid-email-service"></a>什么是 SendGrid 电子邮件服务？
SendGrid 是一项[基于云的电子邮件服务]，该服务提供了可靠的[事务电子邮件传递]、可缩放性、实时分析以及可用于简化自定义集成的灵活的 API。 常见 SendGrid 使用方案包括：

* 自动向客户发送收据。
* 管理用于每月向客户发送电子传单和特惠产品/服务的通讯组列表。
* 收集诸如已阻止的电子邮件和客户响应性等项目的实时度量值。
* 生成用于帮助确定趋势的报告。
* 转发客户查询。
* 处理传入的电子邮件。

有关详细信息，请参阅 [https://sendgrid.com](https://sendgrid.com) 或 [C# 库][sendgrid-csharp]

## <a name="create-a-sendgrid-account"></a>创建 SendGrid 帐户
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>引用 SendGrid.NET 类库
[SendGrid NuGet 包](https://www.nuget.org/packages/Sendgrid)是获取 SendGrid API 和使用所有依赖项配置应用程序的最简单方法。 NuGet 是 Microsoft Visual Studio 2015 随附的一个 Visual Studio 扩展，可让你轻松安装和更新库与工具。 

> [!NOTE]
> 如果运行的 Visual Studio 版本低于 Visual Studio 2015，并且想要安装 NuGet，那么，请访问 [http://www.nuget.org](http://www.nuget.org)，然后单击“安装 NuGet”按钮。
> 
> 

若要在应用程序中安装 SendGrid NuGet 包，请执行以下操作：

1. 创建新项目。
   
   ![创建新项目][create-new-project]
2. 选择模板。
   
   ![选择模板][select-a-template]
3. 在“解决方案资源管理器”中，右键单击“引用”，然后单击“管理 NuGet 包”。
4. 搜索 **SendGrid**，然后在结果列表中选择 **SendGrid** 项。
   
   ![SendGrid NuGet 包][SendGrid-NuGet-package]
5. 从版本下拉列表中选择“6.3.4 版”的 Nuget 包，以便能够使用本文中演示的对象模型和 API
6. 单击“安装”以完成安装，然后关闭此对话框。

SendGrid 的 .NET 类库名为 **SendGridMail**。 其中包含以下命名空间：

* **SendGridMail**：用于创建和处理电子邮件项。
* **SendGridMail.Transport**：用于通过 **SMTP** 协议或采用 **Web/REST** 的 HTTP 1.1 协议发送电子邮件。

在想要以编程方式访问 SendGrid 电子邮件服务的任何 C\# 文件中，将以下代码命名空间声明添加到文件的顶部。
**System.Net** 和 **System.Net.Mail** 均为 .NET Framework 命名空间，加入它们的原因是包含通常要配合 SendGrid API 使用的类型。

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>如何：创建电子邮件
使用 **SendGridMessage** 对象创建电子邮件。 创建邮件对象后，可以设置属性和方法，包括电子邮件发件人、电子邮件收件人以及电子邮件的主题和正文。

以下示例演示了如何创建完全填充的电子邮件对象：

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

若要深入了解 **SendGrid** 类型支持的所有属性和方法的详细信息，请参阅 GitHub 上的 [sendgrid-csharp][sendgrid-csharp]。

## <a name="how-to-send-an-email"></a>如何：发送电子邮件
创建电子邮件后，可以使用 SendGrid 提供的 Web API 发送该邮件。 或者，可以[使用 .NET 的内置库](https://sendgrid.com/docs/Code_Examples/csharp.html)。

发送电子邮件都需要你提供SendGrid 帐户凭据（用户名和密码）或 SendGrid API 密钥。 API 密钥是首选方法。 如需有关如何配置 API 密钥的详细信息，请访问相关[文档](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

可通过 Azure 门户存储这些凭据，方法是单击“配置”，再在“应用设置”下添加键/值对。

 ![Azure 应用设置][azure_app_settings]

 然后，可按如下所示访问这些凭据： 

    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

使用凭据：

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

使用 API 密钥：

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


以下示例演示如何使用 Web API 发送邮件。

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>如何：添加附件
可以通过调用 **AddAttachment** 方法并指定要附加的文件名和路径，将附件添加到邮件。
对想要附加的每个文件调用此方法一次，即可包含多个附件。 以下示例演示了如何将附件添加到邮件：

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

还可以从数据**流**添加附件。 可以调用与上述相同的 **AddAttachment** 方法来执行此操作，不过这次要传入数据的流，以及要在邮件中显示的文件名。 在此情况下，需要添加 System.IO 库。

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>如何：使用应用来启用页脚、跟踪和分析
SendGrid 通过使用应用提供其他电子邮件功能。 可将这些设置添加到电子邮件以启用特定功能（例如单击跟踪、Google 分析、订阅跟踪等）。 有关应用的完整列表，请参阅[应用设置][应用设置]。

可以使用作为 **SendGrid** 类的一部分实现的方法将应用应用到 **SendGrid** 电子邮件。

以下示例演示了脚注和单击跟踪筛选器：

### <a name="footer"></a>脚注
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>单击跟踪
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>如何：使用其他 SendGrid 服务
SendGrid 提供了基于 Web 的 API 和 Webhook，可通过这些 API 从 Azure 应用程序中使用其他 SendGrid 功能。 有关完整详细信息，请参阅 [SendGrid API 文档][SendGrid API 文档]。

## <a name="next-steps"></a>后续步骤
此时，你已了解 SendGrid 电子邮件服务的基础知识，请访问以下链接以了解更多信息。

* SendGrid C\# 库存储库：[sendgrid-csharp][sendgrid-csharp]
* SendGrid API 文档：<https://sendgrid.com/docs>
* 面向 Azure 客户的 SendGrid 特惠产品：[https://sendgrid.com](https://sendgrid.com)

[后续步骤]: #next-steps
[什么是 SendGrid 电子邮件服务？]: #whatis
[创建 SendGrid 帐户]: #createaccount
[引用 SendGrid.NET 类库]: #reference
[如何：创建电子邮件]: #createemail
[如何：发送电子邮件]: #sendemail
[如何：添加附件]: #addattachment
[如何：使用筛选器启用页脚、跟踪和分析]: #usefilters
[如何：使用其他 SendGrid 服务]: #useservices

[特惠]: https://www.sendgrid.com/windowsazure.html

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
[select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP 与Web API]: https://sendgrid.com/docs/Integrate/index.html
[应用设置]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API 文档]: https://sendgrid.com/docs

[基于云的电子邮件服务]: https://sendgrid.com/email-solutions
[事务电子邮件传递]: https://sendgrid.com/transactional-email




<!--HONumber=Nov16_HO5-->


