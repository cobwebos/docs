---
title: Fivetran 快速入门
description: 快速开始使用 Fivetran 和 Azure SQL 数据仓库。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 715f891484458f3bf3febc6807c3490b88062d50
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229094"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>快速开始使用 Fivetran 和 SQL 数据仓库

本快速入门介绍如何设置一个新的可以使用 Azure SQL 数据仓库的 Fivetran 用户。 本文假定你已有 SQL 数据仓库的实例。

## <a name="set-up-a-connection"></a>设置连接

1. 查找用于连接 SQL 数据仓库的完全限定的服务器名和数据库名。
    
    如果在查找该信息时需要帮助，请参阅[连接到 Azure SQL 数据仓库](sql-data-warehouse-connect-overview.md)。

2. 在安装向导中，选择是要直接连接数据库还是通过 SSH 隧道进行连接。

   如果选择直接连接数据库，则必须创建防火墙规则，用于允许访问。 此方法是最简单且最安全的方法。

   如果选择通过 SSH 隧道进行连接，Fivetran 会连接到网络中一个单独的服务器。 服务器提供一个连接到数据库的 SSH 隧道。 如果数据库位于虚拟网络上一个无法访问的子网中，则必须使用此方法。

3. 在服务器级别防火墙中添加 IP 地址 **52.0.2.4**，允许传入从 Fivetran 到 SQL 数据仓库实例的连接。

   有关详细信息，请参阅[创建服务器级防火墙规则](create-data-warehouse-portal.md#create-a-server-level-firewall-rule)。

## <a name="set-up-user-credentials"></a>设置用户凭据

1. 使用 SQL Server Management Studio 或首选工具连接到 Azure SQL 数据仓库。 以服务器管理员用户身份登录。 然后，运行以下 SQL 命令，为 Fivetran 创建一个用户：
    - 在 master 数据库中： 
    
      ```
      CREATE LOGIN fivetran WITH PASSWORD = '<password>'; 
      ```

    - 在 SQL 数据仓库数据库中：

      ```
      CREATE USER fivetran_user_without_login without login;
      CREATE USER fivetran FOR LOGIN fivetran;
      GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
      ```

2. 向 Fivetran 用户授予以下仓库权限：

    ```
    GRANT CONTROL to fivetran;
    ```

    创建数据库范围的凭据需要 CONTROL 权限，而用户在通过 PolyBase 从 Azure Blob 存储加载文件时会使用该凭据。

3. 向 Fivetran 用户添加适当的资源类。 所用资源类取决于创建列存储索引时需要的内存。 例如，与 Marketo 和 Salesforce 之类的产品集成时由于产品使用的列数较多且数据量较大，需要较高级别的资源类。 较高级别的资源类需要更多内存来创建列存储索引。

    建议使用静态资源类。 可以从 `staticrc20` 资源类着手。 资源类 `staticrc20` 为每个用户分配 200 MB，不考虑你所使用的性能级别。 如果列存储索引无法使用初始资源类级别，请提高资源类的级别。

    ```
    EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
    ```

    有关详细信息，请阅读[内存和并发限制](memory-concurrency-limits.md)和[资源类](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md#ways-to-allocate-more-memory)。


## <a name="sign-in-to-fivetran"></a>登录到 Fivetran

若要登录到 Fivetran，请输入用于访问 SQL 数据仓库的凭据： 

* 主机（服务器名称）。
* 端口。
* 数据库。
* 用户（用户名应为 " **fivetran\@_server_name_**  ，其中*server_name*是 Azure 主机 URI 的一部分： ***server_name *.** e）。
* Password。
