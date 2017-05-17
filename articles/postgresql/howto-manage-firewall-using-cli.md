---
title: "使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则 | Microsoft Docs"
description: "介绍了如何使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则
使用服务器级防火墙规则，管理员可以管理特定 IP 地址或一系列 IP 地址对 Azure Database for PostgreSQL 服务器的访问权限。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 有关 Azure Database for PostgreSQL 防火墙的概述，请参阅 [Azure Database for PostgreSQL Server firewall rules](concepts-firewall-rules.md)（Azure Database for PostgreSQL 服务器防火墙规则）

## <a name="prerequisites"></a>先决条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL 服务器和数据库](quickstart-create-server-database-azure-cli.md)
- 已安装 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 命令行实用工具

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>配置 Azure Database for PostgreSQL 的防火墙规则
从 Azure CLI 使用 az postgres server firewall-rule 命令，创建、删除、列出、显示和更新防火墙规则。 此 

## <a name="login-to-azure-and-list-servers"></a>登录到 Azure 并列出服务器
使用 Azure 帐户安全连接到 Azure CLI。 使用 az login 命令来执行此操作。
1. 从命令行运行以下命令。
```azurecli
az login
```
此命令将输出要在下一步骤中使用的代码。

2. 使用 Web 浏览器打开页面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入该代码。

3. 出现提示时，请使用 Azure 凭据登录。

4. 获得登录授权后，将在控制台中打印出订阅列表。
复制所需订阅的 ID，便于设置后期会使用到的当前订阅。
```azurecli
az account set --subscription {your subscription id}
```
5. 如果不确定其名称，请列出订阅和资源组的 Azure Databases for PostgreSQL 服务器。

```azurecli
az postgres server list --resource-group myresourcegroup
```
请注意列表中的名称属性，该属性将用于指定使用哪个 PostgreSQL 服务器。 如果需要，请确认该服务器的详细信息，使用名称属性确认名称是否正确：

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>列出防火墙规则 
使用服务器名称和资源组名称，列出服务器上现有的服务器防火墙规则。 请注意，应在“--server”开关（而不是在“--name”开关）中指定服务器名称属性。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
输出将列出规则（如果存在），默认情况下采用 JSON 格式。 可以使用“--output table”开关，对输出使用更易于读取的表格格式。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>创建防火墙规则
使用 Azure Database for PostgreSQL 服务器名称和资源组名称，在服务器上创建新的防火墙规则。 输入规则名称，以及规则的起始 IP 和结束 IP，涵盖允许访问的一系列 IP 地址。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
如果允许单个 IP 地址进行访问，请将该地址用作起始 IP 和结束 IP，如下所示。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
成功后，命令输出会列出已创建防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。

## <a name="update-firewall-rule"></a>更新防火墙规则 
使用 Azure Database for PostgreSQL 服务器名称和资源组名称，更新服务器上的现有防火墙规则。 输入现有防火墙规则的名称，提供要更新的起始 IP 和结束 IP 属性。
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。
> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建一个。

## <a name="show-firewall-rule-details"></a>显示防火墙规则详细信息
使用 Azure Database for PostgreSQL 服务器名称和资源组名称，显示服务器上现有防火墙规则的详细信息。 输入现有防火墙规则的名称。
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。

## <a name="delete-firewall-rule"></a>删除防火墙规则
使用 Azure Database for PostgreSQL 服务器名称和资源组名称，从服务器中删除现有防火墙规则。 输入现有防火墙规则的名称。
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
成功后没有任何输出。 如果失败，将返回错误消息文本。

## <a name="next-steps"></a>后续步骤
- 同样，也可以在 Web 浏览器中[使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](howto-manage-firewall-using-portal.md)
- 了解有关 [Azure Database for PostgreSQL 服务器防火墙规则](concepts-firewall-rules.md)的详细信息
- 有关连接到 Azure Database for PostgreSQL 服务器的帮助，请参阅 [Azure Database for PostgreSQL 的连接库](concepts-connection-libraries.md)

