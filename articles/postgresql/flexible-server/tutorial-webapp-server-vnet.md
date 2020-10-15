---
title: 教程：在同一虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和 Azure 应用服务 Web 应用
description: 在虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和 Web 应用的快速入门指南
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 20401a3c96a9a20399c07d1a30370d27f2858e29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90929257"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>教程：在虚拟网络中创建 Azure Database for PostgreSQL 灵活服务器和应用服务 Web 应用

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

本教程介绍如何在[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)中创建 Azure 应用服务 Web 应用和 Azure Database for PostgreSQL 灵活服务器（预览版）。

在本教程中，你将执行以下操作：
>[!div class="checklist"]
> * 在虚拟网络中创建 PostgreSQL 灵活服务器
> * 创建 Web 应用
> * 将 Web 应用添加到虚拟网络
> * 从 Web 应用连接到 Postgres 

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 命令登录到你的帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。

```azurecli
az login
```

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅 ID。 将 az login 输出中的你的订阅的订阅 ID 属性替换到订阅 ID 占位符中 。

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>在新的虚拟网络中创建 PostgreSQL 灵活服务器

使用以下命令在虚拟网络 (VNET) 中创建专用灵活服务器：
```azurecli
az postgres flexible-server create --resource-group myresourcegroup --location westus2
```
此命令将执行以下操作，可能需要花几分钟的时间：

- 创建资源组（如果尚不存在）。
- 生成服务器名称（如果未提供）。
- 为新的 PostgreSQL 服务器创建新的虚拟网络。 记下为服务器创建的虚拟网络名称和子网名称，因为你需要将 Web 应用添加到同一虚拟网络。
- 创建服务器的管理员用户名和密码（如果未提供）。
- 创建一个名为 postgres 的空数据库

> [!NOTE]
> - 记下你的密码，如果未提供密码，系统将为你生成一个。 如果忘记密码，则必须使用 ``` az postgres flexible-server update``` 命令重置密码
> - 如果使用的不是应用服务环境，则需要使用此命令启用“允许从任何 Azure IP 进行访问”。 
>  ```azurecli
>  az postgres flexible-server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
>  ```


## <a name="create-a-web-app"></a>创建 Web 应用
在本部分中，你将在应用服务应用中创建应用主机，将此应用连接到 Postgres 数据库，然后将代码部署到该主机。 在终端中，请确保你位于应用程序代码的存储库根路径。

使用 az webapp up 命令创建应用服务应用（主机进程）

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku B1 --name mywebapp
```

> [!NOTE]
> - 对于 --location 参数，请使用与上一节中数据库相同的位置。
> - 请将 <app-name> 替换为在整个 Azure 中唯一的名称（服务器终结点为 https://\<app-name>.azurewebsites.net）。 <app-name> 允许的字符包括“A-Z”、“0-9”和“-”。 良好的模式是结合使用公司名称和应用标识符。

此命令将执行以下操作，可能需要花几分钟的时间：

- 创建资源组（如果尚不存在）。 （在此命令中，你将使用之前在其中创建数据库的同一资源组。）
- 在基本定价层 (B1) 中创建应用服务计划 ```testappserviceplan```（如果不存在）。 --plan 和--sku 是可选的。
- 创建应用服务应用（如果不存在）。
- 为应用启用默认日志记录（如果尚未启用）。
- 在启用了生成自动化的情况下，使用 ZIP 部署上传存储库。

## <a name="add-the-web-app-to-the-virtual-network"></a>将 Web 应用添加到虚拟网络
使用 az webapp vnet-integration 命令向 webapp 添加区域虚拟网络集成。 将 <vnet-name> 和 <subnet-name> 替换为灵活服务器使用的虚拟网络和子网名称。

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>
```

## <a name="configure-environment-variables-to-connect-the-database"></a>配置环境变量以连接数据库
将代码部署到应用服务后，下一步是将应用连接到 Azure 中的灵活服务器。 应用代码需要在多个环境变量中查找数据库信息。 若要在应用服务中设置环境变量，则使用 ```az webapp config appsettings``` set 命令创建“应用设置”。

```azurecli
az webapp config appsettings set --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```


- 为新创建的灵活服务器命令替换 ```postgres-server-name```、```username``` 和 ```password```。
- 将 <username> 和 <password> 替换为命令也为你生成的凭据。
- 资源组和应用名称是从“.azure/config”文件中的缓存值中提取的。
- 此命令会创建名为 ```DBHOST```、```DBNAME```、```DBUSER``` 和 ```DBPASS``` 的设置。 如果应用程序代码对数据库信息使用了不同的名称，则如代码中所述，对应用设置使用这些名称。

## <a name="clean-up-resources"></a>清理资源

使用以下命令清除在此教程中创建的所有资源。 此命令将删除这一资源组中的所有资源。

```azurecli
az group delete -n myresourcegroup
```


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)