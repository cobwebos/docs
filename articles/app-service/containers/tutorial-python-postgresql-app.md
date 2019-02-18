---
title: 在 Linux 上使用 PostgreSQL 生成 Python 应用 - Azure 应用服务 | Microsoft Docs
description: 了解如何在 Azure 中运行可以连接到 PostgreSQL 数据库的数据驱动型 Python 应用。
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: 161778c921026547c532b05474a60dd0d3e9fa7a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237794"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>在 Azure 应用服务中生成 Python 和 PostgreSQL 应用

[Linux 应用服务](app-service-linux-intro.md)提供高度可缩放、自修补的 Web 托管服务。 本教程介绍如何创建一个数据驱动型 Python 应用，使用 PostgreSQL 作为数据库后端。 完成后，会有一个在 Linux 上的应用服务中运行的 Django 应用程序。

![Linux 应用服务中的 Python Django 应用](./media/tutorial-python-postgresql-app/django-admin-azure.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 查看诊断日志
> * 在 Azure 门户中管理应用

> [!NOTE]
> 在创建 Azure Database for PostgreSQL 之前，请查看[此文](https://docs.microsoft.com/en-us/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores)，了解所在区域提供的计算代系。

可以在 macOS 中执行本文中的步骤。 Linux 和 Windows 指令在大多数情况下相同，本教程中并未详述差异。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
2. [安装 Python](https://www.python.org/downloads/)
3. [安装并运行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>测试本地 PostgreSQL 安装并创建数据库

在本地终端窗口中运行 `psql`，以便连接到本地 PostgreSQL 服务器。

```bash
sudo -u postgres psql postgres
```

如果收到类似于`unknown user: postgres`的错误消息，则可能是因为 PostgreSQL 安装是使用登录的用户名配置的。 改为尝试以下命令。

```bash
psql postgres
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，请确保按照[下载 - PostgreSQL Core Distribution](https://www.postgresql.org/download/) 中针对操作系统的说明启动本地 PostgresQL 数据库。

创建一个名为 pollsdb 的数据库，并设置一个名为 manager 的单独数据库用户，将其密码设置为supersecretpass。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

<a name="step2"></a>

## <a name="create-local-python-app"></a>创建本地 Python 应用

此步骤将设置本地 Python Django 项目。

### <a name="clone-the-sample-app"></a>克隆示例应用

打开终端窗口并 `CD` 到工作目录。

运行下列命令以克隆示例存储库。

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

此示例存储库包含一个 [Django](https://www.djangoproject.com/) 应用程序。 它是你通过 [Django 文档中的入门教程](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)获取的同一数据驱动型应用。 本教程不讲述 Django，而是介绍如何将 Django 应用（或其他数据驱动型 Python 用于）部署到应用服务，然后再运行它。

### <a name="configure-environment"></a>配置环境

创建 Python 虚拟环境，并使用脚本来设置数据库连接设置。

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

在 *env.sh* 和 *env.ps1* 中定义的环境变量可以在 _azuresite/settings.py_ 中用来定义数据库设置。

### <a name="run-app-locally"></a>在本地运行应用

安装所需的包，[运行 Django 迁移](https://docs.djangoproject.com/en/2.1/topics/migrations/)，然后[创建管理员用户](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)。

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

创建管理员用户以后，请运行 Django 服务器。

```bash
python manage.py runserver
```

当应用完全加载后，将看到以下类似消息：

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

在浏览器中导航至 `http://localhost:8000` 。 应该看到消息 `No polls are available.`。 

导航到 `http://localhost:8000/admin`，使用在上一步创建的管理员用户登录。 单击“问题”旁边的“添加”，创建一个包含一些选项的轮询问题。

![在本地运行的 Python Django 应用程序](./media/tutorial-python-postgresql-app/django-admin-local.png)

再次导航到 `http://localhost:8000`，此时会看到轮询问题已显示。

Django 示例应用程序在数据库中存储用户数据。 如果成功添加轮询问题，应用会将数据写入本地 PostgreSQL 数据库。

若要随时停止 Django 服务器，请在终端中键入 Ctrl+C。

## <a name="create-a-production-postgresql-database"></a>创建 PostgreSQL 生产数据库

此步骤在 Azure 中创建一个 PostgreSQL 数据库。 应用部署到 Azure 后，它将使用该云数据库。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

在 Cloud Shell 中使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) 命令创建 PostgreSQL 服务器。

在以下示例命令中，请将 *\<postgresql_name>* 替换为唯一的服务器名称，并将 *\<admin_username>* 和 *\<admin_password>* 替换为所需的用户凭据。 用户凭据用于数据库管理员帐户。 此服务器名称将用作 PostgreSQL 终结点 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

创建用于 PostgreSQL 的 Azure 数据库服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> 记下 \<admin_username> 和 \<admin_password>，供以后使用。 需要使用它们登录到 Postgre 服务器及其数据库。


### <a name="create-firewall-rules-for-the-postgresql-server"></a>为 PostgreSQL 服务器创建防火墙规则

在 Cloud Shell 中运行以下 Azure CLI 命令，以便从 Azure 资源访问数据库。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> 此设置允许从 Azure 网络中的所有 IP 进行网络连接。 进行生产性使用时，请尝试尽可能配置最严格的防火墙规则，即[只使用应用所使用的出站 IP 地址](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。

在 Cloud Shell 中再次运行该命令（将 *\<your_ip_address>* 替换为[你的本地 IPv4 IP 地址](http://www.whatsmyip.org/)），以便从本地计算机进行访问。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>将 Python 应用连接到生产数据库

此步骤将 Django 示例应用连接到已创建的 Azure Database for PostgreSQL 服务器。

### <a name="create-empty-database-and-user-access"></a>创建空数据库和用户访问权限

在 Cloud Shell 中通过运行以下命令连接到数据库。 系统提示输入管理员密码时，请使用在[创建 Azure Database for PostgreSQL 服务器](#create-an-azure-database-for-postgresql-server)中指定的密码。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

在 Azure Postgres 服务器中创建数据库和用户，就像在本地 Postgres 服务器中一样。

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

> [!NOTE]
> 最佳做法是使用特定应用程序的受限权限而不是管理员用户来创建数据库用户。 在此示例中，`manager` 用户只具有 `pollsdb` 数据库的完整权限。

### <a name="test-app-connectivity-to-production-database"></a>测试从应用到生产数据库的连接

在本地终端窗口中，更改数据库环境变量（此前已通过运行 *env.sh* 或 *env.ps1* 进行配置）：

```bash
# Bash
export DBHOST="<postgresql_name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql_name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql_name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql_name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
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

再次导航到 `http://localhost:8000`。 应该会再次看到消息 `No polls are available.`。 

导航到 `http://localhost:8000/admin`，使用已创建的管理员用户登录，然后像以前一样创建轮询问题。

![在本地运行的 Python Django 应用程序](./media/tutorial-python-postgresql-app/django-admin-local.png)

再次导航到 `http://localhost:8000`，此时会看到轮询问题已显示。 现在，应用正将数据写入 Azure 中的数据库。

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 Postgres 的 Python 应用程序部署到 Azure 应用服务。

### <a name="configure-repository"></a>配置存储库

Django 会验证传入请求中的 `HTTP_HOST` 标头。 若要在应用服务中运行 Django 应用，需要将应用的完全限定域名添加到允许的主机中。 打开 _azuresite/settings.py_，找到 `ALLOWED_HOSTS` 设置。 将行更改为：

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

接下来，由于 Django 不支持[在生产环境中处理静态文件](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/)，因此需手动完成相关的启用操作。 在本教程中，请使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/)。 WhiteNoise 包已经包括在 _requirements.txt_ 中。 只需将 Django 配置为使用它即可。 

在 _azuresite/settings.py_ 中找到 `MIDDLEWARE` 设置，将 `whitenoise.middleware.WhiteNoiseMiddleware` 中间件添加到列表的 `django.middleware.security.SecurityMiddleware` 中间件下方。 `MIDDLEWARE` 设置应该如下所示：

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

在 _azuresite/settings.py_ 末尾添加以下行。

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

有关如何配置 WhiteNoise 的详细信息，请参阅 [WhiteNoise 文档](https://whitenoise.evans.io/en/stable/)。

> [!IMPORTANT]
> 数据库设置部分已经遵循了有关如何使用环境变量的安全方面的最佳做法。 如需完整的部署建议，请参阅 [Django 文档：部署清单](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)。


将更改提交到存储库。

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>配置环境变量

在本教程的前面部分，你已定义用于连接到 PostgreSQL 数据库的环境变量。

在应用服务的 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 命令将环境变量设置为应用设置。

以下示例将数据库连接详细信息指定为应用设置。 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
   06b6df4..6520eea  master -> master
```  

应用服务部署服务器会看到存储库根目录中的 _requirements.txt_，并且会在 `git push` 后自动运行 Python 包管理。

### <a name="browse-to-the-azure-app"></a>浏览到 Azure 应用

浏览到已部署的应用。 它需要一些时间才能启动，因为在首次请求该应用时需下载并运行容器。 如果页面超时或显示错误消息，请等待数分钟，然后刷新页面。

```bash
http://<app_name>.azurewebsites.net
```

你应该会看到此前创建的轮询问题。 

应用服务会检测存储库中的 Django 项目，其方式是在每个由 `manage.py startproject` 默认创建的子目录中查找 _wsgi.py_。 找到该文件后，就会加载 Django 应用。 若要详细了解应用服务如何加载 Python 应用，请参阅[配置内置的 Python 映像](how-to-configure-python.md)。

导航到 `<app_name>.azurewebsites.net`，使用已创建的同一管理员用户登录。 可以根据需要尝试创建更多的轮询问题。

![在本地运行的 Python Django 应用程序](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**祝贺你！** 你已在 Linux 的应用服务中运行 Python 应用。

## <a name="access-diagnostic-logs"></a>访问诊断日志

在 Linux 上的应用服务中，应用在默认 Docker 映像的容器中运行。 可以访问在容器中生成的控制台日志。 若要获取日志，请先在 Cloud Shell 中运行以下命令，以便启用容器日志记录功能：

```azurecli-interactive
az webapp log config --name <app_name> --resource-group myResourceGroup --docker-container-logging filesystem
```

启用容器日志记录功能以后，请运行以下命令来查看日志流：

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 门户中管理应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的应用。

在左侧菜单中单击“应用服务”，然后单击 Azure 应用的名称。

![在门户中导航到 Azure 应用](./media/tutorial-python-postgresql-app/app-resource.png)

默认情况下，门户将显示应用的“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 查看诊断日志
> * 在 Azure 门户中管理应用

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](../app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [配置内置的 Python 映像并排查错误](how-to-configure-python.md)

