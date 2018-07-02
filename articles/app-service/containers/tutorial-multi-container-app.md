---
title: 在用于容器的 Web 应用中创建多容器（预览版）应用
description: 了解如何配合 WordPress 和 MySQL 应用，通过 Docker Compose 和 Kubernetes 配置文件在 Azure 上使用多个容器。
keywords: azure 应用服务, web 应用, linux, docker, compose, 多容器, 容器, kubernetes
services: app-service
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 43a3fa271a1958c99bd3dd597c73de2d77bb1bfd
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751908"
---
# <a name="tutorial-create-a-multicontainer-preview-app-in-web-app-for-containers"></a>教程：在用于容器的 Web 应用中创建多容器（预览版）应用

在[用于容器的 Web 应用](app-service-linux-intro.md)中可以灵活使用 Docker 映像。 本教程介绍如何使用 WordPress 和 MySQL 创建多容器应用。

本教程介绍以下操作：
> [!div class="checklist"]
> * 转换 Docker Compose 配置以使用用于容器的 Web 应用
> * 转换 Kubernetes 配置以使用用于容器的 Web 应用
> * 将多容器应用部署到 Azure
> * 添加应用程序设置
> * 对容器使用持久性存储
> * 连接到 Azure Database for MySQL
> * 排查错误

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* 安装 [Azure CLI](/cli/azure/install-azure-cli)（2.0.32 或更高版本）。
* [Docker Compose](https://docs.docker.com/compose/) 或 [Kubernetes](https://kubernetes.io/) 方面的经验。

## <a name="create-a-deployment-user"></a>创建部署用户

在本地命令提示符下，使用 [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set) 命令创建部署凭据。 对 Web 应用进行 FTP 和本地 Git 部署时需要此部署用户。 用户名和密码都为帐户级别。 它们与 Azure 订阅凭据不同。

在以下示例中，将 *\<username>* 和 *\<password>*（包括括号）替换为新的用户名和密码。 用户名在 Azure 中必须唯一。 密码长度必须至少为 8 个字符，其中包含以下 3 种元素中的两种：字母、数字、符号。

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

应该会获得一个 JSON 输出，密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 `'Bad Request'. Details: 400` 错误，请使用更强的密码。

只创建此部署用户一次；可对所有 Azure 部署使用此用户。

> [!NOTE]
> 记录用户名和密码。 稍后需要使用这些凭据来部署 Web 应用。
>
>

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

在本地命令提示符下，使用 [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) 命令创建一个资源组。 以下示例在“美国中南部”位置创建名为 *myResourceGroup* 的资源组。 若要查看**标准**层中 Linux 上的应用服务支持的所有位置，请运行 [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

通常在附近的区域中创建资源组和资源。

此命令完成后，JSON 输出会显示资源组属性。

## <a name="create-an-azure-app-service-plan"></a>创建 Azure 应用服务计划

在本地命令提示符下，使用 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create) 命令在资源组中创建应用服务计划。

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

以下示例在**标准**定价层 (`--sku S1`) 和 Linux 容器 (`--is-linux`) 中创建名为 `myAppServicePlan` 的应用服务计划。

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

创建应用服务计划后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

## <a name="docker-compose-configuration-options"></a>Docker Compose 配置选项

本教程使用 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project) 中的 compose 文件，但我们将对其进行修改，以包含 Azure Database for MySQL、持久性存储和 Redis。 或者，可以使用 [Kubernetes 配置](#use-a-kubernetes-configuration-optional)。 可在 [Azure 示例](https://github.com/Azure-Samples/multicontainerwordpress)中找到配置文件。

以下列表显示了用于容器的 Web 应用中支持和不支持的 Docker Compose 配置选项：

### <a name="supported-options"></a>支持的选项

* command
* entrypoint
* 环境
* 图像
* ports
* restart
* services
* volumes

### <a name="unsupported-options"></a>不支持的选项

* build（不允许）
* depends_on（忽略）
* networks（忽略）
* secrets（忽略）

> [!NOTE]
> 未显式指明的任何其他选项在公共预览版中也将被忽略。

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>使用 WordPress 和 MySQL 容器的 Docker Compose

在本地将以下 YAML 复制并粘贴到名为 `compose-wordpress.yml` 的文件。

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

## <a name="create-a-docker-compose-app"></a>创建 Docker Compose 应用

在本地命令提示符终端中，使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myAppServicePlan` 应用服务计划中创建多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为唯一的应用名称。

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。 该应用可能需要几分钟时间才能完成加载。 如果收到错误，请在几分钟后刷新浏览器。 如果遇到问题并想要进行故障排除，请查看[容器日志](#find-docker-container-logs)。

![用于容器的 Web 应用中的示例多容器应用][1]

**恭喜**，你已在用于容器的 Web 应用中创建了多容器应用。 接下来，请将应用配置为使用 Azure Database for MySQL。 暂时不要安装 WordPress。

## <a name="connect-to-production-database"></a>连接到生产数据库

不建议在生产环境中使用数据库容器。 本地容器不可缩放。 应改用可缩放的 Azure Database for MySQL。

### <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器

使用 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 命令创建 Azure Database for MySQL 服务器。

在以下命令中，请将 &lt;mysql_server_name> 占位符替换为你自己的唯一 MySQL 服务器名称（有效字符是 `a-z`、`0-9` 和 `-`）。 此名称是 MySQL 服务器主机名 (`<mysql_server_name>.database.windows.net`) 的一部分，必须全局唯一。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

创建 MySQL 服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 命令创建 MySQL 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。
>

### <a name="create-the-wordpress-database"></a>创建 WordPress 数据库

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

创建数据库后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>在 WordPress 中配置数据库变量

若要将 WordPress 应用连接到这个新的 MySQL 服务器，请配置几个特定于 WordPress 的环境变量，包括 `MYSQL_SSL_CA` 定义的 SSL CA 路径。 以下[自定义映像](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations)中提供了来自 [DigiCert](http://www.digicert.com/) 的 [Baltimore 网络信任根证书](https://www.digicert.com/digicert-root-certificates.htm)。

若要做出这些更改，请在本地命令提示符终端中使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 应用设置区分大小写，用空格分开。

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

创建应用设置后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>对 MySQL SSL 和其他配置使用自定义映像

默认情况下，SSL 由 Azure Database for MySQL 使用。 WordPress 需要附加的配置才能将 SSL 和 MySQL 配合使用。 WordPress“官方映像”不提供附加的配置，但出于方便，已准备了一个[自定义映像](https://hub.docker.com/r/microsoft/multicontainerwordpress/builds/)。 在实践中，请将所需的更改添加到自己的映像。

自定义映像基于 [Docker 中心的 WordPress](https://hub.docker.com/_/wordpress/) 的“官方映像”。 适用于 Azure Database for MySQL 的此自定义映像中已做出以下更改：

* [将适用于 SSL 的 Baltimore 网络信任根证书文件添加到 MySQL。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [在 WordPress wp-config.php 中使用 MySQL SSL 证书颁发机构证书的应用设置。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [为 MySQL SSL 所需的 MYSQL_CLIENT_FLAGS 添加 WordPress 定义。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

为 Redis 做出了以下更改（在稍后的部分中使用）：

* [添加 Redis v4.0.2 的 PHP 扩展。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [添加解压缩功能以实现文件解压缩。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [添加 Redis 对象缓存 1.3.8 WordPress 插件。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [在 WordPress wp-config.php 中使用 Redis 主机名的应用设置。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

若要使用自定义映像，请更新 compose-wordpress.yml 文件。 更改 `image: wordpress` 以使用 `image: microsoft/multicontainerwordpress`。 不再需要数据库容器。 从配置文件中删除 `db`、`environment`、`depends_on` 和 `volumes` 节。 文件应如以下代码所示：

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>使用新配置更新应用

在本地命令提示符终端中，使用 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) 命令重新配置多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为前面创建的 Web 应用的名称。

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

重新配置应用后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。 应用正在使用 Azure Database for MySQL。

![用于容器的 Web 应用中的示例多容器应用][1]

## <a name="add-persistent-storage"></a>添加持久性存储

你的多容器应用现在正在用于容器的 Web 应用中运行。 但是，如果现在安装 WordPress 并稍后重启应用，则会发现 WordPress 安装已消失。 之所以发生这种情况，是因为 Docker Compose 配置当前指向容器中的存储位置。 重启应用后，在容器中安装的文件不会保留。 在本部分，我们将持久性存储添加到 WordPress 容器。

### <a name="configure-environment-variables"></a>配置环境变量

若要使用持久性存储，请在应用服务中启用此设置。 若要做出此更改，请在本地命令提示符终端中使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 应用设置区分大小写，用空格分开。

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

创建应用设置后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>修改配置文件

再次打开 *compose-wordpress.yml*。

`volumes` 选项将文件系统映射到容器中的某个目录。 `${WEBAPP_STORAGE_HOME}` 是应用服务中已映射到应用持续性存储的环境变量。 将在 volumes 选项中使用此环境变量，以便将 WordPress 文件安装到持久性存储，而不是安装到容器。 对文件做出以下修改：

如以下代码所示，在 `wordpress` 节中添加 `volumes` 选项：

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>使用新配置更新应用

在本地命令提示符终端中，使用 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) 命令重新配置多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为唯一的应用名称。

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

该命令运行后，会显示类似于以下示例的输出：

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。

WordPress 容器正在使用 Azure Database for MySQL 和持久性存储。

## <a name="add-redis-container"></a>添加 Redis 容器

 WordPress“官方映像”不包含 Redis 的依赖项。 此[自定义映像](https://github.com/Azure-Samples/multicontainerwordpress)中准备好这些依赖项和所需的附加配置，以便用户能够配合 WordPress 使用 Redis。 在实践中，请将所需的更改添加到自己的映像。

自定义映像基于 [Docker 中心的 WordPress](https://hub.docker.com/_/wordpress/) 的“官方映像”。 适用于 Redis 的此自定义映像中已做出以下更改：

* [添加 Redis v4.0.2 的 PHP 扩展。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [添加解压缩功能以实现文件解压缩。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [添加 Redis 对象缓存 1.3.8 WordPress 插件。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [在 WordPress wp-config.php 中使用 Redis 主机名的应用设置。](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

如以下示例所示，将 Redis 容器添加到配置文件的底部：

[!code-yml[Main](../../../azure-app-service-multi-container/compose-wordpress.yml)]

### <a name="configure-environment-variables"></a>配置环境变量

若要使用 Redis，请在应用服务中启用 `WP_REDIS_HOST` 设置。 WordPress 在与 Redis 主机通信时必须使用此设置。 若要做出此更改，请在本地命令提示符终端中使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 应用设置区分大小写，用空格分开。

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WP_REDIS_HOST="redis"
```

创建应用设置后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>使用新配置更新应用

在本地命令提示符终端中，使用 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) 命令重新配置多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为唯一的应用名称。

