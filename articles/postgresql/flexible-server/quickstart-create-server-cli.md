---
title: 快速入门：创建服务器 - Azure CLI - Azure Database for PostgreSQL 灵活服务器
description: 本快速入门介绍如何使用 Azure CLI 在 Azure 资源组中创建 Azure Database for PostgreSQL 灵活服务器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/22/2020
ms.custom: mvc
ms.openlocfilehash: 78824ef6f557a2df431e664158da9d858df0603f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944092"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-flexible-server-using-azure-cli"></a>快速入门：使用 Azure CLI 创建 Azure Database for PostgreSQL 灵活服务器

本快速入门介绍如何使用 [Azure Cloud Shell](https://shell.azure.com) 中的 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 命令在大约 5 分钟内创建 Azure Database for PostgreSQL 灵活服务器。 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

> [!IMPORTANT] 
> Azure Database for PostgreSQL 灵活服务器当前以预览版提供。

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com/bash](https://shell.azure.com/bash) 打开 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后选择 Enter 来运行它。  

如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="prerequisites"></a>先决条件

你将需要使用 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) 命令登录到你的帐户。 请注意 id 属性，该属性指的是 Azure 帐户的订阅 ID。

```azurecli-interactive
az login
```

使用 [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) 命令选择帐户下的特定订阅。 记下 az login 输出中的 id 值，以用作命令中订阅参数的值。 如果有多个订阅，请选择应计费的资源所在的相应订阅。 若要获取所有订阅，请使用 [az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>创建灵活服务器

使用 `az group create` 命令创建 [Azure 资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)，然后在此资源组中创建 PostgreSQL 灵活服务器。 应提供唯一名称。 以下示例在 `westus` 位置创建名为 `myresourcegroup` 的资源组。

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

使用 `az postgres flexible-server create` 命令创建灵活服务器。 一个服务器可以包含多个数据库。 以下命令使用服务默认值和 Azure CLI [本地上下文](https://docs.microsoft.com/cli/azure/local-context?view=azure-cli-latest)中的值创建服务器： 

```azurecli
az postgres flexible-server create
```

创建的服务器具有以下属性： 
- 自动生成的服务器名称、管理员用户名、管理员密码、资源组名称（如果尚未在本地上下文中指定），并且与资源组位于同一位置 
- 其余服务器配置的服务默认值：计算层（常规用途）、计算大小/SKU（D2s_v3 - 2 个 vCore、8 GB RAM）、备份保持期（7 天）和 PostgreSQL 版本 (12)
- 默认的连接方法是设置了自动生成的虚拟网络和子网的“专用访问(VNet 集成)”

> [!NOTE] 
> 创建服务器后，无法更改连接方法。 例如，如果在创建过程中选择了“专用访问(VNet 集成)”，则在创建后无法将其更改为“公共访问(允许的 IP 地址)” 。 强烈建议创建采用专用访问的服务器，以使用 VNet 集成安全地访问你的服务器。 若要详细了解专用访问，请参阅[概念文章](./concepts-networking.md)。

如果要更改任何默认设置，请参阅 Azure CLI 参考文档， <!--FIXME --> 以获取可配置 CLI 参数的完整列表。 

> [!NOTE]
> 连接到 Azure Database for PostgreSQL 时，经端口 5432 进行通信。 如果尝试从企业网络内部进行连接，则可能不允许经端口 5432 的出站流量。 如果是这样，则无法连接到服务器，除非 IT 部门打开了端口 5432。

## <a name="get-the-connection-information"></a>获取连接信息

若要连接到服务器，需要提供主机信息和访问凭据。

```azurecli-interactive
az postgres flexible-server show --resource-group myresourcegroup --name mydemoserver
```

结果采用 JSON 格式。 记下 fullyQualifiedDomainName  和 administratorLogin  。

<!--FIXME-->
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "name": "Standard_D2s_v3",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "publicAccess": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 131072
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/flexibleServers",
  "userVisibleState": "Ready",
  "version": "12"
}
```

## <a name="connect-using-postgresql-command-line-client"></a>使用 PostgreSQL 命令行客户端进行连接

由于使用“专用访问(VNet 集成)”创建了灵活服务器，因此你需要从与服务器相同的 VNet 中的资源连接到服务器。 可以创建虚拟机并将其添加到创建的虚拟网络中。 

创建虚拟机后，可以通过 SSH 进入计算机并安装 [psql](https://www.postgresql.org/download/) 命令行工具。

使用 psql，通过以下命令进行连接。 将值替换为实际的服务器名称和密码。 

```bash
psql -h mydemoserver.postgres.database.azure.com -u mydemouser -p
```

## <a name="clean-up-resources"></a>清理资源

如果不需要将这些资源用于其他快速入门/教程，则可通过执行以下命令将其删除：

```azurecli-interactive
az group delete --name myresourcegroup
```

如果只想删除这一新创建的服务器，可以运行 `az postgres flexible-server delete` 命令。

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用应用服务和 PostgreSQL 部署 Django 应用](tutorial-django-app-service-postgres.md)