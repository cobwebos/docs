---
title: SSL/TLS - Azure Database for PostgreSQL - 单一服务器
description: 有关如何为 Azure Database for PostgreSQL（单一服务器）配置 TLS 连接的说明和信息。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 872daf3a208452e8b7ec27b2326e394b416a1c5f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902039"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL（单一服务器）中配置 TLS 连接

Azure Database for PostgreSQL 倾向于使用传输层安全性 (TLS)（以前成为安全套接字层 (SSL)）将客户端应用程序连接到 PostgreSQL 服务。 通过在数据库服务器与客户端应用程序之间强制实施 TLS 连接，可以加密服务器与应用程序之间的数据流，这有助于防止“中间人”攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 TLS 连接。 如果客户端应用程序不支持 TLS 连接，则可以选择禁用 TLS。

## <a name="enforcing-tls-connections"></a>强制实施 TLS 连接

对于通过 Azure 门户或 CLI 预配的所有 Azure Database for PostgreSQL 服务器，默认会强制实施 TLS 连接。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 TLS 连接到数据库服务器所需的参数。 TLS 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-tls"></a>配置强制实施 TLS

（可选）可以禁用强制实施 TLS 连接。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”**** 设置，以增强安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 门户

访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置。 然后单击“保存” 。

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="连接安全性 - 禁用强制实施 TLS/SSL":::

可以通过在“概述”页中查看“SSL 强制实施状态”指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI

可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保应用程序或框架支持 TLS 连接

某些使用 PostgreSQL 作为其数据库服务的应用程序框架在安装期间默认不启用 TLS。 如果 PostgreSQL 服务器强制实施 TLS 连接，但应用程序未配置 TLS，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 TLS 连接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要证书验证才可启用 TLS 连接性的应用程序

在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能实现安全连接。 用于连接到 Azure Database for PostgreSQL 的证书位于 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下载证书文件并将其保存到首选位置。 

请参阅以下链接，了解主权云中服务器的证书： [Azure 政府](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)版、 [azure 中国](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)版和 [azure 德国](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)版。

### <a name="connect-using-psql"></a>使用 psql 进行连接

以下示例演示如何使用 psql 命令行实用程序连接到 PostgreSQL 服务器。 使用 `sslmode=verify-full` 连接字符串设置强制实施 TLS/SSL 证书验证。 将本地证书文件路径传递给 `sslrootcert` 参数。

以下命令是 psql 连接字符串的示例：

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 单一服务器中的 TLS 强制

对于使用传输层安全性 (TLS) 连接到数据库服务器的客户端，Azure Database for PostgreSQL - 单一服务器支持加密。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现安全的网络连接，使你能够满足合规性要求。

### <a name="tls-settings"></a>TLS 设置

Azure Database for PostgreSQL 单一服务器提供了为客户端连接强制使用 TLS 版本的功能。 若要强制使用 TLS 版本，请使用“最低 TLS 版本”选项设置。 此选项设置允许以下值：

|  最低 TLS 设置             | 支持的客户端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled（默认值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2 及更高版本 |
| TLS1_1                           | TLS 1.1、TLS 1.2 及更高版本          |
| TLS1_2                           | TLS 版本 1.2 及更高版本           |


例如，将此最低 TLS 设置版本设置为 TLS 1.0 意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端进行连接。 也可将此选项设置为 1.2，这意味着仅允许那些使用 TLS 1.2+ 的客户端进行连接，将拒绝使用 TLS 1.0 和 TLS 1.1 进行的所有连接。

> [!Note] 
> 默认情况下，Azure Database for PostgreSQL 不强制执行最低 TLS 版本要求（设置为 `TLSEnforcementDisabled`）。
>
> 一旦强制实施最低 TLS 版本要求后，以后将无法禁用最低版本强制实施。

若要了解如何为 Azure Database for PostgreSQL 单一服务器指定 TLS 设置，请参阅[如何配置 TLS 设置](howto-tls-configurations.md)。

## <a name="next-steps"></a>后续步骤

在 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)中查看各种应用程序连接选项。

- 了解如何[配置 TLS](howto-tls-configurations.md)