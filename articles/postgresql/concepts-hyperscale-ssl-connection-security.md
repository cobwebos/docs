---
title: 超大规模（Citus）-Azure Database for PostgreSQL
description: 用于配置 Azure Database for PostgreSQL-超大规模（Citus）和关联应用程序以正确使用 TLS 连接的说明和信息。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580568"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure Database for PostgreSQL-超大规模（Citus）中配置 TLS
与超大规模（Citus）协调器节点的客户端应用程序连接需要传输层安全性（TLS），以前称为安全套接字层（SSL）。 在数据库服务器与客户端应用程序之间强制 TLS 连接可以通过加密服务器与应用程序之间的数据流，帮助防止 "中间人" 攻击。

## <a name="enforcing-tls-connections"></a>强制 TLS 连接
对于通过 Azure 门户预配的所有 Azure Database for PostgreSQL 服务器，默认情况下会启用 TLS 连接的强制。 

同样，在 Azure 门户中的服务器下的 "连接字符串" 设置中预定义的连接字符串包括使用 TLS 连接到数据库服务器所需的公共语言参数。 TLS 参数因连接器而异，例如 "ssl = true" 或 "sslmode = required" 或 "sslmode = required" 和其他变体。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保你的应用程序或框架支持 TLS 连接
默认情况下，在安装期间，某些使用 PostgreSQL 的应用程序框架不会启用 TLS。 如果你的 PostgreSQL 服务器强制执行 TLS 连接，但未将应用程序配置为 TLS，则该应用程序可能无法连接到你的数据库服务器。 请参阅应用程序文档，了解如何启用 TLS 连接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要对 TLS 连接进行证书验证的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 要连接到 Azure Database for PostgreSQL 超大规模（Citus）的证书位于上https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem。 下载证书文件并将其保存到首选位置。

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例演示如何使用 psql 命令行实用工具连接到超大规模（Citus）协调器节点。 使用`sslmode=verify-full`连接字符串设置强制执行 TLS 证书验证。 将本地证书文件路径传递给`sslrootcert`参数。

下面是 psql 连接字符串的示例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="next-steps"></a>后续步骤
通过[Azure Database for PostgreSQL-超大规模（Citus）中的防火墙规则](concepts-hyperscale-firewall-rules.md)进一步提高安全性。
