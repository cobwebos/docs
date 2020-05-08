---
title: 防火墙规则-超大规模（Citus）-Azure Database for PostgreSQL
description: 本文介绍 Azure Database for PostgreSQL-超大规模（Citus）的防火墙规则。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 127840738d8fb8db00a7efc0b0d8fe393138488d
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580922"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-超大规模（Citus）中的防火墙规则
在指定哪些计算机具有权限之前，Azure Database for PostgreSQL 服务器防火墙阻止对超大规模（Citus）协调器节点的所有访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。
要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

**防火墙规则：** 这些规则允许客户端访问超大规模（Citus）协调器节点，即同一逻辑服务器内的所有数据库。 服务器级防火墙规则可以使用 Azure 门户进行配置。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
默认情况下，防火墙会阻止对协调器节点的所有数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。
来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 PostgreSQL 数据库，如下图中所示：

![防火墙工作流示例](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>从 Internet 和 Azure 连接

超大规模（Citus）服务器组防火墙控制哪些用户可以连接到组的协调器节点。 防火墙通过咨询可配置的规则列表来确定访问权限。 每个规则都是中允许的 IP 地址或地址范围。

当防火墙阻止连接时，可能会导致应用程序错误。 例如，使用 PostgreSQL JDBC 驱动程序会引发错误，如下所示：

> util： Java.util.concurrent.executionexception： RuntimeException：：：致命：\_主机 "util"，用户 "org.postgresql.util.psqlexception"，数据库 "123.45.67.890"，SSL 的 "postgresql" 条目。

有关如何定义规则的详细说明，请参阅[创建和管理防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)。

## <a name="troubleshooting-the-database-server-firewall"></a>数据库服务器防火墙故障排除
当对 PostgreSQL-超大规模（Citus）服务的 Microsoft Azure 数据库的访问无法按预期方式工作时，请考虑以下要点：

* 对**允许列表的更改尚未生效：** 对于超大规模（Citus）防火墙配置的更改生效，可能需要长达五分钟的延迟。

* **用户未经授权或使用了错误的密码：** 如果用户对服务器没有权限或者使用的密码不正确，则会拒绝与服务器的连接。 创建防火墙设置，仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

例如，使用 JDBC 客户端可能会出现以下错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **动态 IP 地址：** 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

* 向 Internet 服务提供商（ISP）询问分配给客户端计算机（访问超大规模（Citus）协调器节点）的 IP 地址范围，然后将该 IP 地址范围添加为防火墙规则。

* 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤
有关如何创建服务器级和数据库级防火墙规则的文章，请参阅：
* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)
