---
title: SSL/TLS 连接-Azure Database for MariaDB
description: 有关配置 Azure Database for MariaDB 和关联应用程序以正确使用 SSL 连接的信息
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 4111b0b01690097535412205b60619172e2c100a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416649"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>SSL/TLS 连接 Azure Database for MariaDB
Azure Database for MariaDB 支持使用安全套接字层 (SSL) 将数据库服务器连接到客户端应用程序。 通过在数据库服务器与客户端应用程序之间强制实施 SSL 连接，可以加密服务器与应用程序之间的数据流，有助于防止“中间人”攻击。

## <a name="default-settings"></a>默认设置
默认情况下，应将数据库服务配置为需要 SSL 连接才可连接到 MariaDB。  建议尽量不要禁用 SSL 选项。

通过 Azure 门户和 CLI 预配新的 Azure Database for MariaDB 服务器时，默认情况下会强制实施 SSL 连接。

Azure 门户中显示了各种编程语言的连接字符串。 这些连接字符串包含连接到数据库所需的 SSL 参数。 在 Azure 门户中，选择服务器。 在“设置”标题下，选择“连接字符串”   。 SSL 参数因连接器而异，例如“ssl=true”、“sslmode=require”或“sslmode=required”，以及其他变体。

若要了解如何在开发应用程序期间启用或禁用 SSL 连接，请参阅[如何配置 SSL](howto-configure-ssl.md)。

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的 TLS 强制

Azure Database for MariaDB 支持使用传输层安全性（TLS）连接到数据库服务器的客户端加密。 TLS 是一种行业标准协议，可确保数据库服务器和客户端应用程序之间的安全网络连接，使你能够遵守符合性要求。

### <a name="tls-settings"></a>TLS 设置

Azure Database for MariaDB 提供了为客户端连接强制 TLS 版本的功能。 若要强制使用 TLS 版本，请使用 "**最小 tls 版本**" 选项设置。 此选项设置允许以下值：

|  最小 TLS 设置             | 支持的客户端 TLS 版本                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled （默认值） | 无需 TLS                      |
| TLS1_0                           | TLS 1.0，TLS 1.1，TLS 1.2 及更高版本         |
| TLS1_1                           | TLS 1.1，TLS 1.2 及更高版本              |
| TLS1_2                           | TLS 版本1.2 及更高版本                  |


例如，将 "最小 TLS 设置版本" 的值设置为 "TLS 1.0" 意味着你的服务器将允许使用 TLS 1.0、1.1 和 1.2 + 的客户端连接。 或者，将此选项设置为1.2 意味着仅允许使用 TLS 1.2 + 的客户端进行连接，并且将拒绝 TLS 1.0 和 TLS 1.1 的所有连接。

> [!Note] 
> 默认情况下，将为所有新服务器禁用 Azure Database for MariaDB 默认值。 
>
> 目前 Azure Database for MariaDB 支持的 TLS 版本为 TLS 1.0、1.1 和1.2。 强制执行到特定的最低 TLS 版本后，无法将其更改为 "已禁用"。

若要了解如何设置 Azure Database for MariaDB 的 TLS 设置，请参阅[如何配置 tls 设置](howto-tls-configurations.md)。

## <a name="next-steps"></a>后续步骤
- 了解有关[服务器防火墙规则](concepts-firewall-rules.md)的详细信息
- 了解如何[配置 SSL](howto-configure-ssl.md)
- 了解如何[配置 TLS](howto-tls-configurations.md)
