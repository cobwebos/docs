---
title: TLS - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 用于为 PostgreSQL - 超大规模 （Citus） 和相关应用程序配置 Azure 数据库以正确使用 TLS 连接的说明和信息。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422339"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 数据库中为后函数 SQL - 超大规模 （Citus） 配置 TLS
与 Hyperscale （Citus） 协调节点的客户端应用程序连接需要传输层安全 （TLS），以前称为安全套接字层 （SSL）。 通过加密服务器和应用程序之间的数据流，在数据库服务器和客户端应用程序之间强制实施 TLS 连接，有助于防止"中间人"攻击。

## <a name="enforcing-tls-connections"></a>强制 TLS 连接
对于通过 Azure 门户预配的所有 PostgreSQL 服务器 Azure 数据库，默认情况下启用 TLS 连接的强制。 

同样，在 Azure 门户中的服务器下的"连接字符串"设置中预定义的连接字符串包括使用 TLS 连接到数据库服务器的通用语言所需的参数。 TLS 参数因连接器而异，例如"ssl_true"或"sslmode_要求"或"sslmode_需要"和其他变体。

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>确保您的应用程序或框架支持 TLS 连接
某些应用程序框架在安装过程中默认不会启用 TLS，这些框架将 PostgreSQL 用于其数据库服务。 如果您的 PostgreSQL 服务器强制执行 TLS 连接，但应用程序未配置为 TLS，则应用程序可能无法连接到数据库服务器。 请参阅应用程序的文档，了解如何启用 TLS 连接。

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>需要 TLS 连接证书验证的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 连接到 Azure 数据库的 PostgreSQL- 超大规模 （Citus） 的证书https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem位于 。 下载证书文件并将其保存到您的首选位置。

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例演示如何使用 psql 命令行实用程序连接到超大规模 （Citus） 协调器节点。 使用`sslmode=verify-full`连接字符串设置强制执行 TLS 证书验证。 将本地证书文件路径传递给参数`sslrootcert`。

下面是 psql 连接字符串的示例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="next-steps"></a>后续步骤
使用 Azure[数据库中的防火墙规则进一步提高安全性，用于 PostgreSQL- 超大规模 （Citus）。](concepts-hyperscale-firewall-rules.md)
