---
title: "Azure Database for PostgreSQL 服务器防火墙规则 | Microsoft Docs"
description: "描述 Azure Database for PostgreSQL 服务器的防火墙规则。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 464a8725847271bddb19dcf71add3f5184e4771f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure Database for PostgreSQL 服务器防火墙规则
在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。
若要配置你的防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

防火墙规则：这些规则允许客户端访问整个 Azure Database for PostgreSQL 服务器，即同一逻辑服务器内的所有数据库。 可以通过使用 Azure 门户或 Azure CLI 命令配置服务器级防火墙规则。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
默认情况下，防火墙将阻止对 Azure Database for PostgreSQL 服务器的所有数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。
来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，然后才能访问 PostgreSQL 数据库，如下图中所示：

![防火墙工作流示例](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>从 Internet 连接
服务器级防火墙规则适用于 Azure Database for PostgreSQL 服务器上的所有数据库。 如果该请求的 IP 地址位于服务器级防火墙规则中指定的某个范围内，则授予连接权限。
如果该请求的 IP 地址不位于任何数据库级或服务器级防火墙规则中指定的范围内，则连接请求失败。
例如，如果应用程序与 PostgreSQL 的 JDBC 驱动程序连接，则在防火墙阻止连接时尝试进行连接可能会遇到此错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="programmatically-managing-firewall-rules"></a>以编程方式管理防火墙规则
除了 Azure 门户外，还可使用 Azure CLI 通过编程方式管理防火墙规则。
另请参阅[使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>数据库防火墙故障排除
在对 Microsoft Azure Database for PostgreSQL 服务器服务的访问与你的期望不符时，请考虑以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for PostgreSQL 服务器防火墙配置所做的更改可能需要多达 5 分钟的延迟才可生效。

* 登录名未授权或使用了错误的密码：如果某个登录名对 Azure Database for PostgreSQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for PostgreSQL 服务器的连接将被拒绝。 创建防火墙设置仅向客户端提供尝试连接到你的服务器的机会；每个客户端必须提供必需的安全凭据。

例如，使用 JDBC 客户端可能会出现以下错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **动态 IP 地址：**如果你的 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

* 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for PostgreSQL 服务器的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。

* 改为获取你的客户端计算机的静态 IP 地址，然后将该 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤
有关如何创建服务器级和数据库级防火墙规则的文章，请参阅：
* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-cli.md)
