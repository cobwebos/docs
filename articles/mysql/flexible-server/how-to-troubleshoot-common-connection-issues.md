---
title: 排查连接问题-Azure Database for MySQL-灵活服务器
description: 了解如何对 Azure Database for MySQL 灵活的服务器的连接问题进行故障排除。
keywords: mysql 连接，连接字符串，连接问题，永久错误，连接错误
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: b328da01cd3b2ecb3eb5f183296eab808de27399
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933384"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>排查 Azure Database for MySQL-灵活服务器的连接问题

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

连接问题可能由多种因素造成，包括：

* 防火墙设置
* 连接超时
* 不正确的登录信息
* 某些 Azure Database for MySQL 灵活的服务器资源达到了最大限制

在本文中，我们将讨论如何排查一些常见错误以及解决这些错误的步骤。

## <a name="troubleshoot-common-errors"></a>解决常见问题

如果应用程序持久无法连接到 Azure Database for MySQL 灵活的服务器，它通常表示存在以下问题之一：

* 使用 TLS/SSL 的加密连接：灵活服务器仅支持使用传输层安全性 (TLS 1.2) 的加密连接，并且 **将拒绝 tls 1.0 和 tls 1.1 的所有传入连接**。 不能禁用或更改 TLS 版本。 详细了解如何 [使用传输层安全性 (TLS 1.2) 在 Azure Database for MySQL 灵活的服务器中使用传输层安全性](./how-to-connect-tls-ssl.md)。
- 专用访问中的灵活服务器 * (VNet 集成) *：确保从与灵活服务器相同的虚拟网络中进行连接。 请参阅 [Azure Database for MySQL 灵活服务器中的虚拟网络]<!--(./concepts-networking-virtual-network.md)-->
- 具有 *公共访问权限的灵活服务器 (允许的 IP 地址) *，请确保将防火墙配置为允许来自客户端的连接。 请参阅 [使用 Azure 门户创建和管理灵活的服务器防火墙规则](./how-to-manage-firewall-portal.md)。
* 客户端防火墙配置：客户端的防火墙必须允许连接到数据库服务器。 不能访问的服务器的 IP 地址和端口以及一些防火墙中的应用程序名称（如 MySQL）必须被允许。
* 用户错误：您可能键入了错误的连接参数，例如连接字符串中的服务器名称。

### <a name="resolve-connectivity-issues"></a>解决连接问题

* 有关加密连接的详细信息，请参阅 [在 Azure Database for MySQL 灵活的服务器中使用传输层安全性 (TLS 1.2) > 的加密连接](./how-to-connect-tls-ssl.md) 。
* 如果你使用的是 **公共访问 (允许的 ip 地址) **，请设置 [防火墙规则](./how-to-manage-firewall-portal.md) 以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 这样会使服务器向所有 IP 地址开放。 如果这样可以解决连接性问题，请删除此规则，再针对适当限制的 IP 地址或地址范围创建防火墙规则。
* 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 3306。
* 验证连接字符串和其他连接设置。 请参阅适用于适用于你的服务器的 " **连接字符串** " 页中的预定义连接字符串，这些字符串位于适用于公共语言的 Azure 门户中。

## <a name="next-steps"></a>后续步骤
- [使用 MySQL 工作台在 Azure Database for MySQL 灵活的服务器中连接和查询数据](./connect-workbench.md)。
- [使用 PHP 在 Azure Database for MySQL 灵活的服务器中连接和查询数据](./connect-php.md)。
- [使用 Python 连接和查询 Azure Database for MySQL 灵活服务器中的数据](./connect-python.md)。
