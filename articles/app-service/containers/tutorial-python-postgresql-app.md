---
title: 教程：使用 Postgres 部署 Python (Django)
description: 了解如何创建使用 PostgreSQL 数据库的 Python 应用并将其部署到 Linux 上的 Azure 应用服务。 本教程使用 Django 示例应用进行演示。
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 504e2f7c07d8d29e4fe4dad52dc008c895517a3d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82609776"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>教程：在 Azure 应用服务中部署使用 PostgreSQL 的 Python (Django) Web 应用

本教程介绍如何将数据驱动的 Python (Django) Web 应用部署到 [Azure 应用服务](app-service-linux-intro.md)，并将其连接到 Azure Database for PostgreSQL 数据库。 应用服务提供高度可缩放的、可自我修补的 Web 托管服务。

![将 Python Django Web 应用部署到 Azure 应用服务](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure Database for PostgreSQL 数据库
> * 将代码部署到 Azure 应用服务并连接到 Postgres
> * 更新代码并重新部署
> * 查看诊断日志
> * 在 Azure 门户中管理 Web 应用

可以在 macOS、Linux 或 Windows 中执行本文中的步骤。

## <a name="install-dependencies"></a>安装依赖项

开始本教程前，请执行以下操作：

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- 安装 [Azure CLI](/cli/azure/install-azure-cli)。
- 安装 [Git](https://git-scm.com/)。
- 安装 [Python 3](https://www.python.org/downloads/)。

## <a name="clone-the-sample-app"></a>克隆示例应用

在终端窗口中，运行以下命令以克隆示例应用存储库，然后切换到存储库的根目录：

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

djangoapp 示例存储库包含数据驱动的 [Django](https://www.djangoproject.com/) 投票应用，该应用是根据 Django 文档的[编写第一个 Django 应用](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)中的说明创建的。 为方便你参考，本文提供了该应用。

## <a name="prepare-app-for-app-service"></a>为应用服务准备应用

与许多 Python Web 框架一样，Django [需要进行某些更改才能在生产服务器中运行](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)，在应用服务中也同样如此。 需要在默认的 azuresite/settings.py 文件中更改并添加一些设置，以便该应用在部署到应用服务后能够正常运行。  

查看 azuresite/production.py，其中为应用服务提供了所需的配置。  简单而言，该文件执行以下操作：

- 继承 azuresite/settings.py 中的所有设置。 
- 将应用服务应用的完全限定域名添加到允许的主机。 
- 使用 [WhiteNoise](https://whitenoise.evans.io/en/stable/) 来启用为生产环境中的静态文件提供服务，因为 Django 默认不会为生产环境中的静态文件提供服务。 WhiteNoise 包已经包括在 *requirements.txt* 中。
- 添加 PostgreSQL 数据库的配置。 Django 默认使用 Sqlite3 作为数据库，但该数据库不适合用于生产应用。 requirements.txt 中已包含 [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) 包。 
- Postgres 配置使用环境变量。 稍后本文将介绍如何在应用服务中设置环境变量。

为方便起见，存储库中包含了 azuresite/production.py，但应用目前不使用该文件。  为确保在应用服务中使用该文件的设置，需要配置两个文件 manage.py 和 azuresite/wsgi.py 来访问该文件。  

- 在 manage.py 中，将以下行 

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    更改为以下代码：

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    稍后在配置应用服务应用时，将设置环境变量 `DJANGO_ENV`。

- 在 azuresite/wsgi.py 中，按上面所示做出相同的更改。 

    在应用服务中，你将使用 manage.py 运行数据库迁移，而应用服务将使用 azuresite/wsgi.py 在生产环境中运行 Django 应用。   在这两个文件中进行此项更改可确保在这两种情况下使用生产设置。

## <a name="sign-in-to-azure-cli"></a>登录 Azure CLI

你应已安装了 Azure CLI。 [Azure CLI](/cli/azure/what-is-azure-cli) 可让你从命令行终端处理 Azure 资源。 

若要登录到 Azure，请运行 [`az login`](/cli/azure/reference-index#az-login) 命令：

```azurecli
az login
```

按照终端中的说明登录到 Azure 帐户。 完成后，终端输出中将以 JSON 格式显示你的订阅。

## <a name="create-postgres-database-in-azure"></a>在 Azure 中创建 Postgres 数据库

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

在本部分，你将创建 Azure Database for PostgreSQL 服务器和数据库。 若要开始，请使用以下命令安装 `db-up` 扩展：

```azurecli
az extension add --name db-up
```

如以下示例所示，使用 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 命令在 Azure 中创建 Postgres 数据库。 请将 \<postgresql-name> 替换为唯一名称（服务器终结点为 https://\<postgresql-name>.postgres.database.azure.com）。    对于 \<admin-username> 和 \<admin-password>，请指定用来为此 Postgres 服务器创建管理员用户的凭据。  

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

此命令可能会运行较长时间，因为它要执行以下操作：

- 创建名为 `myResourceGroup` 的[资源组](../../azure-resource-manager/management/overview.md#terminology)（如果不存在）。 每个 Azure 资源都需要位于某个资源组中。 `--resource-group` 是可选项。
- 创建带有管理用户的 Postgres 服务器。
- 创建 `pollsdb` 数据库。
- 允许从本地 IP 地址进行访问。
- 允许从 Azure 服务进行访问。
- 创建有权访问 `pollsdb` 数据库的数据库用户。

可以使用其他 `az postgres` 命令和 `psql` 单独执行每个步骤，但 `az postgres up` 可以通过一个步骤完成所有这些操作。

命令完成后，找到以 `Ran Database Query:` 开头的输出行。 这些行显示了系统为你创建的数据库用户（用户名为 `root`，密码为 `Pollsdb1`）。 稍后你要使用这些凭据将应用连接到数据库。

<!-- not all locations support az postgres up -->
> [!TIP]
> `--location <location-name>` 可以设置为任一个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 可以使用 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 命令获取可供你的订阅使用的区域。 对于生产应用，请将数据库和应用放置在同一位置。

## <a name="deploy-the-app-service-app"></a>部署应用服务应用

在本部分，你将创建应用服务应用。 然后，将此应用连接到已创建的 Postgres 数据库并部署代码。

### <a name="create-the-app-service-app"></a>创建应用服务应用

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

请确保返回到存储库根目录 (`djangoapp`)，因为应用将从此目录部署。

如以下示例所示，使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令创建应用服务应用。 请将 \<app-name> 替换为唯一名称（服务器终结点为 https://\<app-name>.azurewebsites.net）。    \<app-name> 允许的字符为 `A`-`Z`、`0`-`9` 和 `-`。 

```azurecli
az webapp up --plan myAppServicePlan --location westus2 --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

此命令可能会运行较长时间，因为它要执行以下操作：

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- 自动生成[资源组](../../azure-resource-manager/management/overview.md#terminology)。
- 在基本定价层 (B1) 中创建[应用服务计划](../overview-hosting-plans.md) myAppServicePlan（如果不存在）。  `--plan` 和 `--sku` 都是可选的。
- 创建应用服务应用（如果不存在）。
- 为应用启用默认日志记录（如果尚未启用）。
- 在启用了生成自动化的情况下，使用 ZIP 部署上传存储库。

部署完成后，将会看到如下所示的 JSON 输出：

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

复制 \<app-resource-group> 的值。  稍后需要使用该值来配置应用。 

> [!TIP]
> 相关设置会保存到存储库中隐藏的 .azure  目录中。 稍后可以使用简单命令来重新部署任何更改，并使用以下命令立即启用诊断日志：
> 
> ```azurecli
> az webapp up
> ```

现已部署示例代码，但该应用尚未连接到 Azure 中的 Postgres 数据库。 接下来将执行此操作。

### <a name="configure-environment-variables"></a>配置环境变量

在本地运行应用时，可以在终端会话中设置环境变量。 在应用服务中，可以使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令通过应用设置来实现此目的。 

运行以下命令，以将数据库连接详细信息指定为应用设置。 请将 \<app-name>、\<app-resource-group> 和 \<postgresql-name> 替换为自己的值。    请记住，`az postgres up` 已经为你创建了用户凭据 `root` 和 `Pollsdb1`。

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

有关代码如何访问这些应用设置的信息，请参阅[访问环境变量](how-to-configure-python.md#access-environment-variables)。

### <a name="run-database-migrations"></a>运行数据库迁移

若要在应用服务中运行数据库迁移，请在浏览器中导航到 https://>\<appname>.azurewebsites.net/webssh/host 来打开 SSH 会话： 

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

在 SSH 会话中运行以下命令：

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>转到 Azure 应用

在浏览器中使用 URL http:\//\<app-name>.azurewebsites.net 浏览到已部署的应用。  应会看到消息“无可用轮询”  。 

浏览到 http:\//\<app-name>.azurewebsites.net/admin，并使用在上一步骤中创建的管理员用户登录。  选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题   。

使用 URL http:\//\<app-name>.azurewebsites.net/admin 浏览到部署的应用，并创建一些投票问题。  可以在 http:\//\<app-name>.azurewebsites.net/ 中查看问题。  

![在 Azure 的应用程序服务中运行 Python Django 应用](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

再次使用 URL http:\//\<app-name>.azurewebsites.net/admin 浏览到部署的应用，以查看投票问题并回答问题。 

应用服务会检测存储库中的 Django 项目，其方式是在每个由 `manage.py startproject` 默认创建的子目录中查找 wsgi.py 文件  。 应用服务找到该文件后，就会加载 Django Web 应用。 若要详细了解应用服务如何加载 Python 应用，请参阅[配置内置的 Python 映像](how-to-configure-python.md)。

祝贺你！  你已在 Linux 的 Azure 应用服务中运行 Python (Django) Web 应用。

## <a name="develop-app-locally-and-redeploy"></a>在本地开发应用并重新部署

在本部分，你将在本地环境中开发应用，并将代码重新部署到应用服务。

### <a name="set-up-locally-and-run"></a>在本地进行设置并运行

若要设置本地开发环境并首次运行示例应用，请运行以下命令：

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

当 Django Web 应用完全加载后，将返回以下类似消息：

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

在浏览器中转到 http:\//localhost:8000  。 应会看到消息“无可用轮询”  。 

转到 http:\//localhost:8000/admin，使用在上一步创建的管理员用户登录  。 选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题   。

![在应用程序服务中本地运行 Python Django 应用](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

再次转到 http:\//localhost:8000，以查看轮询问题并回答问题  。 本地 Django 示例应用程序会在本地 Sqlite3 数据库中写入和存储用户数据，因此你无需担心生产数据库变得杂乱。 若要使开发环境与 Azure 环境相匹配，请考虑在本地使用 Postgres 数据库。

若要停止 Django 服务器，请按 Ctrl+C。

### <a name="update-the-app"></a>更新应用

如果只想了解应用更新的工作原理，请在 `polls/models.py` 中做出少量更改。 查找行：

<pre>
choice_text = models.CharField(max_length=200)
</pre>

将其更改为：

```python
choice_text = models.CharField(max_length=100)
```

更改数据模型后，需要创建新的 Django 迁移。 使用以下命令执行此操作：

```
python manage.py makemigrations
```

可以在本地测试更改，方法是运行迁移，运行开发服务器，然后导航到 http:\//localhost:8000/admin： 

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>将代码重新部署到 Azure

若要重新部署更改，请从存储库根目录运行以下命令：

```azurecli
az webapp up
```

应用服务将检测该应用是否存在，并只部署代码。

### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新运行迁移

由于对数据模型进行了更改，因此需要在应用服务中重新运行数据库迁移。 在浏览器中导航到 https://\<app-name>.scm.azurewebsites.net/webssh/host 来打开 SSH 会话。  运行以下命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>在生产环境中查看应用

浏览到 http:\//\<app-name>.azurewebsites.net，并查看生产环境中实时运行的更改。  

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

可以访问在容器中生成的控制台日志。

> [!TIP]
> `az webapp up` 将为你启用默认日志记录。 出于性能原因，此日志记录在一段时间后会自动禁用，但每次重新运行 `az webapp up` 时，它又会重新启用。 若要手动启用此日志记录，请运行以下命令：
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

运行以下 Azure CLI 命令查看日志流：

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

> [!NOTE]
> 也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 门户中管理应用

在 [Azure 门户](https://portal.azure.com)中，搜索并选择创建的应用。

![在 Azure 门户中导航到 Python Django 应用](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

默认情况下，门户将显示应用的  “概述”页。 在此页中可以查看应用的运行状况。 也可在此处执行基本的管理任务，例如浏览、停止、重启和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![在 Azure 门户的“概述”页中管理 Python Django 应用](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>清理资源

如果你认为将来不再需要这些资源，请运行以下命令删除资源组：

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 创建 Azure Database for PostgreSQL 数据库
> * 将代码部署到 Azure 应用服务并连接到 Postgres
> * 更新代码并重新部署
> * 查看诊断日志
> * 在 Azure 门户中管理 Web 应用

转到下一篇教程，了解如何将自定义 DNS 名称映射到应用：

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../app-service-web-tutorial-custom-domain.md)

或者查看其他资源：

> [!div class="nextstepaction"]
> [配置 Python 应用](how-to-configure-python.md)
