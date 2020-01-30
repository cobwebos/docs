---
title: 教程：将 Linux Python 应用与 Postgre 配合使用
description: 了解如何在 Azure 应用服务中运行 Linux Python 应用，同时使其连接到 Azure 中的 PostgreSQL 数据库。 本教程使用 Django。
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: 3aa5b5085a6120ca513f0aeba344e7f541f0fd72
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713406"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>教程：在 Azure 应用服务中使用 PostgreSQL 运行 Python (Django) Web 应用

[Azure 应用服务](app-service-linux-intro.md)提供高度可缩放、自修复的 Web 托管服务。 本教程演示如何将数据驱动的 Python Django Web 应用连接到 Azure Database for PostgreSQL 数据库，并在 Azure 应用服务上部署和运行应用。

![Azure 应用服务中的 Python Django Web 应用](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for PostgreSQL 数据库，并将 Web 应用连接到该数据库
> * 将 Web 应用部署到 Azure 应用服务
> * 查看诊断日志
> * 在 Azure 门户中管理 Web 应用

可以在 macOS、Linux 或 Windows 中执行本文中的步骤。 大多数情况下的步骤是相似的，但本教程中不会对差异进行详细介绍。 下面的大多数示例都使用 Linux 上的 `bash` 终端窗口。 

## <a name="prerequisites"></a>必备条件

开始本教程前，请执行以下操作：

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- 安装 [Git](https://git-scm.com/)。
- 安装 [Python 3](https://www.python.org/downloads/)。
- 安装并运行 [PostgreSQL](https://www.postgresql.org/download/)。

## <a name="test-postgresql-installation-and-create-a-database"></a>测试 PostgreSQL 安装并创建数据库

首先，连接到本地 PostgreSQL 服务器并创建数据库： 

在本地终端窗口中运行 `psql`，以便作为内置 `postgres` 用户连接到本地 PostgreSQL 服务器。

```bash
sudo su - postgres
psql
```
或
```PowerShell
psql -U postgres
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，请确保按照[下载 - PostgreSQL Core Distribution](https://www.postgresql.org/download/) 中针对操作系统的说明启动本地 PostgresQL 数据库。

创建名为 pollsdb 的新数据库，并设置名为 manager 的数据库用户，将其密码设置为supersecretpass    ：

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>创建并运行本地 Python 应用

接下来，设置和运行示例 Python Django Web 应用。

[djangoapp](https://github.com/Azure-Samples/djangoapp) 示例存储库包含数据驱动的 [Django](https://www.djangoproject.com/) 轮询应用，该应用通过执行 Django 文档中[编写第一个 Django 应用](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)中的操作而获得。

### <a name="clone-the-sample-app"></a>克隆示例应用

在终端窗口中，运行以下命令，克隆示例应用存储库，并更改为新的工作目录：

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>配置 Python 虚拟环境

创建并激活 Python 虚拟环境以运行应用。

```bash
python3 -m venv venv
source venv/bin/activate
```
或
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

在 `venv` 环境中，运行 env.sh 或 env. ps1 来设置 azuresite/py 将用于数据库连接设置的环境变量    。

```bash
source ./env.sh
```
或
```PowerShell
.\env.ps1
```

从 requirements.txt 安装所需的包，运行 [Django 迁移](https://docs.djangoproject.com/en/2.1/topics/migrations/)，并[创建管理员用户](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)  ：

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>运行 Web 应用

创建管理员用户后，请运行 Django 服务器。

```bash
python manage.py runserver
```

当 Django Web 应用完全加载后，将返回以下类似消息：

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

在浏览器中转到 http:\//localhost:8000  。 应会看到消息“无可用轮询”  。 

转到 http:\//localhost:8000/admin，使用在上一步创建的管理员用户登录  。 选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题   。

![在应用程序服务中本地运行 Python Django 应用](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次转到 http:\//localhost:8000，以查看轮询问题并回答问题  。 本地 Django 示例应用程序会写入用户数据并将其存储到本地 PostgreSQL 数据库。

若要停止 Django 服务器，请在终端中键入 Ctrl+C。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

本文余下的大部分步骤都使用 Azure Cloud Shell 中的 Azure CLI 命令。 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>创建并连接到 Azure Database for PostgreSQL

本部分将创建一个 Azure Database for PostgreSQL 服务器和数据库，并将 Web 应用连接到该服务器和数据库。 将 Web 应用部署到 Azure 应用服务时，应用将使用此云数据库。 

### <a name="create-a-resource-group"></a>创建资源组

可以为 Azure Database for PostgreSQL 服务器创建新的资源组，也可使用现有资源组。 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

在 Cloud Shell 中使用 [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令创建 PostgreSQL 服务器。

> [!NOTE]
> 在创建 Azure Database for PostgreSQL 服务器之前，请检查你所在区域中可用的[计算代系](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)。 如果你所在区域不支持 Gen4 硬件，请将以下命令行中的 --sku-name 更改为你所在区域支持的值，例如 Gen5  。 

在以下命令中，将 \<postgresql-name> 替换为唯一服务器名称  。 此服务器名称是 PostgreSQL 终结点 https://\<postgresql-name>.postgres.database.azure.com 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一  。 

将 \<resourcegroup-name> 和 \<region> 替换为要使用的资源组的名称和区域   。 对于 \<admin-username> 和 \<admin-password>，请为数据库管理员帐户创建用户凭据   。 请记住 \<admin-username> 和 \<admin-password>，以便稍后用于登录 PostgreSQL 服务器和数据库   。

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

创建 Azure Database for PostgreSQL 后，Azure CLI 会显示类似于以下示例的 JSON 代码：

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器的防火墙规则

运行 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) 命令，以允许从 Azure 资源访问数据库。 将 \<postgresql-name> 和 \<resourcegroup-name> 占位符替换为自己的值   。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 上述设置允许从 Azure 网络中的所有 IP 地址进行网络连接。 进行生产性使用时，请尝试尽可能配置最严格的防火墙规则，即[允许只使用应用所使用的出站 IP 地址](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。

再次运行 `firewall-rule create` 命令，以允许来自本地计算机的访问。 将 \<your-ip-address> 替换为[本地 IPv4 IP 地址](https://www.whatsmyip.org/)  。 将 \<postgresql-name> 和 \<resourcegroup-name> 占位符替换为你自己的值   。

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>创建并连接到 Azure Database for PostgreSQL 数据库

运行以下命令以连接到 Azure Database for PostgreSQL 服务器。 使用你自己的 \<postgresql-name> 和 \<admin-username>，并通过创建的密码进行登录   。

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

就像在本地 PostgreSQL 服务器中进行的操作那样，在 Azure Database for PostgreSQL 服务器中创建数据库和用户：

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> 最佳做法是使用特定应用的受限权限而不是管理员用户来创建数据库用户。 `manager` 用户只具有 `pollsdb` 数据库的完整权限  。

键入 `\q` 退出 PostgreSQL 客户端。

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>测试与 Azure PostgreSQL 数据库的应用连接

将 \<postgresql-name> 替换为 Azure Database for PostgreSQL 服务器名称，编辑本地 env.sh 或 env. ps1 文件以指向云 PostgreSQL 数据库    。

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
或
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

在本地终端窗口的 `venv` 环境中，运行编辑过的 env.sh 或 env. ps1   。 
```bash
source ./env.sh
```
或
```PowerShell
.\env.ps1
```

运行目标为 Azure 数据库的 Django 迁移，并创建管理员用户。

```bash
python manage.py migrate
python manage.py createsuperuser
```

创建管理员用户以后，请运行 Django 服务器。

```bash
python manage.py runserver
```

在浏览器中，转到 http:\//localhost:8000，应再次看到消息“无可用轮询”   。 

转到 http:\//localhost:8000/admin，使用已创建的管理员用户登录，然后像之前一样创建轮询问题  。

![在应用程序服务中本地运行 Python Django 应用](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次转到 http:\//localhost:8000，此时会看到该轮询问题已显示  。 应用现正将数据写入 Azure Database for PostgreSQL 数据库。

若要停止 Django 服务器，请在终端中键入 Ctrl+C。

## <a name="deploy-the-web-app-to-azure-app-service"></a>将 Web 应用部署到 Azure 应用服务

此步骤将已连接 Azure Database for PostgreSQL 数据库的 Python 应用部署到 Azure 应用服务。

### <a name="configure-repository"></a>配置存储库

由于本教程使用的是 Django 示例，因此需要在 djangoapp/azuresite/settings.py 文件中更改并添加一些设置，以便与 Azure 应用服务配合使用  。 

1. Django 会验证传入请求中的 `HTTP_HOST` 标头。 若要在应用服务中运行 Django Web 应用，需要将应用的完全限定的域名添加到允许的主机中。 
   
   编辑 azuresite/settings.py 以更改 `ALLOWED_HOSTS` 行，如下所示  ：
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django 不支持[在生产中提供静态文件](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)。 对于本教程，请使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/) 来支持提供文件。 WhiteNoise 包已通过 requirements.txt 进行了安装  。 
   
   若要将 Django 配置为使用 WhiteNoise，请在 azuresite/py 中查找 `MIDDLEWARE` 设置，并将 `whitenoise.middleware.WhiteNoiseMiddleware` 添加到列表中，紧接 `django.middleware.security.SecurityMiddleware` 行之后  。 `MIDDLEWARE` 设置应该如下所示：
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. 在 azuresite/settings.py 末尾添加以下行  ：
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   有关配置 WhiteNoise 的详细信息，请参阅 [WhiteNoise 文档](https://whitenoise.evans.io/en/stable/)。

> [!IMPORTANT]
> 数据库设置部分已经遵循了有关如何使用环境变量的安全方面的最佳做法。 如需完整的部署建议，请参阅 [Django 文档：部署清单](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)。

将所做的更改提交到 djangoapp 存储库的分支中  ：

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>配置环境变量

在本教程的前面部分，你已定义用于连接到 PostgreSQL 数据库的环境变量。

在 Azure 应用服务中，使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令将环境变量设置为应用设置  。

在 Azure Cloud Shell 中运行以下命令，以将数据库连接详细信息指定为应用设置。 将 \<app-name>、\<resourcegroup-name> 以及 \<postgresql-name> 占位符替换为你自己的值    。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

有关代码如何访问这些应用设置的信息，请参阅[访问环境变量](how-to-configure-python.md#access-environment-variables)。

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

应用服务部署服务器会看到存储库根目录中的 *requirements.txt*，并且会在 `git push` 后自动运行 Python 包管理。

### <a name="browse-to-the-azure-app"></a>浏览到 Azure 应用

通过 URL http:\//\<app-name>.azurewebsites.net 浏览到已部署的应用  。 它需要一些时间才能启动，因为在首次请求该应用时必须下载并运行容器。 如果页面超时或显示错误消息，请等待数分钟，然后刷新页面。

应该会看到此前创建的轮询问题。 

应用服务会检测存储库中的 Django 项目，其方式是在每个由 `manage.py startproject` 默认创建的子目录中查找 wsgi.py 文件  。 应用服务找到该文件后，就会加载 Django Web 应用。 若要详细了解应用服务如何加载 Python 应用，请参阅[配置内置的 Python 映像](how-to-configure-python.md)。

转到 http:\//\<app-name>.azurewebsites.net/admin，使用创建的管理员用户登录  。 可以根据需要创建更多的轮询问题。

![在 Azure 的应用程序服务中运行 Python Django 应用](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

祝贺你！  你已在 Linux 的 Azure 应用服务中运行 Python (Django) Web 应用。

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 门户中管理应用

在 [Azure 门户](https://portal.azure.com)中，搜索并选择创建的应用。

![在 Azure 门户中导航到 Python Django 应用](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

默认情况下，门户将显示应用的  “概述”页。 在此页中可以查看应用的运行状况。 也可在此处执行基本的管理任务，例如浏览、停止、重启和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![在 Azure 门户的“概述”页中管理 Python Django 应用](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

转到下一篇教程，了解如何将自定义 DNS 名称映射到应用：

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../app-service-web-tutorial-custom-domain.md)

或者查看其他资源：

> [!div class="nextstepaction"]
> [配置 Python 应用](how-to-configure-python.md)
