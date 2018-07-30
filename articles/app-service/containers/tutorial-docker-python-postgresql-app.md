---
title: 在 Azure 应用服务中生成 Python 和 PostgreSQL Web 应用 | Microsoft Docs
description: 了解如何在 Azure 中运行可以连接到 PostgreSQL 数据库的数据驱动型 Python 应用。
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 20b549914daf71c0d23235b5c20ebb6f14367471
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172028"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用

用于容器的 Web 应用提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何创建一个数据驱动型 Python Web 应用，使用 PostgreSQL 作为数据库后端。 完成此步骤后，将拥有一个在 [Linux 应用服务](app-service-linux-intro.md)的 Docker 容器中运行的 Python Flask 应用程序。

![Linux 应用服务中的 Docker Python Flask 应用](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 Azure 中创建 PostgreSQL 数据库
> * 将 Python 应用连接到 PostgreSQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 在 Azure 门户中管理应用

可以在 macOS 中执行本文中的步骤。 Linux 和 Windows 指令在大多数情况下相同，本教程中并未详述差异。
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 Python](https://www.python.org/downloads/)
1. [安装并运行 PostgreSQL](https://www.postgresql.org/download/)
1. [安装 Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>测试本地 PostgreSQL 安装并创建数据库

在本地终端窗口中运行 `psql`，以便连接到本地 PostgreSQL 服务器。

```bash
sudo -u postgres psql
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，应确保按照[下载 - PostgreSQL Core Distribution](https://www.postgresql.org/download/)中的步骤启用本地 PostgresQL 数据库。

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

运行下列命令以克隆示例存储库并转到 0.1-initialapp 版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此示例存储库包含一个 [Flask](http://flask.pocoo.org/) 应用程序。 

### <a name="run-the-app-locally"></a>在本地运行应用

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

在浏览器中导航至 `http://localhost:5000` 。 单击“注册!” 创建一个测试用户。

![在本地运行的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask 示例应用程序在数据库中存储用户数据。 如果成功注册用户，应用将会把数据写入本地 PostgreSQL 数据库。

若要随时停止 Flask 服务器，请在终端中键入 Ctrl+C。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>创建 PostgreSQL 生产数据库

此步骤在 Azure 中创建一个 PostgreSQL 数据库。 应用部署到 Azure 后，它将使用该云数据库。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

在 Cloud Shell 中使用 [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) 命令创建 PostgreSQL 服务器。

在以下示例命令中，请将 *\<postgresql_name>* 替换为唯一的服务器名称，并将 *\<admin_username>* 和 *\<admin_password>* 替换为所需的用户凭据。 此服务器名称将用作 PostgreSQL 终结点 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。 用户凭据用于数据库管理员用户帐户。 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
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
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>为 PostgreSQL 服务器创建防火墙规则

在 Cloud Shell 中运行以下 Azure CLI 命令，以便从所有 IP 地址访问数据库。 若同时将起始 IP 和结束 IP 设置为 `0.0.0.0`，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。
>

在 Cloud Shell 中再次运行该命令（将 *\<you_ip_address>* 替换为[你的本地 IPv4 IP 地址](https://whatismyipaddress.com/)），以便从本地计算机访问数据库。 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>将 Python 应用连接到生产数据库

此步骤将 Flask 示例应用连接到已创建的 Azure Database for PostgreSQL 服务器。

### <a name="create-empty-database-and-user-access"></a>创建空数据库和用户访问权限

在 Cloud Shell 中通过运行 `psql` 连接到数据库。 系统提示输入管理员密码时，请使用在[创建 Azure Database for PostgreSQL 服务器](#create-an-azure-database-for-postgresql-server)中指定的密码。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

通过 PostgreSQL CLI 创建数据库和用户。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

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

![在本地运行的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>将应用上传到容器注册表

在此步骤中，请创建一个 Docker 映像并将其上传到 Azure 容器注册表。 也可使用常用的注册表，例如 Docker 中心。

### <a name="build-the-docker-image-and-test-it"></a>生成 Docker 映像并对其进行测试。

在本地终端窗口中生成 Docker 映像。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 会显示一条确认消息，指出已成功创建映像。

```bash
Successfully built 7548f983a36b
```

在存储库根目录中，添加名为 _db.env_ 的环境变量文件，然后向其添加以下数据库环境变量。 该应用连接到 PostgreSQL 生产数据库的 Azure 数据库。

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

在 Docker 容器中以本地方式运行映像。 下列命令指定环境变量文件，并将默认 Flask 端口 5000 映射到本地端口 5000。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

输出结果类似于你此前看到的结果。 但是，不再需要执行初始数据库迁移，因此已跳过此步骤。

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

数据库已包含前面创建的注册信息。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-docker.png)

验证容器可以在本地使用以后，请删除 _db.env_。 在 Azure 应用服务中，将使用应用设置来定义环境变量。  

### <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

在 Cloud Shell 中，请使用以下命令在 Azure 容器注册表中创建一个注册表。 将 *\<registry_name>* 替换为唯一的注册表名称。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>检索注册表凭据

在 Cloud Shell 中运行以下命令，以便检索注册表凭据。 需要这些凭据来推送和拉取映像。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

在输出中，会看到两个密码。 请记下用户名（默认为注册表名称）和第一个密码。

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-docker-image-to-registry"></a>将 Docker 映像上传到注册表

在本地终端窗口中，使用 `docker` 登录到新注册表。 出现提示时，提供检索的密码。

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

将 Docker 映像推送到注册表。

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>使用上传的映像创建 Web 应用

在此步骤中，请在 Azure 应用服务中创建一个应用，然后将其配置为使用 Azure 容器注册表中已上传的 Docker 映像。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 命令在 myAppServicePlan 应用服务计划中创建 Web 应用。

在下面的命令中，将 \<app_name> 占位符替换为唯一应用名称。 该名称是 Web 应用的默认 URL 的一部分，因此需要在 Azure 应用服务中的所有应用之间保持唯一性。

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

创建 Web 应用后，Azure CLI 会显示类似于以下示例的信息：

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

### <a name="configure-environment-variables"></a>配置环境变量

在本教程的前面部分，你已定义用于连接到 PostgreSQL 数据库的环境变量。

在应用服务中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令将环境变量设置为应用设置。

以下示例将数据库连接详细信息指定为应用设置。 它还使用针对容器端口 5000 的 *WEBSITES_PORT* 变量，以便容器接收端口 80 上的 HTTP 流量。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>配置自定义容器部署

即使已指定容器映像名称，也仍需指定自定义注册表 URL 和用户凭据。 在 Cloud Shell 中运行 [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) 命令。

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

在 Cloud Shell 中，重启该应用。 重启可确保应用所有设置，且从注册表中拉取最近的容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用 

浏览到已部署的 Web 应用。 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> Web 应用需要一些时间才能启动，因为在首次请求该应用时必须下载并运行容器。 如果一开始在经过很长时间后出现错误，只需刷新页面即可。

随后将会看到以前已注册的并在上一步骤中保存到 Azure 生产数据库的来宾。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**祝贺你！** 你正在用于容器的 Web 应用中运行 Python 应用。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

此步骤通过更新 `Guest` 模型将参与者数目添加到每个事件注册。

在本地终端窗口中，请使用以下 git 命令签出 0.2-migration 版本：

```bash
git checkout tags/0.2-migration
```

此版本已对模型、视图和控制器做出必要的更改。 此外，它还包含通过 *alembic* (`flask db migrate`) 生成的数据库迁移。 可通过以下 git 命令查看所有更改：

```bash
git diff 0.1-initialapp 0.2-migration
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

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在本地终端窗口中生成新的 Docker 映像并将其推送到注册表。

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

在 Cloud Shell 中重启应用，确保从注册表中拉取最新的容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

导航到 Azure Web 应用，再次试用新的功能。 创建另一个事件注册。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure 应用服务中的 Docker Python Flask 应用](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的 Web 应用。

从左侧菜单中单击“应用服务”，并单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/tutorial-docker-python-postgresql-app/app-resource.png)

默认情况下，门户将显示 Web 应用“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>后续步骤

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service-web-tutorial-custom-domain.md)
