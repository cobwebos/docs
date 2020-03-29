---
title: SSL - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 用于为 PostgreSQL - 超大规模 （Citus） 和相关应用程序配置 Azure 数据库以正确使用 SSL 连接的说明和信息。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973979"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>在 Azure 数据库中配置 SSL，用于后缩放 - 超大规模（Citus）
客户端应用程序连接到超大规模 （Citus） 协调节点需要安全套接字层 （SSL）。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

## <a name="enforcing-ssl-connections"></a>强制实施 SSL 连接
对于通过 Azure 门户预配的所有 PostgreSQL 服务器 Azure 数据库，默认情况下启用 SSL 连接的强制。 

同样，在 Azure 门户中，用户服务器的“连接字符串”设置中预定义了连接字符串，该字符串中包含以通用语言使用 SSL 连接到数据库服务器所需的参数。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>确保应用程序或框架支持 SSL 连接
某些使用 PostgreSQL 作为其数据库服务的应用程序框架在安装期间默认不启用 SSL。 如果 PostgreSQL 服务器强制实施 SSL 连接，但应用程序未配置 SSL，则应用程序可能无法连接到数据库服务器。 请查阅应用程序文档，了解如何启用 SSL 连接。

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>需要证书验证才可启用 SSL 连接性的应用程序
在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件 (.cer) 生成的本地证书文件才能实现安全连接。 连接到 Azure 数据库的 PostgreSQL- 超大规模 （Citus） 的证书https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem位于 。 下载证书文件并将其保存到您的首选位置。

### <a name="connect-using-psql"></a>使用 psql 进行连接
下面的示例演示如何使用 psql 命令行实用程序连接到超大规模 （Citus） 协调器节点。 使用`sslmode=verify-full`连接字符串设置强制实施 SSL 证书验证。 将本地证书文件路径传递给参数`sslrootcert`。

下面是 psql 连接字符串的示例：
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> 确认传递给 `sslrootcert` 的值与你保存的证书的文件路径匹配。

## <a name="next-steps"></a>后续步骤
使用 Azure[数据库中的防火墙规则进一步提高安全性，用于 PostgreSQL- 超大规模 （Citus）。](concepts-hyperscale-firewall-rules.md)
