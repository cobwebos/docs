---
title: 在 Azure 中生成 Python 和 PostgreSQL Web 应用 | Microsoft Docs
description: 了解如何创建一个可在 Azure 中运行的 Python 应用，并将其连接到 PostgreSQL 数据库。
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 49ec67d06446d6c48e45aef90e2bd528a1b541a9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203504"
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>教程：在 Azure 中生成 Python 和 PostgreSQL Web 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 Linux 的应用服务，请参阅[在 Azure 中生成 Docker Python 和 PostgreSQL Web 应用](./containers/tutorial-docker-python-postgresql-app.md)。
>

[Azure 应用服务](app-service-web-overview.md)提供高度可缩放、自修补的 Web 托管服务。 本教程介绍如何在 Azure 中创建一个基本的 Python Web 应用。 将此应用连接到 PostgreSQL 数据库。 完成后，会有一个在应用服务中运行的 Python Flask 应用程序。

![Linux 应用服务中的 Python Flask 应用](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 在 Azure 门户中管理应用

可以在 macOS 中执行本教程中的步骤。 Linux 和 Windows 指令在大多数情况下相同，本教程中并未详述差异。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 Python](https://www.python.org/downloads/)
1. [安装并运行 PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>测试本地 PostgreSQL 安装并创建数据库

打开终端窗口，并运行 `psql` 连接到本地 PostgreSQL 服务器。

```bash
sudo -u postgres psql
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，应确保按照[下载 - PostgreSQL Core Distribution](https://www.postgresql.org/download/)中的步骤启用本地 PostgresQL 数据库。

创建一个名为 eventregistration 的数据库，并设置一个名为 manager 的单独数据库用户，将其密码设置为supersecretpass。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
键入 `\q` 退出 PostgreSQL 客户端。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>创建本地 Python Flask 应用程序

此步骤将设置本地 Python Flask 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

打开终端窗口并 `CD` 到工作目录。

运行以下命令来克隆示例存储库，然后恢复到初始应用的提交状态（`modelChange` 之前的状态）。

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

此示例存储库包含一个 [Flask](http://flask.pocoo.org/) 应用程序。 

### <a name="run-the-application"></a>运行应用程序

安装所需的包，并启动应用程序。

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

当应用完全加载后，将看到以下类似消息：

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在浏览器中导航至 `http://localhost:5000`。 单击“注册!” 创建一个测试用户。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Flask 示例应用程序在数据库中存储用户数据。 如果成功注册用户，应用将会把数据写入本地 PostgreSQL 数据库。

若要随时停止 Flask 服务器，请在终端中键入 Ctrl+C。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>在 Azure 中创建 PostgreSQL

此步骤在 Azure 中创建一个 PostgreSQL 数据库。 应用部署到 Azure 后，它将使用该云数据库。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>创建 PostgreSQL 服务器

使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) 命令创建 PostgreSQL 服务器。

在下列命令中，用唯一的服务器名称替换 *\<postgresql_name>* 占位符，用用户名替换 *\<admin_username>* 占位符，并用密码替换 *\<admin_password>* 占位符。 此服务器名称将用作 PostgreSQL 终结点 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

创建用于 PostgreSQL 的 Azure 数据库服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "location": "westeurope",
  "name": "<postgresql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

运行以下的 Azure CLI 命令，以允许从所有 IP 地址访问数据库。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

Azure CLI 使用类似以下示例的输出确认防火墙规则：

```json
{
  "additionalProperties": {},
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
 "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](app-service-ip-addresses.md#find-outbound-ips)。
>

### <a name="create-a-production-database-and-user"></a>创建生产数据库和用户

创建一个只有访问单一数据库权限的数据库用户。 通过使用这些凭据，可避免提供访问服务器的应用程序完整权限。

连接到数据库（系统会提示输入管理员密码）。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <admin_username>@<postgresql_name> postgres
```

通过 PostgreSQL CLI 创建数据库和用户。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

### <a name="test-app-locally-with-azure-postgresql"></a>使用 Azure PostgreSQL 在本地测试应用

返回到克隆 Github 存储库的应用文件夹，可通过更新数据库环境变量来运行 Python Flask 应用程序。

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

在浏览器中导航至 http://localhost:5000。 单击“注册!” 并创建测试注册。 现在，正将数据写入 Azure 中的数据库。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 PostgreSQL 的 Python 应用程序部署到 Azure 应用服务。

Git 存储库已包含在应用服务中运行 Flask Web 应用所需的下述文件：

- `.deployment`：指定要运行的自定义部署脚本。
- `deploy.cmd`：部署脚本， 是运行 `pip install` 的地方。
- `web.config`：指定可在 IIS 的 `httpPlatformHandler` 中运行的入口点脚本。
- `run_waitress_server.py`：入口点脚本， 可在 [`waitress`](https://docs.pylonsproject.org/projects/waitress) 服务器中启动 Flask Web 应用。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>安装 Python

此步骤在应用服务中安装带[站点扩展](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)的 Python 3.6.2。 将使用在[配置部署用户](#configure-a-deployment-user)中配置的凭据，针对 REST 终结点进行身份验证。

在 Cloud Shell 中运行下一命令，获取 Python 3.6.2 包信息。 将 *\<deployment_user>* 替换为已配置的部署用户名，将 *\<app_name>* 替换为应用名称。 出现提示时，请使用已配置的部署密码。

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

在 Cloud Shell 中运行下一命令，安装 Python 包。 将 *\<deployment_user>* 替换为已配置的部署用户名，将 *\<app_name>* 替换为应用名称。 出现提示时，请使用已配置的部署密码。

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

在命令输出中，可以看到 Python 安装在 `D:\home\python362x86\python.exe` 路径。

### <a name="configure-database-settings"></a>配置数据库设置

在本教程的前面部分，你已定义用于连接到 PostgreSQL 数据库的环境变量。

在应用服务中，使用 [az webapp config appsettings update ](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令将环境变量设置为应用设置。

以下示例将数据库连接详细信息指定为应用设置。 将 *\<app_name>* 替换为应用名称，将 *\<postgresql_name>* 替换为 PostgreSQL 服务器名称。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用 

使用 Web 浏览器浏览到已部署的 Web 应用。 

```bash 
http://<app_name>.azurewebsites.net 
```

随后将会看到以前已注册的并在上一步骤中保存到 Azure 生产数据库的来宾。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**祝贺你！** 你已在 Azure 应用服务中运行 Python Flask 应用。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

此步骤通过更新 `Guest` 模型将参与者数目添加到每个事件注册。

签出 `modelChange` 提交标记的文件：

```bash
git checkout modelChange -- *
```

我们已对此版本的视图、控制器和模型做出必要的更改。 此外，它还包含通过 *alembic* (`flask db migrate`) 生成的数据库迁移。 可以在 [GitHub 提交视图](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e)中查看对所有文件进行的更改。

### <a name="test-your-changes-locally"></a>在本地测试更改

运行以下命令，通过运行 Flask 服务器在本地测试所做的更改。

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在浏览器中导航到 http://localhost:5000 查看更改。 创建测试注册。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在本地终端窗口中，提交在 Git 中所做的所有更改，然后将代码更改推送到 Azure。

```bash
git add .
git commit -m "updated data model"
git push azure master
```

导航到 Azure Web 应用，再次试用新的功能。 创建另一个事件注册。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure 应用服务中的 Python Flask 应用](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的 Web 应用。

从左侧菜单中单击“应用服务”，并单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

默认情况下，门户将显示 Web 应用“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)
