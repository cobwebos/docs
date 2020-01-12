---
title: SSL-Azure Database for PostgreSQL-单一服务器
description: 有关如何为 Azure Database for PostgreSQL 单服务器配置 SSL 连接的说明和信息。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4f7fd3ea1c83e1bf5183aedf4fe894809884414c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903506"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL 单服务器中配置 SSL 连接
Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以通过加密服务器和应用程序之间的数据流来防止 "中间人" 攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 SSL 连接。 如果客户端应用程序不支持 SSL 连接，则可以选择禁用要求 SSL。 

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接
对于通过 Azure 门户或 CLI 预配的所有 Azure Database for PostgreSQL 服务器，强制实施 SSL 连接是默认启用的。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-ssl"></a>配置强制实施 SSL
（可选）可以禁用强制实施 SSL 连接。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”设置，以增强安全性。

> [!NOTE]
> 目前支持的 TLS 版本 Azure Database for PostgreSQL 为 TLS 1.0、TLS 1.1、TLS 1.2。

### <a name="using-the-azure-portal"></a>使用 Azure 门户
访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”。 使用切换按钮来启用或禁用“强制实施 SSL 连接”设置。 然后，单击“保存”。 

![连接安全性 - 禁用强制实施 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

可以通过在“概述”页中查看“SSL 强制实施状态”指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接
默认情况下，在安装期间，某些使用 PostgreSQL 的应用程序框架不会启用 SSL。 如果你的 PostgreSQL 服务器强制实施 SSL 连接，但没有为 SSL 配置该应用程序，则该应用程序可能无法连接到你的数据库服务器。 请查阅应用程序文档，了解如何启用 SSL 连接。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要证书验证才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 用于连接到 Azure Database for PostgreSQL 服务器的证书位于 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下载证书文件并将其保存到首选位置。 

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例演示如何使用 psql 命令行实用工具连接到 PostgreSQL 服务器。 使用 `sslmode=verify-full` 连接字符串设置强制实施 SSL 证书验证。 将本地证书文件路径传递到 `sslrootcert` 参数。

下面是 psql 连接字符串的示例：
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 确认传递到 `sslrootcert` 的值与保存的证书的文件路径匹配。


## <a name="next-steps"></a>后续步骤
查看[连接库中用于 Azure Database for PostgreSQL 的](concepts-connection-libraries.md)各种应用程序连接选项。
