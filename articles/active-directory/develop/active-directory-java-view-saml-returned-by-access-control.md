---
title: 查看 Access Control 服务返回的 SAML (Java)
description: 了解如何通过 Azure 上托管的 Java 应用程序查看 Access Control 服务返回的 SAML。
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: 6cd216f9-eb43-46b4-b30d-f194d0ae2d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: 28c43b27d58d78036dd832fb4f95f2bc9958441c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>如何查看 Azure Access Control 服务返回的 SAML
本指南演示如何查看 Azure Access Control 服务 (ACS) 返回到应用程序的基础安全声明标记语言 (SAML)。 本指南基于[如何使用 Eclipse 通过 Azure 访问控制服务对 Web 用户进行身份验证](active-directory-java-authenticate-users-access-control-eclipse.md)主题，并提供了用于显示 SAML 信息的代码。 完整的应用程序与下图中所示类似。

![示例 SAML 输出][saml_output]

有关 ACS 的详细信息，请参阅[后续步骤](#next_steps)部分。

> [!NOTE]
> Azure 访问服务控制筛选器是一个社区技术预览版。 作为预发行软件，Microsoft 不会为它提供正式支持。
> 
> 

## <a name="prerequisites"></a>先决条件
要完成本指南中的任务，请完成[如何使用 Eclipse 向 Azure 访问控制服务对 Web 用户进行身份验证](active-directory-java-authenticate-users-access-control-eclipse.md)主题中的示例，并将其用作本指南的起点。

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>将 JspWriter 库添加到生成路径和部署程序集
将包含 **javax.servlet.jsp.JspWriter** 类的库添加到生成路径和部署程序集。 如果使用了 Tomcat，则该库为位于 Apache **lib** 文件夹中的 **jsp-api.jar**。

1. 在 Eclipse 的项目资源管理器中，右键单击“MyACSHelloWorld”，并依次单击“生成路径”、“配置生成路径”、“库”选项卡和“添加外部 JAR”。
2. 在“JAR 选择”对话框中，导航到所需的 JAR，选择该 JAR，并单击“打开”。
3. 在“MyACSHelloWorld 的属性”对话框保持打开的情况下，单击“部署程序集”。
4. 在“Web 部署程序集”对话框中，单击“添加”。
5. 在“新建程序集指令”对话框中，单击“Java 生成路径项”，并单击“下一步”。
6. 选择相应的库，并单击“完成”。
7. 单击“确定”关闭“MyACSHelloWorld 的属性”对话框。

## <a name="modify-the-jsp-file-to-display-saml"></a>修改 JSP 文件以显示 SAML
修改 **index.jsp** 以使用以下代码。

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {

            try
            {
                String nodeName;
                int nChild, i;

                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");

                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }

                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                    {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    

                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                        for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);

                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }

                                     }
                                     out.println("<br>");

                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>

        <%
        try
        {
            String data  = (String) request.getAttribute("ACSSAML");

            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();

            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA);
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();

            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();

            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e)
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }

        %>
    </body>
    </html>

## <a name="run-the-application"></a>运行应用程序
1. 按照如何[使用 Eclipse 在 Azure 访问控制服务中对 Web 用户进行身份验证](active-directory-java-authenticate-users-access-control-eclipse.md)主题所述的步骤，在计算模拟器中运行应用程序或部署到 Azure。
2. 启动浏览器并打开 Web 应用程序。 登录到应用程序后，会看到 SAML 信息，包括标识提供程序提供的安全声明。

## <a name="next-steps"></a>后续步骤
要进一步探索 ACS 的功能并尝试将其用于更复杂的方案，请参阅[访问控制服务 2.0][Access Control Service 2.0]。

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
