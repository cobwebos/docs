---
title: SSL/TLS 连接 - Azure Database for MariaDB
description: 有关配置 Azure Database for MariaDB 和关联应用程序以正确使用 SSL 连接的信息
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: a108459985f235f0280354ef7b4fa0cb181f5dda
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054239"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的 SSL/TLS 连接
Azure Database for MariaDB 支持使用安全套接字层 (SSL) 将数据库服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

> [!IMPORTANT] 
> SSL 根证书设置为在10月26日开始过期，2020 (10/26/2020) 。 请更新您的应用程序以使用 [新证书](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)。 若要了解详细信息，请参阅 [计划的证书更新](concepts-certificate-rotation.md)

## <a name="default-settings"></a>默认设置
默认情况下，应将数据库服务配置为需要 SSL 连接才可连接到 MariaDB。  建议尽量不要禁用 SSL 选项。

通过 Azure 门户和 CLI 预配新的 Azure Database for MariaDB 服务器时，默认情况下会强制实施 SSL 连接。

在某些情况下，应用程序需要具备从受信任的证书颁发机构 (CA) 证书文件生成的本地证书文件才能实现安全连接。 目前，客户只能使用预定义的证书连接到位于 https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem 的 Azure Database for MariaDB 服务器。 

同样，以下链接指向主权云中的服务器证书： [Azure 政府](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)版、 [azure 中国](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)版和 [azure 德国](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt)版。

Azure 门户中显示了各种编程语言的连接字符串。 这些连接字符串包含连接到数据库所需的 SSL 参数。 在 Azure 门户中，选择服务器。 在“设置”标题下，选择“连接字符串” 。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

若要了解如何在开发应用程序期间启用或禁用 SSL 连接，请参阅[如何配置 SSL](howto-configure-ssl.md)。

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的 TLS 强制执行

对于使用传输层安全性 (TLS) 连接到数据库服务器的客户端，Azure Database for MariaDB 支持加密。 TLS 是一种行业标准协议，可确保在数据库服务器与客户端应用程序之间实现安全的网络连接，使你能够满足合规性要求。

### <a name="tls-settings"></a>TLS 设置

Azure Database for MariaDB 提供了为客户端连接强制使用 TLS 版本的功能。 若要强制执行 TLS 版本，请使用“最低 TLS 版本”选项设置。 此选项设置允许以下值：

|  最低 TLS 设置             | 支持的客户端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled（默认值） | 不需要 TLS                      |
| TLS1_0                           | TLS 1.0、TLS 1.1、TLS 1.2    及更高版本         |
| TLS1_1                           | TLS 1.1、TLS 1.2        及更高版本              |
| TLS1_2                           | TLS 版本 1.2     及更高版本                  |


例如，将此最低 TLS 设置版本的值设置为 TLS 1.0 意味着服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端进行连接。 或者，将此选项设置为 1.2 意味着仅允许使用 TLS 1.2+ 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

> [!Note] 
> 默认情况下，Azure Database for MariaDB 不会强制执行最低 TLS 版本要求（`TLSEnforcementDisabled` 设置）。
>
> 一旦强制实施最低 TLS 版本要求后，以后将无法禁用最低版本强制实施。

若要了解如何为 Azure Database for MariaDB 设置 TLS 设置，请参阅[如何配置 TLS 设置](howto-tls-configurations.md)。

## <a name="next-steps"></a>后续步骤
- 了解有关[服务器防火墙规则](concepts-firewall-rules.md)的详细信息
- 了解如何[配置 SSL](howto-configure-ssl.md)
- 了解如何[配置 TLS](howto-tls-configurations.md)
