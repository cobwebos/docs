---
title: 在 Azure Database for PostgreSQL - 单一服务器中配置 SSL 连接
description: 有关如何配置 Azure Database for PostgreSQL - 单一服务器和关联应用程序以正确使用 SSL 连接的说明和信息。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461839"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>在 Azure Database for PostgreSQL - 单一服务器中配置 SSL 连接
Azure Database for PostgreSQL 倾向于使用安全套接字层 (SSL) 将客户端应用程序连接到 PostgreSQL 服务。 强制实施 SSL 连接你的数据库服务器和客户端应用程序之间帮助防止加密服务器与你的应用程序之间的数据流"--拦截"攻击。

默认情况下，PostgreSQL 数据库服务配置为需要 SSL 连接。 您可以禁止要求 SSL，如果客户端应用程序不支持 SSL 连接性。 

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接
对于通过 Azure 门户或 CLI 预配的所有 Azure Database for PostgreSQL 服务器，强制实施 SSL 连接是默认启用的。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="configure-enforcement-of-ssl"></a>配置强制实施 SSL
（可选）可以禁用强制实施 SSL 连接。 Microsoft Azure 建议始终启用“强制实施 SSL 连接”  设置，以增强安全性。

### <a name="using-the-azure-portal"></a>使用 Azure 门户
访问 Azure Database for PostgreSQL 服务器，并单击“连接安全性”  。 使用切换按钮来启用或禁用“强制实施 SSL 连接”  设置。 然后，单击“保存”  。 

![连接安全性 - 禁用强制实施 SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

可以通过在“概述”  页中查看“SSL 强制实施状态”  指示器来确认设置。

### <a name="using-azure-cli"></a>使用 Azure CLI
可以通过在 Azure CLI 中分别使用 `Enabled` 或 `Disabled` 值来启用或禁用“ssl-enforcement”  参数。

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接
将 PostgreSQL 用于其数据库服务的某些应用程序框架不启用 SSL 默认情况下在安装过程。 如果 PostgreSQL 服务器强制实施 SSL 连接，但没有为 SSL 配置应用程序，该应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 SSL 连接。


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要证书验证才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 若要连接到 Azure Database for PostgreSQL 服务器位于证书 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 。 下载证书文件并将其保存到你首选的位置。 

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例演示如何连接到 PostgreSQL 服务器使用 psql 命令行实用程序。 使用`sslmode=verify-full`连接字符串设置，以强制实施 SSL 证书验证。 传递到该本地证书文件路径`sslrootcert`参数。

下面是 psql 连接字符串的示例：
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> 确认的值传递给`sslrootcert`与保存的证书文件路径相匹配。


## <a name="next-steps"></a>后续步骤
查看在各种应用程序连接性选项[Azure database for PostgreSQL 的连接库](concepts-connection-libraries.md)。
