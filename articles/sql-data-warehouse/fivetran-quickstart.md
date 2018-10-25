---
title: Azure SQL 数据仓库 Fivetran 快速入门 |Microsoft Docs
description: 快速开始使用 Fivetran 和 Azure SQL 数据仓库。
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355170"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>快速开始使用 Fivetran 和 SQL 数据仓库

本快速入门教程假定已有 SQL 数据仓库的实例。

## <a name="setup-connection"></a>设置连接

1. 查找用于连接 Azure SQL 数据仓库的完全限定的服务器名和数据库名。

   [如何从门户查找服务器名和数据库名？](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. 在安装向导中，确定是要直接连接数据库还是通过 SSH 隧道连接。

   如果决定直接连接数据库，需要创建防火墙规则，用于允许访问。 此方法是最简单且最安全的方法。

   如果决定通过 SSH 隧道进行连接，Fivetran 会连接到网络中一个单独的服务器，用于提供到数据库的 SSH 隧道。 如果数据库位于虚拟网络上一个无法访问的子网中，则必须使用此方法。

3. 在服务器级别防火墙中添加 "52.0.2.4" IP 地址以允许传入从 Fivetran 到 Azure SQL 数据仓库的连接。

   [如何添加服务器级防火墙？](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>设置用户凭据

使用 SQL Server Management Studio 或所选工具，以服务器管理员用户身份连接到 Azure SQL 数据仓库，并执行以下 SQL 命令，为 Fivetran 创建用户：

在 master 数据库中：` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

在 SQL 数据仓库数据库中：

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

创建用户 fivetran 后，立即向其授予以下仓库权限：

```
GRANT CONTROL to fivetran;
```

将合适的资源类添加到创建的用户，具体取决于有关创建列存储索引的内存要求。 例如，Marketo 和 Salesforce 等集成由于列数较多或数据量较大，需要较多内存来创建列存储索引，因此需要较高级别的资源类。

建议使用静态资源类。 一开始可以使用资源类 `staticrc20`，可为用户分配 200 MB，而无需考虑所使用的性能级别。 如果列存储索引无法使用当前资源类，需要增加资源类。

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

有关详细信息，请查看有关[内存和并发限制](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits)和[资源类](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)的文档

创建数据库范围的凭据需要 CONTROL 权限，使用 PolyBase 将文件从 Blob 存储中进行加载时需要使用该权限。

输入凭据以访问 Azure SQL 数据仓库

1. 主机（服务器名称）
2. 端口
3. 数据库
4. 用户（用户名应为 `fivetran@<server_name>`，其中 `<server_name>` 是 azure 主机 uri 的一部分：`<server_name>.database.windows.net`）
5. 密码