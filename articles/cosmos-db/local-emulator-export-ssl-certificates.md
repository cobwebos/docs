---
title: "导出 Azure Cosmos DB 模拟器证书 | Microsoft Docs"
description: "以不使用 Windows 证书存储的语言和运行时进行开发时，需要导出并管理 SSL 证书。 本文提供分步说明。"
services: cosmos-db
documentationcenter: 
keywords: "Azure Cosmos DB 模拟器"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ef14df93b7870c7bc4be0f644d32cc45d15b7856
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---

# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>导出 Azure Cosmos DB 模拟器证书供 Java、Python 和 Node.js 使用

[**下载模拟器**](https://aka.ms/documentdb-emulator)

为进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境（包括使用 SSL 连接）。 本文演介绍了如何导出 SSL 证书以用于不与 Windows 证书存储集成的语言和运行时，例如使用自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)的 Java、使用[套接字包装](https://docs.python.org/2/library/ssl.html)的 Python 和使用 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) 的 Node.js。 可以在[使用 Azure Cosmos DB 模拟器进行开发和测试](./local-emulator.md)中了解有关模拟器的更多信息。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 旋转证书
> * 导出 SSL 证书
> * 了解了如何在 Java、Python 和 Node.js 中使用证书

## <a name="certification-rotation"></a>证书旋转

Azure Cosmos DB 本地模拟器中的证书在首次运行模拟器时生成。 证书有两个。 一个用于连接到本地模拟器，另一个用于管理模拟器中的机密。 要导出的证书是具有“Azure Cosmos DBEmulatorCertificate”友好名称的连接证书。

在 Windows 任务栏中运行的 Azure Cosmos DB 模拟器中，单击“重置数据”（如下所示），即可重新生成这两个证书。 如果重新生成证书并将它们安装到 Java 证书存储或在其他地方使用，则需要更新证书，否则应用程序将不再连接到本地模拟器。

![Azure Cosmos DB 本地模拟器重置数据](./media/local-emulator/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>如何导出 Azure Cosmos DB SSL 证书

1. 通过运行 certlm.msc 启动 Windows 证书管理器，然后导航到“个人”->“证书”文件夹，打开友好名称为“CosmosDBEmulatorCertificate”的证书。

    ![Azure Cosmos DB 本地模拟器导出步骤 1](./media/local-emulator/database-local-emulator-export-step-1.png)

2. 单击“详细信息”，然后单击“确定”。

    ![Azure Cosmos DB 本地模拟器导出步骤 2](./media/local-emulator/database-local-emulator-export-step-2.png)

3. 单击“复制到文件...”。

    ![Azure Cosmos DB 本地模拟器导出步骤 3](./media/local-emulator/database-local-emulator-export-step-3.png)

4. 单击“下一步”。

    ![Azure Cosmos DB 本地模拟器导出步骤 4](./media/local-emulator/database-local-emulator-export-step-4.png)

5. 单击“否，不导出私钥”，然后单击“下一步”。

    ![Azure Cosmos DB 本地模拟器导出步骤 5](./media/local-emulator/database-local-emulator-export-step-5.png)

6. 单击“Base-64 编码 X.509 (.CER)”，然后单击“下一步”。

    ![Azure Cosmos DB 本地模拟器导出步骤 6](./media/local-emulator/database-local-emulator-export-step-6.png)

7. 为证书指定名称。 在本示例中为“documentdbemulatorcert”，然后单击“下一步”。

    ![Azure Cosmos DB 本地模拟器导出步骤 7](./media/local-emulator/database-local-emulator-export-step-7.png)

8. 单击“完成”。

    ![Azure Cosmos DB 本地模拟器导出步骤 8](./media/local-emulator/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>如何在 Java 中使用证书

运行使用 Java 客户端的 Java 应用程序或 MongoDB 应用程序时，将证书安装到 Java 默认证书存储比传递“-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>”标志更简单。 例如，包含的 [Java 演示应用程序](https://localhost:8081/_explorer/index.html)取决于默认证书存储。

请按照[将证书添加到 Java CA 证书存储](https://docs.microsoft.com/azure/java-add-certificate-ca-store)中的说明将 X.509 证书导入到默认 Java 证书存储。 请记住，需在 ％JAVA_HOME％ 目录中运行 keytool。

安装“CosmosDBEmulatorCertificate”SSL 证书后，应用程序应能连接并使用本地 Azure Cosmos DB 模拟器。 如果仍遇到问题，请按照 [Debugging SSL/TLS Connections](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html)（调试 SSL / TLS 连接）一文操作。 很有可能是该证书未安装到 %JAVA_HOME%/jre/lib/security/cacerts 存储中。 例如，如果安装了多个 Java 版本，应用程序使用的 cacerts 存储可能未更新。

## <a name="how-to-use-the-certificate-in-python"></a>如何在 Python 中使用证书

默认情况下，用于 DocumentDB 的 [Python SDK（版本 2.0.0 或更高版本）](documentdb-sdk-python.md)在连接到本地模拟器时不会尝试使用 SSL 证书。 但如果要使用 SSL 验证，可以按照 [Python 套接字包装器](https://docs.python.org/2/library/ssl.html)一文中的示例操作。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用证书

默认情况下，用于 DocumentDB 的 [Node.js SDK（版本 1.10.1 或更高版本）](documentdb-sdk-node.md)在连接到本地模拟器时不会尝试使用 SSL 证书。 但如果要使用 SSL 验证，可以按照 [Node.js 文档](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)中的示例操作。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 旋转证书
> * 导出 SSL 证书
> * 了解了如何在 Java、Python 和 Node.js 中使用证书

现在可以转到“概念”部分详细了解 Cosmos DB。

> [!div class="nextstepaction"]
> [全局分发](distribute-data-globally.md) 

