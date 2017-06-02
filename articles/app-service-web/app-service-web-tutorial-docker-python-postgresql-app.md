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
ms.topic: article
ms.date: 05/03/2017
ms.author: beverst
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: ca086f76e50cb27f012bb2e7f05595be5fdcb241
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017

---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>在 Azure 中构建 Docker Python 和 PostgreSQL Web 应用
本教程介绍如何在 Azure 中创建一个基本的 Docker Python Web 应用。 此外，介绍如何将此应用连接到 PostgreSQL 数据库。 完成本教程后，[Azure 应用服务 Web 应用](app-service-web-overview.md)上的 Docker 容器中将会运行一个 Python Flask 应用程序。

![Azure 应用服务中的 Docker Python Flask 应用](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="before-you-begin"></a>开始之前

运行本示例之前，请在本地安装以下必备组件：

1. [下载并安装 git](https://git-scm.com/)
1. [下载并安装 Python](https://www.python.org/downloads/)
1. [下载、安装并运行 PostgreSQL](https://www.postgresql.org/download/)
1. [下载并安装 Docker Community Edition](https://www.docker.com/community-edition)
1. [下载和安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="test-local-postgresql-installation-and-create-a-database"></a>测试本地 PostgreSQL 安装并创建数据库
执行此步骤时，请确保本地 PostgreSQL 数据库正在运行。

打开终端窗口，然后运行 `psql postgres` 连接到本地 PostgreSQL 服务器。

```bash
psql postgres
```

如果连接成功，则表示 PostgreSQL 数据库已在运行。 否则，请确保遵循[下载、安装并运行 PostgreSQL](https://www.postgresql.org/download/) 中的步骤来启动本地 PostgreSQL 数据库。

创建名为 `eventregistration` 的数据库，并设置名为 `manager`、密码为 `supersecretpass` 的独立数据库用户。

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

将终端窗口和 `CD` 打开到工作目录。  

运行以下命令来克隆示例存储库，然后转到 `0.1-initialapp` 版本。

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.gi
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

此示例存储库包含一个 [Flask](http://http://flask.pocoo.org/) 应用程序。 

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
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

当应用完全加载后，应看见类似下方所示的消息：

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在浏览器中导航至 `http://127.0.0.1:5000`。 单击“注册!” 并尝试创建一个虚构的注册。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

Flask 示例应用程序在数据库中存储用户数据。 如果前面的操作已成功并且能够在应用中查看注册，则表示应用正在向本地 PostgreSQL 数据库写入数据。

若要随时停止 Flask 服务器，请在终端中键入 `Ctrl`+`C`。 

## <a name="create-a-production-postgresql-database"></a>创建 PostgreSQL 生产数据库

此步骤在 Azure 中创建一个 PostgreSQL 数据库。 将应用部署到 Azure 后，我们将指定此数据库作为它的生产工作负荷。

### <a name="log-in-to-azure"></a>登录 Azure

接下来，我们将在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure 应用服务 中托管 Python 应用程序。  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在美国西部区域创建一个资源组：

```azurecli
az group create --name myResourceGroup --location "West US"
```

若要查看适用于 `--location` 的可能值，请使用 `az appservice list-locations` Azure CLI 命令。

### <a name="create-an-azure-database-for-postgresql-server"></a>创建用于 PostgreSQL 的 Azure 数据库服务器

使用 [az postgres server create](/cli/azure/documentdb#create) 命令创建 PostgreSQL 服务器。

在以下命令中，请将 `<postgresql_name>` 占位符替换为你自己的唯一 PostgreSQL 服务器名称。 此唯一名称将用作 PostgreSQL 终结点 (`https://<postgresql_name>.postgres.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。 

```azurecli
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <my_admin_username>
```

需要使用 `--admin-user` 创建初始数据库管理员用户帐户。 系统将提示你选择此用户的密码。

创建用于 PostgreSQL 的 Azure 数据库服务器后，Azure CLI 将显示类似于以下示例的信息：

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

### <a name="creating-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>为用于 PostgreSQL 的 Azure 数据库服务器创建防火墙规则

在访问该数据库之前，必须允许从所有 IP 地址访问它。 为此，可运行以下 Azure CLI 命令：

```azurecli
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

创建防火墙后，Azure CLI 将确认规则的存在状态，如下所示：

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

### <a name="creating-an-empty-database-and-setting-up-a-new-database-application-user"></a>创建空数据库并设置新的数据库应用程序用户

我们将创建一个只有权访问单一数据库的新数据库用户。 执行此步骤可以避免通过管理员凭据向应用程序授予对服务器的完全访问权限。

连接到数据库（系统会提示输入管理员密码）。
```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

然后通过 PostgreSQL CLI 创建数据库和用户。
```
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

键入 `\q` 退出 PostgreSQL 客户端。

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>在本地针对 Azure PostgreSQL 数据库测试应用程序 

返回到克隆的 Github 存储库的 `app` 文件夹，只需更新数据库环境变量，即可运行我们的 Python Flask 应用程序。

```bash
FLASK_APP=app.py;DBHOST="<postgresql_name>.postgres.database.azure.com";DBUSER="manager@<postgresql_name>";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

完全加载应用后，会再次出现类似于下面的消息：

```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

在浏览器中导航至 `http://127.0.0.1:5000`。 单击“注册!” 并尝试创建一个虚构的注册。 现在，正在将数据写入 Azure 中的生产数据库。

![在本地运行的 Python Flask 应用程序](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>从 Docker 容器运行应用程序

现在，我们构建 Docker 容器映像，并在本地从 Docker 容器内部运行应用程序，同时仍与 Azure 中的 PostgreSQL 生产数据库保持连接。

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker 将显示一条确认消息，指出已成功创建容器。

```
Successfully built 7548f983a36b
```

让我们在环境变量文件 `db.env` 中添加数据库环境变量。

```
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

现在，从 Docker 容器内部运行应用。 指定环境变量文件，并将默认 Flask 端口 5000 映射到本地端口 5000。

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

输出与之前类似，这并不让人意外。 但是，不再需要执行初始数据库迁移，因此已跳过此步骤。
```
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
 ```

 数据库已包含前面创建的注册。

 ![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/app-service-web-tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>将 Docker 容器上传到容器注册表
此步骤将创建的 Docker 容器上传到容器注册表。 我们将使用 Azure 容器注册表，但是，你也可以使用其他常用的服务，例如 Docker 中心。

### <a name="create-an-azure-container-registry"></a>创建 Azure 容器注册表

在以下用于创建容器注册表的命令中，请将 `<registry_name>` 替换为所选的唯一 Azure 容器注册表名称。

```azurecli
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

必须先启用管理模式，然后才能访问凭据。

```azurecli
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

将显示两个密码。 请记下显示的用户名和第一个密码。
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

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

以下示例使用 S1 定价层创建名为 `myAppServicePlan` 的基于 Linux 的应用服务计划。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

创建应用服务计划后，Azure CLI 将显示类似于以下示例的信息：

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

创建应用服务计划后，请在 `myAppServicePlan` 应用服务计划中创建 Web 应用。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 

在下列命令中，请用自己的唯一应用名称替换 `<app_name>` 占位符。 该唯一名称将用作 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息： 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

### <a name="configure-the-database-environment-variables"></a>配置数据库环境变量

在本教程的前面部分，你已手动定义用于连接到 PostgreSQL 数据库的环境变量。

在应用服务中，使用 [az appservice web config appsettings update](/cli/azure/appservice/web/config/appsettings#update) 命令将环境变量设置为_应用设置_。 

使用以下命令可将数据库连接详细信息指定为应用设置。 此外，我们使用了 `PORT` 变量来指定要映射 Docker 容器中的端口 5000，以便在端口 80 上接收 HTTP 流量。

```azurecli
az appservice web config appsettings update --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>配置 Docker 容器部署 

AppService 可以自动下载并运行 Docker 容器。

使用 [az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 命令创建帐户级别的凭据。 

```azurecli
az appservice web config container update --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

每当更新 Docker 容器或更改上述设置后，请重新启动应用，确保已应用所有设置，并从注册表中提取了最新的容器。

```azurecli
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用 
使用 Web 浏览器浏览到已部署的 Web 应用。 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> 更改容器配置后首次访问 Web 应用时，请再等待一段时间来下载并启动容器。

随后将会看到以前已注册的并在上一步骤中保存到 Azure 生产数据库的来宾。

 ![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**祝贺你！** 你的基于 Docker 容器的 Python Flask 应用正在 Azure 应用服务中运行。


## <a name="update-data-model-and-redeploy"></a>更新数据模型和重新部署

此步骤通过更新来宾模型将参与者数目添加到每个事件注册。

使用以下 git 命令签出 `0.2-migration` 版本。
```bash
git checkout tags/0.2-migration
```

我们已对此版本的视图、控制器和模型做出必要的更改。 此外，它还包含通过 *alembic* (`flask db migrate`) 生成的数据库迁移。 可通过以下 git 命令查看所有更改。

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>在本地测试更改

运行以下命令，通过运行 flask 服务器在本地测试更改。

Mac/Linux：
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py;DBHOST="localhost";DBUSER="manager";DBNAME="eventregistration";DBPASS="supersecretpass";flask db upgrade;flask run
```

在浏览器中导航到 `http://127.0.0.1:5000` 查看更改。 创建新的虚构注册。

![在本地运行的基于 Docker 容器的 Python Flask 应用程序](./media/app-service-web-tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

生成新的 docker 映像，将其推送到容器注册表，然后重新启动应用。

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

导航到你的 Azure Web 应用，然后再次试用新的功能。 创建另一个事件注册。

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure 应用服务中的 Docker Python Flask 应用](./media/app-service-web-tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 Azure 门户查看已创建的 Web 应用。

为此，请登录到 [https://portal.azure.com](https://portal.azure.com)。

从左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-docker-python-postgresql-app/app-resource.png)

现已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务边栏选项卡](./media/app-service-web-tutorial-docker-python-postgresql-app/app-mgmt.png)

边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证


