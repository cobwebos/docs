---
title: "Azure CLI：创建和查询单一 SQL 数据库 | Microsoft 文档"
description: "了解如何使用 Azure CLI 创建 SQL 数据库逻辑服务器、服务器级防火墙规则和数据库。"
keywords: "SQL 数据库教程：创建 SQL 数据库"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>在 Azure CLI 中创建和查询单一 Azure SQL 数据库

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述了如何使用 Azure CLI 部署 Azure SQL 数据库。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI installation guide](https://docs.microsoft.com/cli/azure/install-azure-cli)（Azure CLI 安装指南）。 

## <a name="log-in-to-azure"></a>登录 Azure

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>创建逻辑服务器

使用 [az sql server create](/cli/azure/sql/server#create) 命令创建逻辑服务器。 以下示例使用管理员用户名 `ServerAdmin` 和密码 `ChangeYourAdminPassword1` 在资源组中创建随机命名的服务器。 根据需要替换这些预定义的值。

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>配置服务器防火墙规则

使用 [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create) 命令创建服务器级防火墙规则。 服务器级防火墙规则允许外部服务器（例如 SQL Server Management Studio 或 SQLCMD 实用程序）通过 SQL 数据库服务防火墙连接到 SQL 数据库。 以下示例为预定义的地址范围创建了防火墙规则，该地址范围在本示例中是整个可能的 IP 地址范围。 将这些预定义的值替换为外部 IP 地址或 IP 地址范围的值。 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>在服务器中创建数据库

使用 [az sql db create](/cli/azure/sql/db#create) 命令在服务器中创建数据库。 以下示例创建一个空的名为 `mySampleDatabase` 的数据库。 根据需要替换此预定义的值。

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>清理资源

若要删除通过此快速入门创建的所有资源，请运行以下命令：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio 进行连接，请参阅[使用 Visual Studio 进行连接和查询](sql-database-connect-query.md)。
- 有关 SQL 数据库的技术概述，请参阅[关于 SQL 数据库服务](sql-database-technical-overview.md)。

