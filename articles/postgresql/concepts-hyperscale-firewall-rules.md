---
title: 防火墙规则 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 本文介绍了后格雷SQL - 超大规模 （Citus） 的 Azure 数据库的防火墙规则。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975561"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure 数据库中的防火墙规则，用于后格雷SQL - 超大规模（Citus）
用于 PostgreSQL 服务器防火墙的 Azure 数据库阻止对超大规模 （Citus） 协调器节点的所有访问，直到指定哪些计算机具有权限。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。
要配置防火墙，请创建防火墙规则，以指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

**防火墙规则：** 这些规则使客户端能够访问超量程 （Citus） 协调节点，即同一逻辑服务器中的所有数据库。 可以使用 Azure 门户配置服务器级防火墙规则。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
默认情况下，防火墙会阻止对协调器节点的所有数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。
来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 PostgreSQL 数据库，如下图中所示：

![防火墙工作流示例](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>从 Internet 和 Azure 连接

Hyperscale （Citus） 服务器组防火墙控制谁可以连接到组的协调器节点。 防火墙通过查阅可配置的规则列表来确定访问。 每个规则都是允许进入的 IP 地址或地址范围。

当防火墙阻止连接时，它可能会导致应用程序错误。 例如，使用 PostgreSQL JDBC 驱动程序会引发这样的错误：

> java.util.并发.执行例外：java.lang.RuntimeException：org.postgresql.util.PSQLException：FATAL：主机\_"123.45.67.890"没有pg hba.conf条目，用户"citus"，数据库"citus"，SSL

请参阅[创建和管理防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)以了解如何定义规则。

## <a name="troubleshooting-the-database-server-firewall"></a>数据库服务器防火墙故障排除
当访问 Microsoft Azure 数据库的 PostgreSQL - 超大规模 （Citus） 服务不按预期运行时，请考虑以下要点：

* **对允许列表的更改尚未生效：** Hyperscale （Citus） 防火墙配置的更改可能延迟多达五分钟。"

* **用户未获得授权或使用不正确的密码：** 如果用户对服务器没有权限，或者使用的密码不正确，则与服务器的连接将被拒绝。 创建防火墙设置，仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

例如，使用 JDBC 客户端可能会出现以下错误。
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: password authentication failed for user "yourusername"

* **动态 IP 地址：** 如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，则可以尝试以下解决方法之一：

* 询问 Internet 服务提供商 （ISP） 分配给访问超量（Citus） 协调器节点的客户端计算机的 IP 地址范围，然后添加 IP 地址范围作为防火墙规则。

* 改为获取客户端计算机的静态 IP 地址，然后将该静态 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤
有关如何创建服务器级和数据库级防火墙规则的文章，请参阅：
* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-hyperscale-manage-firewall-using-portal.md)
