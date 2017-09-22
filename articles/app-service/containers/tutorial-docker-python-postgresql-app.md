---
title: "在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用 | Microsoft Docs"
description: "了解如何创建一个可在 Azure 中运行的 Docker Python 应用，并将其连接到 PostgreSQL 数据库。"
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
editor: 
ms.assetid: 2bada123-ef18-44e5-be71-e16323b20466
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 36cf3c0bb4a28a4ccfd5fc94b72fba023516a9ce
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用

Azure Web 应用提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何在 Azure 中创建一个基本的 Docker Python Web 应用。 将此应用连接到 PostgreSQL 数据库。 完成此步骤后，[将在 Azure 应用服务 Web 应用](../app-service-web-overview.md)上的 Docker 容器中运行 Python Flask 应用程序。

![Azure 应用服务中的 Docker Python Flask 应用](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

可在 macOS 上执行以下步骤。 Linux 和 Windows 指令在大多数情况下相同，本教程中并未详述差异。
 
## <a name="prerequisites"></a>先决条件

完成本教程：

1. [安装 Git](https://git-scm.com/)
1. [安装 Python](https://www.python.org/downloads/)
1. [安装并运行 PostgreSQL](https://www.postgresql.org/download/)
1. [安装 Docker Community Edition](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>测试本地 PostgreSQL 安装并创建数据库

打开终端窗口，并运行 `psql postgres` 连接到本地 PostgreSQL 服务器。

```bash
psql postgres
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，应确保按照[下载 - PostgreSQL Core Distribution](https://www.postgresql.org/download/)中的步骤启用本地 PostgresQL 数据库。

创建一个名为 eventregistration 的数据库，并设置一个名为 manager 的单独数据库用户，将其密码设置为supersecretpass。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
退出 PostgreSQL 客户端时，请键入 \q。 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>创建本地 Python Flask 应用程序

此步骤将设置本地 Python Flask 项目。

### <a name="clone-the-sample-application"></a>克隆示例应用程序

打开终端窗口并 `CD` 到工作目录。  

运行下列命令以克隆示例存储库并转到 0.1-initialapp 版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此示例存储库包含一个 [Flask](http://flask.pocoo.org/) 应用程序。 

### <a name="run-the-application"></a>运行应用程序

> [!NOTE] 
> 后面的步骤将会通过构建一个用于生产数据库的 Docker 容器来简化此过程。

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

在浏览器中导航至 `http://127.0.0.1:5000`。 单击“注册!” 创建一个测试用户。

![在本地运行的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask 示例应用程序在数据库中存储用户数据。 如果成功注册用户，应用将会把数据写入本地 PostgreSQL 数据库。

若要随时停止 Flask 服务器，请在终端中键入 Ctrl+C。 

## <a name="create-a-production-postgresql-database"></a>创建 PostgreSQL 生产数据库

此步骤在 Azure 中创建一个 PostgreSQL 数据库。 应用部署到 Azure 后，将会使用云数据库。

### <a name="log-in-to-azure"></a>登录 Azure

现在将使用 Azure CLI 2.0 创建在 Azure 应用服务中托管 Python 应用程序所需的资源。  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli
az login 
``` 
   
### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建[资源组](../../azure-resource-manager/resource-group-overview.md)。 

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

以下示例在美国西部区域创建一个资源组：

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

使用 [az appservice list-locations](/cli/azure/appservice#list-locations) Azure CLI 命令列出可用位置。

### <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器

使用 [az postgres server create](/cli/azure/documentdb#create) 命令创建 PostgreSQL 服务器。

在下列命令中，用唯一的服务器名称替换* \<postgresql_name >* 占位符，并用用户名替换* \<admin_username >* 占位符。 此服务器名称将用作 PostgreSQL 终结点 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。 此用户名用于初始数据库管理员用户帐户。 系统会提示选择此用户的密码。

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

创建用于 PostgreSQL 的 Azure 数据库服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "<my_admin_username>",
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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>为用于 PostgreSQL 服务器的 Azure 数据库创建防火墙规则

运行以下的 Azure CLI 命令，以允许从所有 IP 地址访问数据库。

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Azure CLI 使用类似以下示例的输出确认防火墙规则：

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>将 Python Flask 应用程序连接到数据库

此步骤将 Python Flask 示例应用程序连接到所创建的用于 PostgreSQL 的 Azure 数据库服务器。

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>创建一个空数据库并设置一个新的数据库应用程序用户

创建一个只有访问单一数据库权限的数据库用户。 通过使用这些凭据，可避免提供访问服务器的应用程序完整权限。

连接到数据库（系统会提示输入管理员密码）。

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

通过 PostgreSQL CLI 创建数据库和用户。

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

退出 PostgreSQL 客户端时，请键入 \q。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>在本地针对 Azure PostgreSQL 数据库测试应用程序 

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

在浏览器中导航至 http://127.0.0.1:5000。 单击“注册!” 并创建测试注册。 现在，正将数据写入 Azure 中的数据库。

![在本地运行的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>从 Docker 容器运行应用程序

生成 Docker 容器映像。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 会显示一条确认消息，指出已成功创建容器。

```bash
Successfully built 7548f983a36b
```

将数据库环境变量添加到环境变量文件 db.env 中。 应用将连接到 Azure 中的 PostgreSQL 生产数据库。

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

现在，从 Docker 容器内部运行此应用。 下列命令指定环境变量文件，并将默认 Flask 端口 5000 映射到本地端口 5000。

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

## <a name="upload-the-docker-container-to-a-container-registry"></a>将 Docker 容器上传到容器注册表

在这一步中，请将 Docker 容器上传到容器注册表。 你将使用 Azure 容器注册表，不过，也可以使用其他常用的注册表，例如 Docker Hub。

### <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

在以下用以创建容器注册表的命令中，使用所选的唯一的 Azure 容器注册表名称替换 * \<registry_name >*。

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

输出
```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>检索用于推送和提取 Docker 映像的注册表凭据

若要显示注册表凭据，请先启用管理员模式。

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

会看到两个密码。 请记下用户名和第一个密码。

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

### <a name="upload-your-docker-container-to-azure-container-registry"></a>将 Docker 容器上传到 Azure 容器注册表

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>将 Docker Python Flask 应用程序部署到 Azure

此步骤将基于 Docker 容器的 Python Flask 应用程序部署到 Azure 应用服务。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建应用服务计划。 

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

在以下示例中，使用 S1 定价层创建名为 myAppServicePlan 的基于 Linux 的应用服务计划：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

创建应用服务计划后，Azure CLI 会显示类似于以下示例的信息：

```json 
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
``` 

### <a name="create-a-web-app"></a>创建 Web 应用

使用 [az webapp create](/cli/azure/webapp#create) 命令在 myAppServicePlan 应用服务计划中创建 web 应用。 

该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 用于创建 Web 应用。 

在下面的命令中，将 \<app_name> 占位符替换为唯一应用名称。 该名称是 Web 应用的默认 URL 的一部分，因此需要在 Azure 应用服务中的所有应用之间保持唯一性。 

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
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

### <a name="configure-the-database-environment-variables"></a>配置数据库环境变量

在本教程的前面部分，你已定义用于连接到 PostgreSQL 数据库的环境变量。

在应用服务中，使用 [az webapp config appsettings update ](/cli/azure/webapp/config#set) 命令将环境变量设置为应用设置。 

以下示例将数据库连接详细信息指定为应用设置。 它还使用 PORT 变量，映射 Docker 容器中的 PORT 5000，以接收 PORT 80 上的 HTTP 流量。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>配置 Docker 容器部署 

AppService 可以自动下载并运行 Docker 容器。

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

每次更新 Docker 容器或更改设置，请重启应用。 重启可确保应用所有设置，且从注册表中拉取最近的容器。

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用 

使用 Web 浏览器浏览到已部署的 Web 应用。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Web 应用需要较长时间加载，因为容器配置更改后，必须下载并启动容器。

随后将会看到以前已注册的并在上一步骤中保存到 Azure 生产数据库的来宾。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**祝贺你！** 基于 Docker 容器的 Python Flask 应用正在 Azure 应用服务中运行。

## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

此步骤通过更新来宾模型将参与者数目添加到每个事件注册。

使用以下 git 命令签出0.2-migration 版本：

```bash
git checkout tags/0.2-migration
```

我们已对此版本的视图、控制器和模型做出必要的更改。 此外，它还包含通过 *alembic* (`flask db migrate`) 生成的数据库迁移。 可通过以下 git 命令查看所有更改：

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>在本地测试更改

运行以下命令，通过运行 flask 服务器在本地测试更改。

Mac/Linux：
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

在浏览器中导航到 http://127.0.0.1:5000 查看所做的更改。 创建测试注册。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

生成新的 docker 映像，将其推送到容器注册表，然后重新启动应用。

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
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

