---
title: 导出 Azure Cosmos DB 模拟器证书
description: 了解如何导出 Azure Cosmos DB 模拟器证书以用于 Java、Python 和 Node.js 应用。 应导出证书，并将其用于不使用 Windows 证书存储的语言和运行时环境。
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java
ms.openlocfilehash: 8f2714c7c49aa5e02747ce726da29a98485b5fbd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90988233"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>导出 Azure Cosmos DB 模拟器证书以用于 Java、Python 和 Node.js 应用

为方便进行开发，Azure Cosmos DB 模拟器提供了一个模拟 Azure Cosmos DB 服务的本地环境。 Azure Cosmos 模拟器仅支持通过 TLS 连接进行安全通信。

首次运行模拟器时，将生成 Azure Cosmos DB 本地模拟器中的证书。 有两个证书。 其中一个用于连接到本地模拟器，另一个用于管理模拟器中模拟器数据的默认加密。 要导出的证书是友好名称为“DocumentDBEmulatorCertificate”的连接证书。

当使用仿真程序以不同的语言（如 Java、Python 或 Node.js）来开发应用程序时，需要导出模拟器证书，并将其导入所需的证书存储中。

当应用程序在 Windows OS 主机上运行时，.NET 语言和运行时使用 Windows 证书存储来安全地连接到 Azure Cosmos DB 本地模拟器。 其他语言有自己管理和使用证书方法。 例如，Java 使用自己的 [证书存储区](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)，Python 使用 [套接字包装](https://docs.python.org/2/library/ssl.html)器，Node.js 使用 [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)。

本文演示如何导出 TLS/SSL 证书以用于不与 Windows 证书存储集成的不同语言和运行时环境。 可以在[使用 Azure Cosmos DB 模拟器进行开发和测试](./local-emulator.md)中了解有关模拟器的更多信息。

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>导出 Azure Cosmos DB 的 TLS/SSL 证书

需要导出模拟器证书，才能成功使用不与 Windows 证书存储集成的语言和运行时环境中的模拟器终结点。 您可以使用 Windows 证书管理器导出证书。 使用以下分步说明，将 "DocumentDBEmulatorCertificate" 证书导出为64编码的 x.509 ( .cer) 文件：

1. 通过运行 certlm.msc 启动 Windows 证书管理器并导航到“个人”->“证书”文件夹，打开友好名称为“DocumentDbEmulatorCertificate”**** 的证书。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 1":::

1. 单击“详细信息”****，并单击“确定”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 2":::

1. 单击“复制到文件...” ****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 3":::

1. 单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 4":::

1. 单击“否，不导出私钥”****，并单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 5":::

1. 单击“Base-64 编码 X.509 (.CER)”****，并单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 6":::

1. 为证书指定名称。 在本示例中为“documentdbemulatorcert”****。单击“下一步”****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 7":::

1. 单击“完成” ****。

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Azure Cosmos DB 本地模拟器导出步骤 8":::

## <a name="use-the-certificate-with-java-apps"></a>将证书用于 Java 应用

在运行使用基于 Java 的客户端的 Java 应用程序或 MongoDB 应用程序时，将证书安装到 Java 默认证书存储区比传递标志更容易 `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` 。 例如， () 包含的 Java 演示应用程序 `https://localhost:8081/_explorer/index.html` 依赖于默认证书存储。

按照将 [证书添加到 Java 证书存储](https://docs.microsoft.com/azure/java-add-certificate-ca-store) 中的说明将 x.509 证书导入到默认 java 证书存储。 请注意，在运行 keytool 时，你将在 *% JAVA_HOME%* 目录中工作。 将证书导入到证书存储区后，SQL 和 Azure Cosmos DB 的 API for MongoDB 的客户端将能够连接到 Azure Cosmos 模拟器。

或者，你可以运行以下 bash 脚本来导入证书：

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

安装 "CosmosDBEmulatorCertificate" TLS/SSL 证书后，应用程序应该能够连接并使用本地 Azure Cosmos DB 模拟器。 如果有任何问题，可以遵循 [调试 SSL/TLS 连接](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) 一文。 在大多数情况下，证书可能不会安装到 *% JAVA_HOME%/jre/lib/security/cacerts* 存储中。 例如，如果有多个已安装的 Java 版本，应用程序可能使用不同于所更新的 cacerts 存储。

## <a name="use-the-certificate-with-python-apps"></a>将证书用于 Python 应用

从 Python 应用连接到模拟器时，TLS 验证处于禁用状态。 默认情况下，在连接到本地模拟器时，适用于 SQL API 的 [PYTHON SDK (版本2.0.0 或更高版本的) ](sql-api-sdk-python.md) 将不会尝试使用 TLS/SSL 证书。 但是，如果想要使用 TLS 验证，可以按照 [Python 套接字包装](https://docs.python.org/2/library/ssl.html) 器文档中的示例进行操作。

## <a name="how-to-use-the-certificate-in-nodejs"></a>如何在 Node.js 中使用证书

从 Node.js Sdk 连接到模拟器时，TLS 验证处于禁用状态。 默认情况下，在连接到本地模拟器时，SQL API 的 [Node.js SDK (版本1.10.1 或更高版本的) ](sql-api-sdk-node.md) 将不会尝试使用 TLS/SSL 证书。 不过，如果要使用 TLS 验证，则可以按照 [Node.js 文档](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback)中的示例进行操作。

## <a name="rotate-emulator-certificates"></a>旋转模拟器证书

可以通过在 Windows 任务栏中运行的 Azure Cosmos DB 模拟器中选择 " **重置数据** "，强制重新生成模拟器证书。 请注意，此操作还将擦除模拟器在本地存储的所有数据。

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Azure Cosmos DB 本地模拟器重置数据":::

如果已将证书安装到 Java 证书存储中或在其他位置使用，则需要使用当前证书重新导入。 在更新证书之前，应用程序无法连接到本地模拟器。

## <a name="next-steps"></a>后续步骤

* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
* [调试模拟器的问题](troubleshoot-local-emulator.md)

