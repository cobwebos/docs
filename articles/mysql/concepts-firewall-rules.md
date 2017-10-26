---
title: "Azure Database for MySQL 服务器防火墙规则 | Microsoft Docs"
description: "描述 Azure Database for MySQL 服务器的防火墙规则。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/20/2017
ms.openlocfilehash: aea561b526d6f3f818fd75771dd8c65c9f25051a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2017
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL 服务器防火墙规则
在指定哪些计算机具有访问权限之前，防火墙将禁止所有对数据库服务器的访问。 防火墙基于每个请求的起始 IP 地址授予对服务器的访问权限。

要配置防火墙，请创建防火墙规则，指定可接受的 IP 地址的范围。 可以在服务器级别创建防火墙规则。

防火墙规则：这些规则允许客户端访问整个 Azure Database for MySQL 服务器，即同一逻辑服务器内的所有数据库。 可使用 Azure 门户或 Azure CLI 命令配置服务器级的防火墙规则。 若要创建服务器级防火墙规则，用户必须是订阅所有者或订阅参与者。

## <a name="firewall-overview"></a>防火墙概述
防火墙将默认阻止对 Azure Database for MySQL 服务器的所有数据库访问。 若要从另一台计算机开始使用服务器，需要指定一个或多个服务器级防火墙规则以允许访问服务器。 使用防火墙规则指定要允许的来自 Internet 的 IP 地址范围。 对 Azure 门户网站本身的访问不受防火墙规则影响。

来自 Internet 和 Azure 的连接尝试必须首先通过防火墙，才能访问 Azure Database for MySQL 数据库，如下图中所示：

![防火墙工作流示例](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>从 Internet 连接
服务器级防火墙规则适用于 Azure Database for MySQL 服务器上的所有数据库。

如果该请求的 IP 地址位于服务器级防火墙规则中指定的某个范围内，则授予连接权限。

如果该请求的 IP 地址位于任何数据库级或服务器级防火墙规则中指定的范围外，则连接请求失败。

## <a name="programmatically-managing-firewall-rules"></a>以编程方式管理防火墙规则
除了 Azure 门户外，还可使用 Azure CLI 通过编程方式管理防火墙规则。 另请参阅[使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>数据库防火墙故障排除
对 Microsoft Azure Database for MySQL 服务器服务的访问与预期不符时，请考虑以下几点：

* 对允许列表的更改尚未生效：对 Azure Database for MySQL 服务器防火墙配置所做的更改可能需要多达 5 分钟的延迟才可生效。

* 登录名未授权或使用了错误的密码：如果某个登录名对 Azure Database for MySQL 服务器没有权限或者使用的密码不正确，则与 Azure Database for MySQL 服务器的连接会被拒绝。 创建防火墙设置仅向客户端提供尝试连接到服务器的机会；每个客户端必须提供必需的安全凭据。

* 动态 IP 地址：如果 Internet 连接使用动态 IP 寻址，并且在通过防火墙时遇到问题，可尝试以下解决方法之一：

* 向 Internet 服务提供商 (ISP) 询问分配给客户端计算机、用于访问 Azure Database for MySQL 服务器的 IP 地址范围，然后将该 IP 地址范围作为防火墙规则添加。

* 改为获取客户端计算机的静态 IP 地址，然后将该 IP 地址作为防火墙规则添加。

## <a name="next-steps"></a>后续步骤

[使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)
[使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-cli.md)
