---
title: store-sendgrid-java-how-to-send-email-example
description: 如何在 Azure 部署中通过 Java 使用 SendGrid 发送电子邮件
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: d80d7d9c54bad12a4d26d8623eeccdf9bc2a743a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23059792"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>如何在 Azure 部署中通过 Java 使用 SendGrid 发送电子邮件
以下示例演示如何能够使用 SendGrid 从在 Azure 中托管的网页上发送电子邮件。 所得的应用程序会提示用户输入电子邮件值，如以下屏幕快照中所示。

![“电子邮件”窗体][emailform]

所得的电子邮件将与以下屏幕快照类似。

![电子邮件][emailsent]

需要执行以下操作来使用本主题中的代码：

1. 获取 javax.mail JAR（例如从 <http://www.oracle.com/technetwork/java/javamail/index.html> 获取）。
2. 将这些 JAR 添加到 Java 生成路径。
3. 如果使用 Eclipse 创建此 Java 应用程序，则可使用 Eclipse 的部署程序集功能在应用程序部署文件 (WAR) 中加入 SendGrid 库。 如果不使用 Eclipse 创建此 Java 应用程序，请确保将这些库包含在与 Java 应用程序相同的 Azure 角色中，并将其添加到应用程序的类路径下。

还必须有自己的 SendGrid 用户名和密码，才能发送电子邮件。 若要开始使用 SendGrid，请参阅[如何通过 Java 使用 SendGrid 发送电子邮件](store-sendgrid-java-how-to-send-email.md)。

此外，强烈建议熟悉[在 Eclipse 中为 Azure 创建 Hello World 应用程序](http://msdn.microsoft.com/library/windowsazure/hh690944)上的信息，如果不使用 Eclipse，则强烈建议熟悉在 Azure 中托管 Java 应用程序的其他方法。

## <a name="create-a-web-form-for-sending-email"></a>创建用于发送电子邮件的 Web 窗体
以下代码演示如何创建 Web 窗体以检索用于发送电子邮件的用户数据。 在此内容中，JSP 文件的名称为 **emailform.jsp**。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>创建用于发送电子邮件的代码
以下代码创建电子邮件并发送它，在填写 emailform.jsp 中的窗体时会调用这段代码。 在此内容中，JSP 文件的名称为 **sendemail.jsp**。

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

除了发送电子邮件外，emailform.jsp 还向用户提供发送结果；以下屏幕快照就是一个例子：

![发送邮件结果][emailresult]

## <a name="next-steps"></a>后续步骤
将应用程序部署到计算模拟器，然后在浏览器内运行 emailform.jsp，在窗体中输入值，单击“发送此电子邮件”，然后在 sendemail.jsp 中查看结果。

提供这段代码是为了向你演示如何在 Azure 上通过 Java 使用 SendGrid。 在生产中部署到 Azure 之前，可能希望添加更多错误处理或其他功能。 例如： 

* 你可以使用 Azure 存储 BLOB 或 SQL 数据库（而不是使用 Web 窗体）存储电子邮件地址和电子邮件。 若要了解如何在 Java 中使用 Azure 存储 Blob，请参阅[如何从 Java 使用 Blob 存储服务](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)。 若要了解如何在 Java 中使用 SQL 数据库，请参阅[在 Java 中使用 SQL 数据库](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/)。
* 可以使用 `RoleEnvironment.getConfigurationSettings` 从部署的配置设置中检索 SendGrid 用户名和密码，而不使用 Web 窗体检索这些值。 有关 `RoleEnvironment` 类的信息，请参阅[在 JSP 中使用 Azure 服务运行时库](http://msdn.microsoft.com/library/windowsazure/hh690948)以及 <http://dl.windowsazure.com/javadoc> 上的 Azure 服务运行时程序包文档。
* 若要了解如何在 Java 中使用 SendGrid，请参阅[如何通过 Java 使用 SendGrid 发送电子邮件](store-sendgrid-java-how-to-send-email.md)。

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
