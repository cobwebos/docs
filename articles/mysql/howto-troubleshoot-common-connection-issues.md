---
title: 排查 Azure Databases for MySQL 的连接问题 | Microsoft Docs
description: 了解如何排查 Azure Databases for MySQL 的连接问题。
keywords: mysql 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/09/2018
ms.openlocfilehash: 901c8f0c903a7e26e82d89209ea4b0ec69057d23
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165396"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>排查 Azure Databases for MySQL 的连接问题

连接问题可能由多种因素造成，包括：

* 防火墙设置
* 连接超时
* 不正确的登录信息
* 某些 Azure Database for MySQL 资源达到了最大限制
* 服务的基础结构出现问题
* 正在服务中执行维护
* 通过缩放 vCore 数目或移到不同的服务层更改了服务器的计算资源分配

通常，Azure Database for MySQL 的连接问题可按如下方式分类：

* 暂时性错误（短暂或间歇性）
* 持久或非暂时性错误（定期重复发生的错误）

## <a name="troubleshoot-transient-errors"></a>对暂时性错误进行故障排除

在执行维护、系统遇到硬件或软件错误，或更改服务器的 vCore 数目或服务层时，会发生暂时性错误。 Azure Database for MySQL 服务具有内置的高可用性，可以自动缓解此类问题。 但是，应用程序会短暂地与服务器断开连接，通常不会超过 60 秒。 某些事件偶尔可能需要更长的时间才能缓解，例如，当某个大型事务导致恢复长时间运行时。

### <a name="steps-to-resolve-transient-connectivity-issues"></a>解决暂时性连接问题的步骤

1. 查看 [Microsoft Azure 服务仪表板](https://azure.microsoft.com/status)以了解应用程序报告错误时出现的任何已知中断。
2. 连接到云服务的应用程序（例如 Azure Database for MySQL）应预料到会发生暂时性错误，并实施重试逻辑来处理这些错误，而不是将它们作为应用程序错误展现给用户。 查看[处理 Azure Database for MySQL 的暂时性连接错误](concepts-connectivity.md)，了解有关处理暂时性错误的最佳做法和设计准则。
3. 由于服务器即将达到其资源限制，错误可能看起来像是暂时性连接问题。 请参阅 [Azure Database for MySQL 中的限制](concepts-limits.md)。
4. 如果连接问题继续存在，或者应用程序发生错误的持续时间超过 60 秒或在特定的一天中看到错误多次发生，请通过在 [Azure 支持](https://azure.microsoft.com/support/options)网站上选择“**获取支持**”提出 Azure 支持请求。

## <a name="troubleshoot-persistent-errors"></a>排查一再出现的错误

如果应用程序一直无法连接到 Azure Database for MySQL，通常表示下列其中一项出现了问题：

* 服务器防火墙配置：请确保 Azure Database for MySQL 服务器防火墙配置为允许来自客户端（包括代理服务器和网关）的连接。
* 客户端防火墙配置：客户端的防火墙必须允许连接到数据库服务器。 不能访问的服务器的 IP 地址和端口以及一些防火墙中的应用程序名称（如 MySQL）必须被允许。
* 用户失误：例如，连接参数（例如，连接字符串中的服务器名称）键入错误，或者在用户名中遗漏了 *@servername* 后缀）。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>解决永久性连接问题的步骤

1. 设置[防火墙规则](howto-manage-firewall-using-portal.md)以允许客户端 IP 地址。 （仅出于临时测试目的）使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束 IP 地址，来设置一个防火墙规则。 这会在所有 IP 地址上打开服务器。 如果这样可以解决连接性问题，请删除此规则，再针对适当限制的 IP 地址或地址范围创建防火墙规则。
2. 在客户端与 Internet 之间的所有防火墙上，确保为出站连接打开端口 3306。
3. 验证连接字符串和其他连接设置。 查看[如何将应用程序连接到 Azure Database for MySQL](howto-connection-string.md)。
4. 在仪表板中检查服务运行状况。 如果你认为发生了区域性服务中断，请参阅[有关使用 Azure Database for MySQL 确保业务连续性的概述](concepts-business-continuity.md)了解恢复到新区域所要执行的步骤。

## <a name="next-steps"></a>后续步骤

* [处理 Azure Database for MySQL 的暂时性连接错误](concepts-connectivity.md)
