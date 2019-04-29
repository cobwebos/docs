---
title: 解决 Azure Databases for MariaDB 的连接问题 | Microsoft Docs
description: 了解如何解决 Azure Databases for MariaDB 的连接问题。
keywords: mariadb 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: f5c5a980a99bac3bf02eaaf9c072861ca753d449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038478"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>解决 Azure Databases for MariaDB 的连接问题

连接问题可能由多种因素造成，包括：

* 防火墙设置
* 连接超时
* 不正确的登录信息
* 某些 Azure Database for MariaDB 资源达到了最大限制
* 服务的基础结构出现问题
* 正在服务中执行维护
* 通过缩放 vCore 数目或移到不同的服务层级更改了服务器的计算资源分配

通常，Azure Database for MariaDB 的连接问题可按如下方式分类：

* 暂时性错误（短暂或间歇性）
* 持久或非暂时性错误（定期重复发生的错误）

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

在执行维护、系统遇到硬件或软件错误，或更改服务器的 vCore 数目或服务层级时，会发生暂时性错误。 Azure Database for MariaDB 服务具有内置的高可用性，可以自动缓解此类问题。 但是，应用程序会短暂地与服务器断开连接，通常不会超过 60 秒。 某些事件偶尔可能需要更长的时间才能缓解，例如，当某个大型事务导致恢复长时间运行时。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 查看 [Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)以了解应用程序报告错误时出现的任何已知中断。
2. 连接到云服务的应用程序（例如 Azure Database for MariaDB）应预料到会发生暂时性错误，并实施重试逻辑来处理这些错误，而不是将它们作为应用程序错误展现给用户。 查看[处理 Azure Database for MariaDB 的暂时性连接错误](concepts-connectivity.md)，了解有关处理暂时性错误的最佳做法和设计准则。
3. 由于服务器即将达到其资源限制，错误可能看起来像是暂时性连接问题。 请参阅 [Azure Database for MariaDB 中的限制](concepts-limits.md)。
4. 如果连接问题继续存在，或者应用程序发生错误的持续时间超过 60 秒或在特定的一天中看到错误多次发生，请通过在 [Azure 支持](https://azure.microsoft.com/support/options)网站上选择“**获取支持**”提出 Azure 支持请求。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误

如果应用程序一直无法连接到 Azure Database for MariaDB，通常表示下列其中一项出现了问题：

* 防火墙配置：Azure Database for MariaDB 服务器或客户端防火墙正在阻止连接。
* 客户端上的网络重新配置：已添加新的 IP 地址或代理服务器。
* 用户错误：例如，你可能有误连接参数，如服务器名称中的连接字符串或缺少 *\@servername*中的用户名称后缀。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤

1. 设置[防火墙规则](howto-manage-firewall-portal.md)以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 这会在所有 IP 地址上打开服务器。 如果这样可以解决连接性问题，请删除此规则，再针对适当限制的 IP 地址或地址范围创建防火墙规则。
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 3306。
3. 验证连接字符串和其他连接设置。 查看[如何将应用程序连接到 Azure Database for MariaDB](howto-connection-string.md)。
4. 在仪表板中检查服务运行状况。 如果你认为发生了区域性服务中断，请参阅[有关使用 Azure Database for MariaDB 确保业务连续性的概述](concepts-business-continuity.md)了解恢复到新区域所要执行的步骤。

## <a name="next-steps"></a>后续步骤

* [处理 Azure Database for MariaDB 的暂时性连接错误](concepts-connectivity.md)
