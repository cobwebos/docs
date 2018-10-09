---
title: 在 Azure 应用服务中生成 Python 和 PostgreSQL Web 应用 | Microsoft Docs
description: 了解如何在 Azure 中运行可以连接到 PostgreSQL 数据库的数据驱动型 Python 应用。
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435704"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用

[Linux 应用服务](app-service-linux-intro.md)提供高度可缩放、自修补的 Web 托管服务。 本教程介绍如何创建一个数据驱动型 Python Web 应用，使用 PostgreSQL 作为数据库后端。 完成此步骤后，将拥有一个在 Linux 应用服务的 Docker 容器中运行的 Python Flask 应用程序。

![Linux 应用服务中的 Docker Python Flask 应用](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 查看诊断日志
> * 更新数据模型并重新部署应用
> * 在 Azure 门户中管理应用

可以在 macOS 中执行本文中的步骤。 Linux 和 Windows 指令在大多数情况下相同，本教程中并未详述差异。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 Python](https://www.python.org/downloads/)
1. [安装并运行 PostgreSQL](https://www.postgresql.org/download/)

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

创建一个名为 eventregistration 的数据库，并设置一个名为 manager 的单独数据库用户，将其密码设置为supersecretpass。

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

<a name="step2"></a>

## <a name="create-local-python-app"></a>创建本地 Python 应用

此步骤将设置本地 Python Flask 项目。

### <a name="clone-the-sample-app"></a>克隆示例应用

打开终端窗口并 `CD` 到工作目录。

运行下列命令以克隆示例存储库。

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

此示例存储库包含一个 [Flask](http://flask.pocoo.org/) 应用程序。

### <a name="run-the-app-locally"></a>在本地运行应用

安装所需的包，并启动应用程序。

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

当应用完全加载后，将看到以下类似消息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在浏览器中导航至 `http://localhost:5000` 。 单击“注册!” 创建一个测试用户。

![在本地运行的 Python Flask 应用程序](./media/tutorial-python-postgresql-app/local-app.png)

Flask 示例应用程序在数据库中存储用户数据。 如果成功注册用户，应用将会把数据写入本地 PostgreSQL 数据库。

若要随时停止 Flask 服务器，请在终端中键入 Ctrl+C。

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
> 此设置允许从 Azure 网络中的所有 IP 进行网络连接。 进行生产性使用时，请尝试尽可能配置最严格的防火墙规则，即[只使用应用所使用的出站 IP 地址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。

在 Cloud Shell 中再次运行该命令（将 *\<your_ip_address>* 替换为[你的本地 IPv4 IP 地址](http://www.whatsmyip.org/)），以便从本地计算机进行访问。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>将 Python 应用连接到生产数据库

此步骤将 Flask 示例应用连接到已创建的 Azure Database for PostgreSQL 服务器。

### <a name="create-empty-database-and-user-access"></a>创建空数据库和用户访问权限

在本地终端窗口中运行以下命令，以便连接到数据库。 系统提示输入管理员密码时，请使用在[创建 Azure Database for PostgreSQL 服务器](#create-an-azure-database-for-postgresql-server)中指定的密码。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

在 Azure Postgres 服务器中创建数据库和用户，就像在本地 Postgres 服务器中一样。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

> [!NOTE]
> 最佳做法是使用特定应用程序的受限权限而不是管理员用户来创建数据库用户。 在此示例中，`manager` 用户只具有 `eventregistration` 数据库的完整权限。

### <a name="test-app-connectivity-to-production-database"></a>测试从应用到生产数据库的连接

返回本地终端窗口，运行以下命令，以便运行 Flask 数据库迁移和 Flask 服务器。

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

当应用完全加载后，将看到以下类似消息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在浏览器中导航至 http://localhost:5000 。 单击“注册!” 并创建测试注册。 现在，正将数据写入 Azure 中的数据库。

![在本地运行的 Python Flask 应用程序](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 Postgres 的 Python 应用程序部署到 Azure 应用服务。

### <a name="configure-repository"></a>配置存储库

当存储库根目录中有 _application.py_ 时，应用服务中的 Git 部署引擎会调用 `pip` 自动化。 在本教程中，请让部署引擎为你运行自动化。 在本地终端窗口中，导航到存储库根目录，创建虚拟 _application.py_，然后提交所做的更改。

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
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
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 5, done. 
Delta compression using up to 4 threads. 
Compressing objects: 100% (5/5), done. 
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done. 
Total 5 (delta 3), reused 0 (delta 0) 
remote: Updating branch 'master'. 
remote: Updating submodules. 
remote: Preparing deployment for commit id '6c7c716eee'. 
remote: Running custom deployment command... 
remote: Running deployment command... 
remote: Handling node.js deployment. 
. 
. 
. 
remote: Deployment successful. 
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
 * [new branch]      master -> master 
```  

### <a name="configure-entry-point"></a>配置入口点

默认情况下，内置的映像会寻找根目录中的 _wsgi.py_ 或 _application.py_ 作为入口点，但你的入口点是 _app/app.py_。 此前添加的 _application.py_ 为空，不执行任何操作。

在 Cloud Shell 中运行 [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) 命令，以便设置自定义启动脚本。

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

`--startup-file` 参数使用自定义命令，或者使用自定义命令所在文件的路径。 自定义命令应该采用以下格式：

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

如果入口点不在根目录中，且 `<subdirectory>` 为子目录，则自定义命令 `--chdir` 是必需的。 `<module>` 是 _.py_ 文件的名称，`<variable>` 是代表 Web 应用的模块中的变量。

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

浏览到已部署的 Web 应用。 它需要一些时间才能启动，因为在首次请求该应用时需下载并运行容器。 如果页面超时或显示错误消息，请等待数分钟，然后刷新页面。

```bash
http://<app_name>.azurewebsites.net
```

随后将会看到以前已注册的并在上一步骤中保存到 Azure 生产数据库的来宾。

![在 Azure 中运行的 Python Flask 应用程序](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**祝贺你！** 你已在 Linux 的应用服务中运行 Python 应用。

## <a name="access-diagnostic-logs"></a>访问诊断日志

由于 Python 应用在容器中运行，因此可以使用 Linux 上的应用服务访问在容器中生成的控制台日志。 若要查找这些日志，请导航到此 URL：

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

此时会看到两个 JSON 对象，每个对象有一个 `href` 属性。 一个 `href` 指向 Docker 控制台日志（以 `_docker.log` 结尾），另一个 `href` 指向在 Python 容器中生成的控制台日志。 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

将所需的 `href` 值复制到浏览器窗口中，以便导航到这些日志。 日志不进行流式传输，因此你可能会体验到一些延迟。 若要查看新日志，请刷新浏览器页面。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

在此步骤中，请通过更新 `Guest` 模型将参与者数目添加到每个事件注册，然后将更新重新部署到 Azure。

在本地终端窗口中，请使用以下 git 命令从 `modelChange` 分库签出文件：

```bash
git checkout origin/modelChange -- .
```

此签出已对模型、视图和控制器做出必要的更改。 此外，它还包含通过 *alembic* (`flask db migrate`) 生成的数据库迁移。 可通过以下 git 命令查看所有更改：

```bash
git diff master origin/modelChange
```

### <a name="test-your-changes-locally"></a>在本地测试更改

在本地终端窗口中运行以下命令，通过运行 Flask 服务器在本地测试所做的更改。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在浏览器中导航到 http://localhost:5000 查看更改。 创建测试注册。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在本地终端窗口中，提交在 Git 中所做的所有更改，然后将代码更改推送到 Azure。

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

导航到 Azure Web 应用，再次试用新的功能。 确保刷新页面。

```bash
http://<app_name>.azurewebsites.net
```

![Azure 应用服务中的 Docker Python Flask 应用](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>在 Azure 门户中管理 Web 应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的 Web 应用。

从左侧菜单中单击“应用服务”，并单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/tutorial-python-postgresql-app/app-resource.png)

默认情况下，门户将显示 Web 应用“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 查看诊断日志
> * 更新数据模型并重新部署应用
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [配置内置的 Python 映像](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service-web-tutorial-custom-domain.md)

