---
title: 导出 Azure Cosmos DB 模拟器证书
description: 通过那些不使用 Windows 证书存储的语言和运行时进行开发时，需要导出和管理 TLS/SSL 证书。 此文提供了分步说明。
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: e1321c0d5b1f83ffcfd3f46384dfb3af792c9b8b
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373090"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>使用 Java、Python 和 Node.js 导出要使用的 Azure Cosmos DB 模拟器证书

[**下载模拟器**](https://aka.ms/cosmosdb-emulator)

Azure Cosmos DB 模拟器提供了一个模拟用于开发的 Azure Cosmos DB 服务的本地环境（包括使用 TLS 连接）。 本文演示了如何导出 TLS/SSL 证书以将其用于未与 Windows 证书存储集成的语言和运行时，例如使用自己的[证书存储](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)的 Java、使用[套接字包装器](https://docs.python.org/2/library/ssl.html)的 Python 和使用 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback) 的 Node.js。 可以在[使用 Azure Cosmos DB 模拟器进行开发和测试](./local-emulator.md)中了解有关模拟器的更多信息。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 轮换证书
> * 导出 TLS/SSL 证书
> * 了解如何在 Java、Python 和 Node.js 中使用证书

## <a name="certification-rotation"></a>证书轮转

首次运行模拟器时，在 Azure Cosmos DB 本地模拟器中生成证书。 有两个证书。 一个用于连接到本地模拟器，另一个用于管理模拟器中的机密。 要导出的证书是友好名称为“DocumentDBEmulatorCertificate”的连接证书。

可以通过单击在 Windows 任务栏中运行的 Azure Cosmos DB 模拟器中的“重置数据”**** 重新生成这两个证书，如下所示。 如果重新生成证书并已将它们安装到 Java 证书存储或已在其他位置使用它们，则需要更新这些证书，否则应用程序将无法再连接到本地模拟器。

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 本地模拟器重置数据":::

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>如何导出 Azure Cosmos DB TLS/SSL 证书

1. 通过运行 certlm.msc 启动 Windows 证书管理器并导航到“个人”->“证书”文件夹，打开友好名称为“DocumentDbEmulatorCertificate”**** 的证书。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 1":::

2. 单击“详细信息”****，并单击“确定”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 2":::

3. 单击“复制到文件...” ****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 3":::

4. 单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 4":::

5. 单击“否，不导出私钥”****，并单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 5":::

6. 单击“Base-64 编码 X.509 (.CER)”****，并单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 6":::

7. 为证书指定名称。 在本示例中为“documentdbemulatorcert”****。单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 7":::

8. 单击“完成” ****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 8":::

## <a name="how-to-use-the-certificate-in-java"></a>如何在 Java 中使用证书

运行使用 Java 客户端的 Java 应用程序或 MongoDB 应用程序时，将证书安装到 Java 默认证书存储比传递 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 标志更简单。 例如，包含的 Java 演示应用程序 (`https://localhost:8081/_explorer/index.html`) 依赖于默认证书存储。

请按照[将证书添加到 Java CA 证书存储](https://docs.microsoft.com/azure/java-add-certificate-ca-store)中的说明将 X.509 证书导入到默认 Java 证书存储。 请注意，运行 keytool 时会在 %JAVA_HOME% 目录中执行操作。

或者，你可以创建并执行自动执行此操作的 "bash" 脚本：
```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

安装“CosmosDBEmulatorCertificate”TLS/SSL 证书后，应用程序应该能够连接并使用本地 Azure Cosmos DB 模拟器。 如果仍然遇到问题，可能需要按照 [调试 SSL/TLS 连接](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 一文进行操作。 很有可能是该证书未安装到 %JAVA_HOME%/jre/lib/security/cacerts 存储中。 例如，如果已安装多个 Java 版本，则应用程序使用的 cacerts 存储可能与你更新的存储不同。

## <a name="how-to-use-the-certificate-in-python"></a>如何在 Python 中使用证书

默认情况下，用于 SQL API 的 [Python SDK（2.0.0 或更高版本）](sql-api-sdk-python.md)在连接到本地模拟器时不会尝试使用 TLS/SSL 证书。 但是，如果要使用 TLS 验证，则可以按照 [Python 套接字包装器](https://docs.python.org/2/library/ssl.html)文档中的示例进行操作。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用证书

默认情况下，用于 SQL API 的 [Node.js SDK（1.10.1 或更高版本）](sql-api-sdk-node.md)在连接到本地模拟器时不会尝试使用 TLS/SSL 证书。 但是，如果要使用 TLS 验证，则可以按照 [Node.js 文档](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)中的示例进行操作。

## <a name="next-steps"></a>后续步骤

本教程已完成以下内容：

> [!div class="checklist"]
> * 轮换证书
> * 导出 TLS/SSL 证书
> * 了解如何在 Java、Python 和 Node.js 中使用证书

现在可以转到“概念”部分详细了解 Azure Cosmos DB。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB 中的可优化数据一致性级别](../cosmos-db/consistency-levels.md)
