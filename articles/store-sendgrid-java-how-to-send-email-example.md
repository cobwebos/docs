---
title: store-sendgrid-java-how-to-send-email-example
description: "如何在 Azure 部署中使用 SendGrid 从 Java 发送电子邮件"
services: 
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>如何在 Azure 部署中使用 SendGrid 从 Java 发送电子邮件
下面的示例演示如何使用 SendGrid 从在 Azure 中托管的网页发送电子邮件。 生成的应用程序将提示用户输入电子邮件值，如下面的屏幕快照中所示。

![电子邮件窗体][emailform]

所得的电子邮件将类似于下面的屏幕快照。

![电子邮件][emailsent]

你将需要执行以下操作来使用本主题中的代码：

1. 获取 javax.mail Jar，例如从<http://www.oracle.com/technetwork/java/javamail/index.html>。
2. 将这些 Jar 添加到 Java 生成路径。
3. 如果你使用 Eclipse 创建此 Java 应用程序，您可以在使用 Eclipse 的部署程序集功能你应用程序部署文件 (WAR) 中包含的 SendGrid 库。 如果你不使用 Eclipse 创建此 Java 应用程序，请确保库包含在与 Java 应用程序，相同的 Azure 角色，并将其添加到你的应用程序的类路径。

你还必须具有自己的 SendGrid 用户名和密码，若要能够发送电子邮件。 若要开始使用 SendGrid，请参阅[如何发送电子邮件通过 Java 使用 SendGrid](store-sendgrid-java-how-to-send-email.md)。

此外，熟悉的信息[在 Eclipse 中创建用于 Azure 的 Hello World 应用程序](http://msdn.microsoft.com/library/windowsazure/hh690944)，或如果你未使用 Eclipse，则托管在 Azure 中的 Java 应用程序的其他方法，强烈建议。

## <a name="create-a-web-form-for-sending-email"></a>创建用于发送电子邮件的 web 窗体
下面的代码演示如何创建 web 窗体检索用于发送电子邮件的用户数据。 用于此内容，名为 JSP 文件**emailform.jsp**。

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
下面的代码，它在填写 emailform.jsp 窗体时调用，创建电子邮件消息，并将其发送。 用于此内容，名为 JSP 文件**sendemail.jsp**。

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

除了发送电子邮件，emailform.jsp 所提供的用户; 的结果一个示例是下面的屏幕快照：

![发送邮件结果][emailresult]

## <a name="next-steps"></a>后续步骤
应用程序部署到计算仿真程序和浏览器中运行 emailform.jsp，在窗体中输入值，单击**发送此电子邮件**，然后查看在 sendemail.jsp 中的结果。

提供此代码是为了向您展示如何在 Azure 上使用 SendGrid java。 在生产中部署到 Azure 之前, 可能想要添加更多错误处理或其他功能。 例如： 

* 你可以使用 Azure 存储 blob 或 SQL 数据库存储电子邮件地址和电子邮件，而不是使用 web 窗体。 有关使用 java 的 Azure 存储 blob 的信息，请参阅[如何使用 Blob 存储服务中通过 Java](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/)。 有关使用在 Java 中的 SQL 数据库的信息，请参阅[在 Java 中使用 SQL 数据库](https://azure.microsoft.com/develop/java/how-to-guides/using-sql-azure-in-java/)。
* 你可以使用`RoleEnvironment.getConfigurationSettings`从部署的配置设置，而不是使用 web 窗体检索这些值中检索 SendGrid 用户名和密码。 璝惠`RoleEnvironment`类，请参阅[使用 Azure 服务运行时库在 JSP 中](http://msdn.microsoft.com/library/windowsazure/hh690948)和 Azure 服务运行时程序包文档<http://dl.windowsazure.com/javadoc>。
* 有关在 Java 中使用 SendGrid 的详细信息，请参阅[如何发送电子邮件通过 Java 使用 SendGrid](store-sendgrid-java-how-to-send-email.md)。

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
