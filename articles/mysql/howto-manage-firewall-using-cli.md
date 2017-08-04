---
title: "使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则 | Microsoft Docs"
description: "本文介绍了如何使用 Azure CLI 命令行创建和管理 Azure Database for MySQL 防火墙规则。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 9a03722e9f71be307bdbf0b846a4cbf7b34cd7ff
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017

---

# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-azure-cli"></a>使用 Azure CLI 创建和管理 Azure Database for MySQL 防火墙规则
使用服务器级防火墙规则，管理员可以管理从指定的 IP 地址或某个范围的 IP 地址对 Azure Database for MySQL 服务器的访问权限。 使用便捷的 Azure CLI 命令，可创建、更新、删除、列出和显示防火墙规则，用于管理服务器。 有关 Azure Database for MySQL 的概述，请参阅 [Azure Database for MySQL server firewall rules](./concepts-firewall-rules.md)（Azure Database for MySQL 服务器防火墙规则）

## <a name="prerequisites"></a>先决条件
* [安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* 安装适用于 PostgreSQL 和 MySQL 服务的 Azure Python SDK
* 安装适用于 PostgreSQL 和 MySQL 服务的 Azure CLI 组件
* 创建 Azure Database for MySQL 服务器

## <a name="firewall-rule-commands"></a>防火墙规则命令：
从 Azure CLI 中使用 az mysql server firewall-rule 命令，以创建、删除、列出、显示和更新防火墙规则。

命令：
- create：创建 Azure MySQL 服务器防火墙规则。
- delete：删除 Azure MySQL 服务器防火墙规则。
- list：列出 Azure MySQL 服务器防火墙规则。
- show：显示 Azure MySQL 服务器防火墙规则的详细信息。
- update：更新 Azure MySQL 服务器防火墙规则。

## <a name="login-to-azure-and-list-your-azure-database-for-mysql-servers"></a>登录到 Azure，并列出 Azure Database for MySQL 服务器
使用 Azure 帐户安全连接到 Azure CLI。 使用 az login 命令来执行此操作。

1. 从命令行运行以下命令。
```azurecli
az login
```
此命令将输出要在下一步骤中使用的代码。

2. 使用 Web 浏览器打开页面 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)，然后输入该代码。

3. 出现提示时，请使用 Azure 凭据登录。

4. 获得登录授权后，将在控制台中打印出订阅列表。 复制所需订阅的 ID，便于设置后期会使用到的当前订阅。
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. 如果不确定其名称，请列出订阅和资源组的 Azure Databases for MySQL 服务器。

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   请注意列表中的名称属性，该属性将用于指定使用哪个 MySQL 服务器。 如果需要，请确认该服务器的详细信息，使用名称属性确认名称是否正确：

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>列出 Azure Database for MySQL 服务器上的防火墙规则 
使用服务器名称和资源组名称，列出服务器上现有的服务器防火墙规则。 请注意，应在“--server”开关（而不是在“--name”开关）中指定服务器名称属性。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
输出将列出规则（如果存在），默认情况下采用 JSON 格式。 可以使用“--output table”开关，对输出使用更易于读取的表格格式。
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-firewall-rule-on-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器上的防火墙规则
使用 Azure MySQL 服务器名称和资源组名称，在服务器上创建新的防火墙规则。 输入规则名称，以及规则的起始 IP 和结束 IP，涵盖允许访问的一系列 IP 地址。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
如果允许单个 IP 地址进行访问，请将该地址用作起始 IP 和结束 IP，如下所示。
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
成功后，命令输出会列出已创建防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。

## <a name="update-firewall-rule-on-azure-database-for-mysql-server"></a>更新 Azure Database for MySQL 服务器上的防火墙规则 
使用 Azure MySQL 服务器名称和资源组名称，更新服务器上已有的防火墙规则。 输入现有防火墙规则的名称，提供要更新的起始 IP 和结束 IP 属性。
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
成功后，命令输出会列出更新后的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。

> [!NOTE]
> 如果不存在防火墙规则，更新命令将创建一个。

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>显示 Azure Database for MySQL 服务器上的防火墙规则的详细信息
使用 Azure MySQL 服务器名称和资源组名称，显示服务器上已有的防火墙规则的详细信息。 输入现有防火墙规则的名称。
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功后，命令输出会列出指定的防火墙规则的详细信息，默认情况下采用 JSON 格式。 如果失败，输出将改为显示错误消息文本。

## <a name="delete-firewall-rule-on-azure-database-for-mysql-server"></a>删除 Azure Database for MySQL 服务器上的防火墙规则
使用 Azure MySQL 服务器名称和资源组名称，从服务器中删除已有的防火墙规则。 输入现有防火墙规则的名称。
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
成功后没有任何输出。 如果失败，将返回错误消息文本。

## <a name="next-steps"></a>后续步骤
- 了解有关 [Azure Database for MySQL 服务器防火墙规则](./concepts-firewall-rules.md)的详细信息
- [使用 Azure 门户创建和管理 Azure Database for MySQL 防火墙规则](./howto-manage-firewall-using-portal.md)