```bash
az webapp config container set --resource-group myResourceGroup --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

该命令运行后，会显示类似于以下示例的输出：

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。

完成上述步骤并安装 WordPress。

### <a name="connect-wordpress-to-redis"></a>将 WordPress 连接到 Redis

以管理员身份登录到 WordPress。在左侧导航栏中选择“插件”，然后选择“安装插件”。

![选择 WordPress 插件][2]

此处显示了所有插件

在插件页中，找到“Redis 对象缓存”并单击“激活”。

![激活 Redis][3]

单击“设置” 。

![单击“设置”][4]

单击“启用对象缓存”按钮。

![单击“启用对象缓存”按钮][5]

WordPress 将连接到 Redis 服务器。 同一页面上会显示连接**状态**。

![WordPress 将连接到 Redis 服务器。 同一页面上会显示连接**状态**。][6]

**祝贺你**，现已将 WordPress 连接到 Redis。 生产就绪的应用正在使用 **Azure Database for MySQL、持久性存储和 Redis**。 现在可以扩展应用服务计划以包含多个实例。

## <a name="use-a-kubernetes-configuration-optional"></a>使用 Kubernetes 配置（可选）

本部分介绍如何使用 Kubernetes 配置来部署多个容器。 请务必遵循前面的步骤创建[资源组](#create-a-resource-group)和[应用服务计划](#create-an-azure-app-service-plan)。 由于大多数步骤与 compose 部分中的步骤类似，因此，本部分已将配置文件合并。

### <a name="supported-kubernetes-options-for-multicontainer"></a>多容器支持的 Kubernetes 选项

* args
* command
* containers
* 图像
* 名称
* ports
* spec

> [!NOTE]
>未显式指明的任何其他 Kubernetes 选项在公共预览版中也不受支持。
>

### <a name="create-configuration-file"></a>创建配置文件

将以下 YAML 保存到名为 *kubernetes-wordpress.yml* 的文件。

[!code-yml[Main](../../../azure-app-service-multi-container/kubernetes-wordpress.yml)]

### <a name="create-an-azure-database-for-mysql-server"></a>创建 Azure Database for MySQL 服务器

使用 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 命令在 Azure Database for MySQL（预览版）中创建一个服务器。

在以下命令中，请将 &lt;mysql_server_name> 占位符替换为你自己的唯一 MySQL 服务器名称（有效字符是 `a-z`、`0-9` 和 `-`）。 此名称是 MySQL 服务器主机名 (`<mysql_server_name>.database.windows.net`) 的一部分，必须全局唯一。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

创建 MySQL 服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "southcentralus",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 命令创建 MySQL 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。
>

### <a name="create-the-wordpress-database"></a>创建 WordPress 数据库

如果尚未创建，请创建一个 [Azure Database for MySQL 服务器](#create-an-azure-database-for-mysql-server)。

```bash
az mysql db create --resource-group myResourceGroup --server-name <mysql_server_name> --name wordpress
```

创建数据库后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>在 WordPress 中配置数据库变量

若要将 WordPress 应用连接到这个新的 MySQL 服务器，请配置几个特定于 WordPress 的环境变量。 若要做出此更改，请在本地命令提示符终端中使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 应用设置区分大小写，用空格分开。

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WORDPRESS_DB_HOST="<mysql_server_name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql_server_name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

创建应用设置后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql_server_name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql_server_name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  }
]
```

