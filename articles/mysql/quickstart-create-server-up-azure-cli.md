---
title: 快速入门：使用 az mysql up 创建 Azure Database for MySQL
description: 有关使用 Azure CLI（命令行接口）up 命令创建 Azure Database for MySQL 服务器的快速入门指南。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 12/02/2019
ms.custom: mvc
ms.openlocfilehash: 4bb5c62a7df53548ff59a03c6ccc8fb28f1503d3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765677"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>快速入门：使用简单的 Azure CLI 命令 az mysql up 创建 Azure Database for MySQL（预览）

> [!IMPORTANT]
> [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI 命令处于预览状态。

Azure Database for MySQL 是一种托管服务，可用于在云中运行、管理和缩放高可用性的 MySQL 数据库。 Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 此快速入门介绍了如何使用 [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) 命令通过 Azure CLI 创建 Azure Database for MySQL 服务器。 除了创建服务器，`az mysql up` 命令还会创建示例数据库、在数据库中创建根用户、为 Azure 服务打开防火墙，并为客户端计算机创建默认防火墙规则。 这有助于加快开发过程。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 命令登录到你的帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。

```azurecli
az login
```

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅 ID。 将订阅 ID 占位符替换为订阅的 az login 输出中的“订阅 ID”属性   。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器

要使用命令，请安装 [db-up](/cli/azure/ext/db-up) 扩展。 如果返回错误，则请确保已安装最新版本的 Azure CLI。 请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

```azurecli
az extension add --name db-up
```

使用以下命令创建 Azure Database for MySQL 服务器：

```azurecli
az mysql up
```

服务器是使用以下默认值创建的（除非手动替代它们）：

**设置** | **默认值** | **说明**
---|---|---
server-name | 系统生成的 | 用于标识 Azure Database for MySQL 服务器的唯一名称。
resource-group | 系统生成的 | 新的 Azure 资源组。
sku-name | GP_Gen5_2 | SKU 的名称。 请遵循简写约定 {pricing tier}\_{compute generation}\_{vCores}。 默认为带 2 个 vCore 的常规用途 Gen5 服务器。 有关各层的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/mysql/)。
backup-retention | 7 | 保留备份的时长。 单位为天。
geo-redundant-backup | 已禁用 | 是否应该为此服务启用异地冗余备份。
location | westus2 | 服务器的 Azure 位置。
ssl-enforcement | 已禁用 | 是否应该为此服务启用 ssl。
storage-size | 5120 | 服务器的存储容量（单位是兆字节）。
版本 | 5.7 | MySQL 主版本。
admin-user | 系统生成的 | 用于管理员登录的用户名。
admin-password | 系统生成的 | 管理员用户的密码。

> [!NOTE]
> 有关 `az mysql up` 命令及其附加参数的详细信息，请参阅 [Azure CLI 文档](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)。

服务器创建后，随附有以下设置：

- 创建了一条名为“devbox”的防火墙规则。 Azure CLI 尝试检测运行 `az mysql up` 命令的计算机的 IP 地址以及该 IP 面向的允许列表。
- “允许访问 Azure 服务”设置为“开”。 此设置会将服务器的防火墙配置为接受来自所有 Azure 资源（包括非订阅中的资源）的连接。
- `wait_timeout` 参数设置为 8 个小时
- 创建了一个名为“sampledb”的空数据库
- 创建了一个特权为“sampledb”且名为“root”的新用户

> [!NOTE]
> Azure Database for MySQL 通过端口 3306 进行通信。 从企业网络内部进行连接时，该网络的防火墙可能不允许经端口 3306 的出站流量。 请让 IT 部门打开端口 3306 以连接到你的服务器。

## <a name="get-the-connection-information"></a>获取连接信息

在完成 `az mysql up` 命令后，将向你返回一个常用编程语言连接字符串列表。 这些连接字符串使用你新创建的 Azure Database for MySQL 服务器的特定属性进行预配置。

可使用 [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) 命令再次列出这些连接字符串。

## <a name="clean-up-resources"></a>清理资源

请使用以下命令清除在此快速入门中创建的所有资源。 此命令会删除 Azure Database for MySQL 服务器和资源组。

```azurecli
az mysql down --delete-group
```

要删除新创建的服务器，可运行 [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) 命令。

```azurecli
az mysql down
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Azure CLI 设计 MySQL 数据库](./tutorial-design-database-using-cli.md)
