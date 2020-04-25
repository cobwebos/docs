---
title: TLS-Azure Database for PostgreSQL-单服务器
description: 有关如何为 Azure Database for PostgreSQL 单服务器配置 TLS 连接的说明和信息。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141745"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL 单服务器中配置 TLS 连接

Azure Database for PostgreSQL 倾向于使用传输层安全性（TLS）（以前称为安全套接字层（SSL））将客户端应用程序连接到 PostgreSQL 服务。 在数据库服务器与客户端应用程序之间强制 TLS 连接可以通过加密服务器与应用程序之间的数据流，帮助防止 "中间人" 攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 TLS 连接。 如果客户端应用程序不支持 TLS 连接，则可以选择禁用要求 TLS。

## <a name="enforcing-tls-connections"></a>强制 TLS 连接

对于通过 Azure 门户和 CLI 预配的所有 Azure Database for PostgreSQL 服务器，默认情况下会启用 TLS 连接的强制。 

同样，在 Azure 门户中的服务器下的 "连接字符串" 设置中预定义的连接字符串包括使用 TLS 连接到数据库服务器所需的公共语言参数。 TLS 参数因连接器而异，例如 "ssl = true" 或 "sslmode = required" 或 "sslmode = required" 和其他变体。

## <a name="configure-enforcement-of-tls"></a>配置 TLS 的强制实施

你可以选择禁用强制 TLS 连接。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”**** 设置，以增强安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”****。 使用切换按钮来启用或禁用“强制实施 SSL 连接”**** 设置。 然后，单击 **“保存”**。

![连接安全-禁用强制执行 TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

可以通过在“概述”**** 页中查看“SSL 强制实施状态”**** 指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI

可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”**** 参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保你的应用程序或框架支持 TLS 连接

默认情况下，在安装期间，某些使用 PostgreSQL 的应用程序框架不会启用 TLS。 如果你的 PostgreSQL 服务器强制执行 TLS 连接，但未将应用程序配置为 TLS，则该应用程序可能无法连接到你的数据库服务器。 请参阅应用程序文档，了解如何启用 TLS 连接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要对 TLS 连接进行证书验证的应用程序

在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 要连接到 Azure Database for PostgreSQL 服务器的证书位于上https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem。 下载证书文件并将其保存到首选位置。

### <a name="connect-using-psql"></a>使用 psql 进行连接

下面的示例演示如何使用 psql 命令行实用工具连接到 PostgreSQL 服务器。 使用`sslmode=verify-full`连接字符串设置强制执行 TLS/SSL 证书验证。 将本地证书文件路径传递给`sslrootcert`参数。

以下命令是 psql 连接字符串的示例：

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="next-steps"></a>后续步骤

在 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)中查看各种应用程序连接选项。
