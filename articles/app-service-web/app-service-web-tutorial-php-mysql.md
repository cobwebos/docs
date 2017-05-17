---
title: "在 Azure 中构建 PHP 和 MySQL Web 应用 | Microsoft Docs"
description: "了解如何创建一个可在 Azure 中运行的 PHP 应用，并将其连接到 MySQL 数据库。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/05/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: ae0b63bc338cb3e96eae4593b96265aafbcbc029
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="build-a-php-and-mysql-web-app-in-azure"></a>在 Azure 中构建 PHP 和 MySQL Web 应用
本教程介绍如何在 Azure 中创建 PHP Web 应用，并将其连接到 MySQL 数据库。 完成本教程后，[Azure 应用服务 Web 应用](app-service-web-overview.md)中将会运行一个 [Laravel](https://laravel.com/) 应用程序。

![在 Azure 应用服务中运行的 PHP 应用](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

本教程介绍如何：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 PHP 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

## <a name="prerequisites"></a>先决条件

运行本示例之前，请在本地安装以下必备组件：

1. [下载并安装 git](https://git-scm.com/)
1. [下载并安装 PHP 5.6.4 或更高版本](http://php.net/downloads.php)
1. [下载并安装 Composer](https://getcomposer.org/doc/00-intro.md)
1. 启用 Laravel 所需的以下 PHP 扩展：OpenSSL、PDO-MySQL、Mbstring、Tokenizer、XML
1. [下载、安装并启动 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [下载和安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql"></a>准备本地 MySQL

此步骤在本地 MySQL 服务器供中创建一个数据库，以便在本教程中使用。

### <a name="connect-to-mysql-server"></a>连接到 MySQL 服务器
在终端窗口中连接到你的本地 MySQL 服务器。

```bash
mysql -u root -p
```

当系统提示输入密码时，请输入 `root` 帐户的密码。 如果你不记得自己的 root 帐户密码，请参阅 [MySQL：如何重置 Root 密码](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果命令成功运行，则表示你的 MySQL 服务器已在运行。 否则，请确保遵循 [MySQL 安装后步骤](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)启动本地 MySQL 服务器。

### <a name="create-a-database"></a>创建数据库

在 `mysql` 提示中创建数据库。

```sql
CREATE DATABASE sampledb;
```

键入 `quit` 退出服务器连接。

```sql
quit
```

<a name="step2"></a>

## <a name="create-local-php-app"></a>创建本地 PHP 应用
此步骤创建一个 Laravel 示例应用程序、配置其数据库连接，然后在本地运行该应用程序。 

### <a name="clone-the-sample"></a>克隆示例

将终端窗口和 `cd` 打开到工作目录。  

运行下列命令以克隆示例存储库。 

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

运行 `cd` 切换到克隆的目录，然后安装所需的包。

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>配置 MySQL 连接

在存储库根目录中创建一个 _.env_ 文件，并在其中复制以下变量。 请将 _&lt;root_password>_ 占位符替换为 root 用户的密码。

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

> [!NOTE]
> 有关 Laravel 如何使用此 _.env_ 文件的信息，请参阅 [Laravel 环境配置](https://laravel.com/docs/5.4/configuration#environment-configuration)。
>
>

### <a name="run-the-sample"></a>运行示例

运行 [Laravel 数据库迁移](https://laravel.com/docs/5.4/migrations)，创建应用程序所需的表。 若要查看迁移中创建了哪些表，请查看 Git 存储库中的 _database/migrations_ 目录。

```bash
php artisan migrate
```

生成新的 Laravel 应用程序密钥。

```bash
php artisan key:generate 
```

运行应用程序。

```bash
php artisan serve
```

在浏览器中导航至 `http://localhost:8000`。 在页面中添加一些任务。

![PHP 已成功连接到 MySQL](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

在终端键入 `Ctrl`+`C` 可随时停止 PHP。 

## <a name="create-production-mysql-in-azure"></a>在 Azure 中创建生产 MySQL

此步骤在[用于 MySQL 的 Azure 数据库（预览版）](/azure/mysql)中创建一个 MySQL 数据库。 稍后需要将 PHP 应用程序配置为连接到此数据库。

### <a name="log-in-to-azure"></a>登录 Azure

现在，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 应用服务中托管 PHP 应用程序。 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在北欧区域创建资源组：

```azurecli
az group create --name myResourceGroup --location "North Europe"
```

若要查看可对 `--location` 使用的可能值，请使用 [az appservice list-locations](/cli/azure/appservice#list-locations) 命令。

### <a name="create-a-mysql-server"></a>创建 MySQL 服务器

使用 [az mysql server create](/cli/azure/mysql/server#create) 命令在用于 MySQL 的 Azure 数据库（预览版）中创建一个服务器。

在以下命令中，请将 _&lt;mysql_server_name>_ 占位符替换为你自己的唯一 MySQL 服务器名称。 此名称是 MySQL 服务器主机名 `<mysql_server_name>.database.windows.net` 的一部分，因此必须全局唯一。 同样，请将 _&lt;admin_user>_ 和 _&lt;admin_password>_ 替换为你自己的值。

```azurecli
az mysql server create \
    --name <mysql_server_name> \
    --resource-group myResourceGroup \
    --location "North Europe" \
    --user <admin_user> \
    --password <admin_password>
```

创建 MySQL 服务器后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "administratorLogin": "<admin_user>",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

使用 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 命令创建 MySQL 服务器的防火墙规则，以便能够建立客户端连接。 

```azurecli
az mysql server firewall-rule create \
    --name allIPs \
    --server <mysql_server_name> \
    --resource-group myResourceGroup \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 255.255.255.255
```

> [!NOTE]
> 用于 MySQL 的 Azure 数据库（预览版）尚不支持仅通过 Azure 服务建立连接。 由于 Azure 中的 IP 地址是动态分配的，因此最好是现在就启用所有 IP 地址。 该服务目前以预览版提供，我们即将推出保护数据库的更好方法。
>
>

### <a name="connect-to-production-mysql-server"></a>连接到生产 MySQL 服务器

在终端窗口中，连接到 Azure 中的 MySQL 服务器。 为 _&lt;admin_user>_ 和 _&lt;mysql_server_name>_ 使用前面指定的值。

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.database.windows.net -P 3306 -p
```

### <a name="create-a-production-database"></a>创建生产数据库

在 `mysql` 提示中创建数据库。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>创建具有权限的用户

创建一个数据库用户并向其授予 `sampledb` 数据库中的所有特权。 将占位符 _&lt;phpapp_user>_ 和 _&lt;phpapp_password>_ 替换为你自己的唯一应用名称。

```sql
CREATE USER '<phpapp_user>' IDENTIFIED BY '<phpapp_password>'; 
GRANT ALL PRIVILEGES ON sampledb.* TO '<phpapp_user>';
```

键入 `quit` 退出服务器连接。

```sql
quit
```

## <a name="connect-app-to-production-mysql"></a>将应用连接到生产 MySQL

此步骤将 PHP 应用程序连接到刚刚在用于 MySQL 的 Azure 数据库（预览版）中创建的 MySQL 数据库。 

<a name="devconfig"></a>
### <a name="configure-the-connection"></a>配置连接 

在存储库根目录中创建一个 _.env.production_ 文件，并在其中复制以下变量。 替换占位符 _&lt;mysql_server_name>_、_&lt;phpapp_user>_ 和 _&lt;phpapp_password>_。

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=SomeRandomString

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.database.windows.net
DB_DATABASE=sampledb
DB_USERNAME=<phpapp_user>@<mysql_server_name>
DB_PASSWORD=<phpapp_password>
```

保存所做更改。

### <a name="test-the-application"></a>测试应用程序

使用 _.env.production_ 作为环境文件运行 Laravel 数据库迁移，在用于 MySQL 的 Azure 数据库（预览版）中的 MySQL 数据库内创建表。

```bash
php artisan migrate --env=production --force
```

_.env.production_ 目前还不包含有效的应用程序密钥。 请在终端中为它生成一个新密钥。 

```bash
php artisan key:generate --env=production --force
```

使用 _.env.production_ 作为环境文件运行示例应用程序。

```bash
php artisan serve --env=production
```

在浏览器中导航至 `http://localhost:8000`。 如果页面可加载且未出错，则表示 PHP 应用程序正在连接到 Azure 中的 MySQL 数据库。 

在页面中添加一些任务。

![PHP 已成功连接到用于 MySQL 的 Azure 数据库（预览版）](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

在终端键入 `Ctrl`+`C` 可随时停止 PHP。 

### <a name="secure-sensitive-data"></a>保护敏感数据

需确保不要将 _.env.production_ 中的敏感数据提交到 Git。

为此，请打开存储库根目录中的 _.gitignore_，并在新的代码行中添加文件名：

```
.env.production
```

保存更改，然后将更改提交到 Git。

```bash
git add .gitignore
git commit -m "keep sensitive data out of git"
```

以后，你可以了解如何将应用服务中的环境变量配置为连接到用于 MySQL 的 Azure 数据库（预览版）中的数据库，这样就不需要在应用服务中创建任何 `.env` 文件。 

## <a name="deploy-php-app-to-azure"></a>将 PHP 应用部署到 Azure
此步骤将已连接 MySQL 的 PHP 应用程序部署到 Azure 应用服务。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建应用服务计划。 

> [!NOTE] 
> 应用服务计划表示用于托管应用的物理资源集合。 分配到应用服务计划的所有应用程序将共享该计划定义的资源，在托管多个应用时可以节省成本。 
> 
> 应用服务计划定义： 
> 
> * 区域（北欧、美国东部、东南亚） 
> * 实例大小（小、中、大） 
> * 规模计数（一个、两个、三个实例，等等） 
> * SKU（免费、共享、基本、标准、高级） 
> 

以下示例使用“免费”定价层创建名为 _myAppServicePlan_ 的应用服务计划：

```azurecli
az appservice plan create \
    --name myAppServicePlan \
    --resource-group myResourceGroup \
    --sku FREE
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息：

```json 
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-a-web-app"></a>创建 Web 应用

创建应用服务计划后，请在 _myAppServicePlan_ 应用服务计划中创建 Web 应用。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 

在以下命令中，请将 _&lt;appname>_ 占位符替换为你自己的唯一应用名称。 该唯一名称用作 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。 

```azurecli
az appservice web create \
    --name <app_name> \
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息： 

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
  ...
  < Output has been truncated for readability >
}
```

### <a name="set-the-php-version"></a>设置 PHP 版本

使用 [az appservice web config update](/cli/azure/appservice/web/config#update) 命令设置应用程序所需的 PHP 版本。

以下命令将 PHP 版本设置为 _7.0_。

```azurecli
az appservice web config update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --php-version 7.0
```

### <a name="configure-database-settings"></a>配置数据库设置

如前所述，可以使用应用服务中的环境变量连接到 Azure MySQL 数据库。

在应用服务中，使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令将环境变量设置为_应用设置_。 

使用以下命令可以配置应用设置 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME` 和 `DB_PASSWORD`。 替换占位符 _&lt;appname>_、_&lt;mysql_server_name>_、_&lt;phpapp_user>_ 和 _&lt;phpapp_password>_。

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings DB_HOST="<mysql_server_name>.database.windows.net" DB_DATABASE="sampledb" DB_USERNAME="<phpapp_user>@<mysql_server_name>" DB_PASSWORD="<phpapp_password>"
```

可以使用 PHP [getenv()](http://www.php.net/manual/function.getenv.php) 方法访问这些设置。 Laravel 代码使用 [env()](https://laravel.com/docs/5.4/helpers#method-env) 包装，而不是 PHP `getenv()`。 例如，_config/database.php_ 中的 MySQL 配置如下所示：

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>配置 Laravel 环境变量

如同在本地计算机上一样，Laravel 需要应用服务中的应用程序密钥。 也可以使用应用设置来配置该密钥。

使用 `php artisan` 生成新的应用程序密钥，但不要将它保存到 _.env_。

```bash
php artisan key:generate --show
```

使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令在应用服务 Web 应用中设置应用程序密钥。 替换占位符 _&lt;appname>_ 和 _&lt;outputofphpartisankey:generate>_。

```azurecli
az appservice web config appsettings update \
    --name <app_name> \
    --resource-group myResourceGroup \
    --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

> [!NOTE]
> 如果已部署的 Web 应用遇到错误，`APP_DEBUG="true"` 将告知 Laravel 返回调试信息。 运行生产应用程序时，应将其设置为 `false` 以提高安全性。
>
>

### <a name="set-the-virtual-application-path"></a>设置虚拟应用程序路径

设置 Web 应用的虚拟应用程序路径。 只需执行此步骤，因为 [Laravel 应用程序生命周期](https://laravel.com/docs/5.4/lifecycle)在 _public_ 目录中开始，而不是在应用程序的根目录中开始。 无需手动配置虚拟应用程序路径，生命周期在根目录中开始的其他 PHP 框架也能正常工作。

使用 [az resource update](/cli/azure/resource#update) 命令设置虚拟应用程序路径。 替换 _&lt;appname>_ 占位符。

```bash
az resource update \
    --name web \
    --resource-group myResourceGroup \
    --namespace Microsoft.Web \
    --resource-type config \
    --parent sites/<app_name> \
    --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" \
    --api-version 2015-06-01
```

> [!NOTE]
> 默认情况下，Azure 应用服务将根虚拟应用程序路径 (_/_) 指向已部署的应用程序的文件的根目录 (_sites\wwwroot_)。 
>
>

### <a name="configure-a-deployment-user"></a>配置部署用户

对于 FTP 和本地 Git 部署，需在服务器上配置一个部署用户，用于对部署进行身份验证。 

> [!NOTE] 
> 在 Web 应用中进行 FTP 和本地 Git 部署时需要一个部署用户。 用户名和密码是帐户级别的凭据，因此不同于 Azure 订阅凭据。

如果之前已创建部署用户名和密码，可使用以下命令来显示用户名：

```azurecli
az appservice web deployment user show
```

如果尚未创建部署用户，可运行 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令来创建部署凭据。 

```azurecli
az appservice web deployment user set \
    --user-name <username> \
    --password <minimum-8-char-capital-lowercase-number>
```

用户名必须唯一，密码必须是强密码。 如果出现 `'Conflict'. Details: 409` 错误，请更改用户名。 如果出现 `'Bad Request'. Details: 400` 错误，请使用更强的密码。

只需创建此部署用户一次；可对所有 Azure 部署使用此用户。

请记下用户名和密码，因为稍后在部署应用程序时需要用到。

### <a name="configure-local-git-deployment"></a>配置本地 Git 部署 

可以通过不同的方法将应用程序部署到 Azure 应用服务，包括 FTP、本地 Git、 GitHub、Visual Studio Team Services 和 BitBucket。 

使用 [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git) 命令配置对 Azure Web 应用的本地 Git 访问。 

```azurecli
az appservice web source-control config-local-git \
    --name <app_name> \
    --resource-group myResourceGroup
```

配置部署用户后，Azure CLI 将显示 Azure Web 应用的部署 URL，格式如下所示：

```bash 
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git 
``` 

复制终端的输出，因为下一步骤将要用到。 

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

将 Azure 远程功能添加到本地 Git 存储库。 

```bash
git remote add azure <paste_copied_url_here> 
```

推送到 Azure 远程功能以部署 PHP 应用程序。 系统将提示你输入前面在创建部署用户期间提供的密码。 

```bash
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
``` 

> [!NOTE]
> 你可能会发现，部署过程在即将结束时会安装 [Composer](https://getcomposer.org/) 包。 应用服务在默认部署期间不会运行这些自动化任务，因此该示例存储库的根目录中提供了两个附加的文件用于运行这些任务： 
>
> - `.deployment` - 此文件告知应用服务以自定义部署脚本运行 `bash deploy.sh`。
> - `deploy.sh` - 自定义部署脚本。 查看该文件时可以看到，它会先运行 `npm install`，再运行 `php composer.phar install`。 
> - `composer.phar` - Composer 包管理器。
>
> 可使用此方法将任何步骤添加到应用服务中的基于 Git 的部署。 有关详细信息，请参阅[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。
>
>

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

浏览到 `http://<app_name>.azurewebsites.net` 并在列表中添加一些任务。 

![在 Azure 应用服务中运行的 PHP 应用](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

**祝贺你！** 你的数据驱动的 PHP 应用正在 Azure 应用服务中运行。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

在此步骤中，将对 `task` 数据模型进行一些更改，并将其发布至 Azure。

对于任务方案，需要修改应用程序，以便能够将任务标记为已完成。 

### <a name="add-a-column"></a>添加列

在终端中，确保在 Git 存储库的根目录中操作，然后为 `tasks` 表生成新的数据库迁移。

```bash
php artisan make:migration add_complete_column --table=tasks
```

此命令显示已生成的迁移文件的名称。 在 _database/migrations_ 中找到此文件，然后在文本编辑器中打开它。

将 up()方法替换为以下代码：

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

此代码在名为 `complete` 的 `tasks` 表中添加一个布尔值列。

请将 Down() 方法替换为以下回滚操作代码：

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

在终端中本地运行 Laravel 数据库迁移，以便在本地数据库中进行更改。

```bash
php artisan migrate
```

根据 [Laravel 命名约定](https://laravel.com/docs/5.4/eloquent#defining-models)，模型 `Task`（请参阅 _app/Task.php_）默认映射到 `tasks` 表，因此，更新数据模型的过程现已完成。

### <a name="update-application-logic"></a>更新应用程序逻辑

打开 _routes/web.php_。 示例应用程序在此处定义其路由和业务逻辑。

在文件末尾，添加包含以下代码的路由：

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

此代码通过切换 `complete` 的值对数据模型进行简单的更新。

### <a name="update-the-view"></a>更新视图

打开 _resources/views/tasks.blade.php_。 找到 `<tr>` 开始标记并将其替换为：

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

这会根据任务是否已完成来更改行的颜色。

在下一行中包含以下代码：

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

请将整行替换为以下代码：

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

此代码添加引用前面定义的路由的提交按钮。

### <a name="test-your-changes-locally"></a>在本地测试更改

在 Git 存储库的根目录中再次运行开发服务器。

```bash
php artisan serve
```

在浏览器中导航到 `http://localhost:8000`，然后单击复选框查看任务状态相应发生的变化。

![将复选框添加到任务](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在终端中，使用生产连接字符串运行 Laravel 数据库迁移，在 Azure 中的生产数据库中进行更改。

```bash
php artisan migrate --env=production --force
```

在 Git 中提交所有更改，然后将代码更改推送到 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` 完成后，请再次导航到 Azure Web 应用，然后试用新功能。

![发布到 Azure 的模型和数据库更改](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

> [!NOTE]
> 如果之前添加过任何任务，现在仍能看到它们。 更新数据架构不会改变现有数据。
>
>

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志 

当 PHP 应用程序在 Azure 应用服务中运行时，你可以将控制台日志直接传输到终端。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

若要启动日志流式处理，请使用 [az appservice web log tail](/cli/azure/appservice/web/log#tail) 命令。

```azurecli 
az appservice web log tail \
    --name <app_name> \
    --resource-group myResourceGroup 
``` 

一旦启动日志流式处理，请在浏览器中刷新 Azure Web 应用，以获取一些 Web 流量。 现在应能看到控制台日志传送到终端。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。 

> [!TIP]
> PHP 应用程序可以使用标准 [error_log()](http://php.net/manual/function.error-log.php) 输出到控制台。 示例应用程序在 _app/Http/routes.php_ 中使用此方法。
>
> 作为一种 Web 框架，[Laravel 使用 Monolog](https://laravel.com/docs/5.4/errors) 作为日志记录提供程序。 若要了解如何使用 Monolog 将消息输出到控制台，请参阅 [PHP：如何使用 monolog 记录到控制台 (php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)。
>
>

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 Azure 门户查看已创建的 Web 应用。

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-php-mysql/access-portal.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务边栏选项卡](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

## <a name="clean-up-resources"></a>清理资源
 
如果不需要将这些资源用于其他教程（请参阅[后续步骤](#next)），可通过运行以下命令将其删除： 
  
```azurecli 
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 PHP 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向它映射自定义 DNS 名称。

> [!div class="nextstepaction"] 
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)