### <a name="add-persistent-storage"></a>添加持久性存储

你的多容器应用现在正在用于容器的 Web 应用中运行。 重启后会擦除数据，因为文件不会持久保留。 在本部分，我们将持久性存储添加到 WordPress 容器。

### <a name="configure-environment-variables"></a>配置环境变量

若要使用持久性存储，请在应用服务中启用此设置。 若要做出此更改，请在本地命令提示符终端中使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令。 应用设置区分大小写，用空格分开。

```bash
az webapp config appsettings set --resource-group myResourceGroup --name <app_name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

创建应用设置后，Azure CLI 会显示类似于以下示例的信息：

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="create-a-multicontainer-app-kubernetes"></a>创建多容器应用 (Kubernetes)

在本地命令提示符终端中，使用 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 `myResourceGroup` 资源组和 `myAppServicePlan` 应用服务计划中创建多容器 [Web 应用](app-service-linux-intro.md)。 不要忘记将 _\<app_name>_ 替换为唯一的应用名称。

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type kube --multicontainer-config-file kubernetes-wordpress.yml
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的输出：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>浏览到应用

浏览到所部署的应用 (`http://<app_name>.azurewebsites.net`)。

该应用正在用于容器的 Web 应用中运行多个容器。

![用于容器的 Web 应用中的示例多容器应用][1]

**恭喜**，你已在用于容器的 Web 应用中创建了多容器应用。

若要使用 Redis，请遵循[将 WordPress 连接到 Redis](#connect-wordpress-to-redis) 中的步骤。

## <a name="find-docker-container-logs"></a>查找 Docker 容器日志

如果使用多个容器时遇到问题，可以通过浏览到 `https://<app_name>.scm.azurewebsites.net/api/logs/docker` 来访问容器日志。

将会看到类似于以下示例的输出：

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

查看每个容器的日志，以及父进程的附加日志。 将相应的 `href` 值复制到浏览器以查看日志。

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

本教程介绍了如何：
> [!div class="checklist"]
> * 转换 Docker Compose 配置以使用用于容器的 Web 应用
> * 转换 Kubernetes 配置以使用用于容器的 Web 应用
> * 将多容器应用部署到 Azure
> * 添加应用程序设置
> * 对容器使用持久性存储
> * 连接到 Azure Database for MySQL
> * 排查错误

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对用于容器的 Web 应用使用自定义 Docker 映像](tutorial-custom-docker-image.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
