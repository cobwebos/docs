---
title: "导出 DocumentDB 模拟器证书 | Microsoft Docs"
description: "以不使用 Windows 证书存储的语言和运行时进行开发时，需要导出并管理 SSL 证书。 本文提供分步说明。"
services: documentdb
documentationcenter: 
keywords: "DocumentDB 模拟器"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: c47ff7045a7c69f2f4a0235fa591a6a8cc820192
ms.openlocfilehash: dc3d297f44fb99b6fad58810eb31f429fdb1bd25

---

# <a name="export-the-documentdb-emulator-certificates"></a>导出 DocumentDB 模拟器证书

[**下载模拟器**](https://aka.ms/documentdb-emulator)

为进行开发，Azure DocumentDB 模拟器提供了一个模拟 Azure DocumentDB 服务的本地环境（包括使用 SSL 连接）。 本文演介绍了如何导出 SSL 证书以用于不与 Windows 证书存储集成的语言和运行时，例如使用自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)的 Java 和使用[套接字包装](https://docs.python.org/2/library/ssl.html)的 Python。 有关模拟器的详细信息，可参阅[使用 Azure DocumentDB 模拟器进行开发和测试](./documentdb-nosql-local-emulator.md)一文。

# <a name="certification-rotation"></a>证书旋转

DocumentDB 本地模拟器中的证书在首次运行模拟器时生成。 证书有两个。 一个用于连接到本地模拟器，另一个用于管理模拟器中的机密。 要导出的证书是具有“DocumentDBEmulatorCertificate”友好名称的连接证书。

在 Windows 托盘中运行的 DocumentDB 本地模拟器中，单击“重置数据”（如下所示），可重新生成这两个证书。 如果重新生成证书并将它们安装到 Java 证书存储或在其他地方使用，则需要更新证书，否则应用程序将不再连接到本地模拟器。

![DocumentDB 本地模拟器重置数据](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

# <a name="how-to-export-the-documentdb-ssl-certificate"></a>如何导出 DocumentDB SSL 证书

- 步骤 1：运行 certlm.msc 启动 Windows 证书管理器并导航到“个人”->“证书”文件夹，然后打开具有友好名称“DocumentDBEmulatorCertificate”的证书。

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

- 步骤 2：单击“详细信息”，然后单击“确定”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

- 步骤 3：单击“复制到文件”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

- 步骤 4：单击“下一步”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

- 步骤 5：单击“否，不导出私钥”，然后单击“下一步”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

- 步骤 6：单击“Base-64 编码的 X.509 (.CER)”，然后单击“下一步”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

- 步骤 7：提供证书名称。 本示例中为“documentdbemulatorcert”，然后单击“下一步”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

- 步骤 8：单击“完成”

![DocumentDB 本地模拟器导出步骤 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

# <a name="how-to-use-the-certificate-in-java"></a>如何在 Java 中使用证书

运行使用 Java 客户端的 Java 应用程序或 MongoDB 应用程序时，将证书安装到 Java 默认证书存储比传递“-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>”标志更简单。 例如，包含的 [Java 演示应用程序](https://localhost:8081/_explorer/index.html)取决于默认证书存储。

按照“[Adding a Certificate to the Java CA Certificates Store](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)”（将证书添加到 Java CA 证书存储）一文中的说明将 X.509 证书导入默认 Java 证书存储。 请记住，需在 ％JAVA_HOME％ 目录中运行 keytool。

安装“DocumentDBEmulatorCertificate”SSL 证书后，应用程序应能连接可使用本地 DocumentDB 模拟器。 如果仍遇到问题，请按照“[Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)”（调试 SSL / TLS 连接）一文操作。 很有可能是该证书未安装到 %JAVA_HOME%/jre/lib/security/cacerts 存储中。 例如，如果安装了多个 Java 版本，应用程序使用的 cacerts 存储可能未更新。

# <a name="how-to-use-the-certificate-in-python"></a>如何在 Python 中使用证书

默认情况下，用于 DocumentDB 的 Python SDK 在连接到本地模拟器时不会尝试使用 SSL 证书。 但如果要使用 SSL 验证，可以按照 [Python 套接字包装器](https://docs.python.org/2/library/ssl.html)一文中的示例操作。



<!--HONumber=Nov16_HO5-->


