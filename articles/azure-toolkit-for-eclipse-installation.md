---
title: "安装用于 Eclipse 的 Azure 工具包 | Microsoft Docs"
description: "了解如何安装用于 Eclipse 的 Azure 工具包。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9e93ff6a-f42b-4d99-b55b-624136b4a730
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 35cddba38c364dfb2f6a8646b0014d48ca4cb795
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="installing-the-azure-toolkit-for-eclipse"></a>安装用于 Eclipse 的 Azure 工具包
使用 Azure Toolkit for Eclipse 提供的模板和功能，可轻松地利用 Eclipse 开发环境创建、开发、测试和部署 Azure 应用程序。 适用于 Eclipse 的 Azure 工具包是一个开源项目。 源代码在 MIT 许可下可用，可从 <https://github.com/microsoft/azure-tools-for-java> 中获得。

以下步骤说明如何安装用于 Eclipse 的 Azure 工具包。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>安装用于 Eclipse 的 Azure 工具包
1. 启动 Eclipse。
2. 单击“帮助”菜单，然后单击“安装新软件”，如下图所示。
   
    ![安装用于 Eclipse 的 Azure 工具包][01]
3. 在“可用软件”对话框的“使用”文本框中，键入 `http://dl.microsoft.com/eclipse`，然后按 Enter 键。
4. 在“名称”窗格中，选中“用于 Eclipse 的 Azure 工具包”，并取消选中“在安装过程中访问所有更新站点以查找所需的软件”。 你的屏幕应与下图中所示类似：
   
    ![安装用于 Eclipse 的 Azure 工具包][02]
5. 如果展开“用于 Eclipse 的 Azure 工具包”，可以看到以下项：
   
   * **用于 Java 的 Application Insights 插件**：使用此组件可将 Azure 的遥测日志记录和分析服务用于应用程序和服务器实例。
   * **Azure 访问控制服务筛选器**：此组件对以下情况提供支持：向 Azure ACS 验证应用程序用户的身份，启用单一登录方案，以及从应用程序具体化标识逻辑。
   * **Azure 常用插件**：此组件提供其他工具包组件所需的常见功能。
   * **用于 Eclipse 的 Azure 资源管理器**：此组件提供其他工具包组件所需的常见功能。
   * **用于 Eclipse 且支持 Java 的 Azure 插件**：此组件对以下情况提供支持：在 Eclipse 中，通过命令行开发可帮助构建、测试和部署适用于 Microsoft Azure 云的 Java 应用程序的项目。
   * **支持 Java 的 Azure Web 应用插件**：此组件在将 Java Web 应用程序部署到 Microsoft Azure Web 应用容器时提供支持。
   * **Microsoft JDBC Driver 4.2 for SQL Server**：此组件提供适用于 SQL Server 的 JDBC API 以及适用于 Java Platform Enterprise Edition 8 的 Microsoft Azure SQL 数据库。
   * **Apache Qpid JMS 客户端库包**：此组件提供 Apache Qpid 项目中的 JMS 客户端组件，以允许应用程序在 Microsoft Azure 中使用 AMQP 消息传送。
   * **Microsoft Azure Java 库包**：此组件提供用于访问 Microsoft Azure 服务（例如存储、服务总线、服务运行时等等）的 API。
6. 单击“下一步”。 （如果在安装该工具包时遇到不正常的延迟，请确保未选中“在安装过程中访问所有更新站点以查找所需的软件”。）
7. 在“安装详细信息”对话框中，单击“下一步”。
   
    ![查看安装详细信息][03]
8. 在“查看许可证”对话框中，查看许可协议条款。 如果接受许可协议条款，请单击“我接受许可协议条款”，然后单击“完成”。 （剩余步骤假定你接受许可协议条款。 如果你不接受许可协议条款，请退出安装过程。）
   
    ![查看许可证][04]
   
    Eclipse 将下载并安装必要的包。
   
    ![安装进度][05]
9. 如果系统提示重新启动 Eclipse 以完成安装，请单击“是”。
   
    ![重新启动提示][06]

## <a name="see-also"></a>另请参阅
有关 Azure Toolkits for Java IDE 的详细信息，请参阅以下链接：

* [用于 Eclipse 的 Azure 工具包]
  * [用于 Eclipse 的 Azure 工具包的新增功能]
  * *安装用于 Eclipse 的 Azure 工具包（本文）*
  * [用于 Eclipse 的 Azure 工具包的登录说明]
  * [在 Eclipse 中创建 Azure 的 Hello World Web 应用]
* [用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的新增功能]
  * [安装用于 IntelliJ 的 Azure 工具包]
  * [用于 IntelliJ 的 Azure 工具包的登录说明]
  * [在 IntelliJ 中创建 Azure 的 Hello World Web 应用]

有关将 Azure 与 Java 配合使用的详细信息，请参阅 [Azure Java 开发人员中心]。

<!-- URL List -->

[用于 Eclipse 的 Azure 工具包]: ./azure-toolkit-for-eclipse.md
[用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij.md
[在 Eclipse 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中创建 Azure 的 Hello World Web 应用]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[安装用于 IntelliJ 的 Azure 工具包]: ./azure-toolkit-for-intellij-installation.md
[用于 Eclipse 的 Azure 工具包的登录说明]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[用于 IntelliJ 的 Azure 工具包的登录说明]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[用于 Eclipse 的 Azure 工具包的新增功能]: ./azure-toolkit-for-eclipse-whats-new.md
[用于 IntelliJ 的 Azure 工具包的新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 开发人员中心]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

