---
title: 教程：使用 Postgre 部署 Python Django 应用
description: 创建使用 PostgreSQL 数据库的 Python Web 应用并将其部署到 Azure。 本教程使用 Django 框架，应用托管在 Linux 上的 Azure 应用服务上。
ms.devlang: python
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: a630387a41b6def67141a423249c3347ff034e2e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369614"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>教程：在 Azure 应用服务中部署使用 PostgreSQL 的 Django Web 应用

本教程介绍如何将数据驱动的 Python [Django](https://www.djangoproject.com/) Web 应用部署到 [Azure 应用服务](overview.md)，并将其连接到 Azure Database for Postgres 数据库。 应用服务提供高度可缩放的、可自我修补的 Web 托管服务。

在本教程中，你将使用 Azure CLI 完成以下任务：

> [!div class="checklist"]
> * 使用 Python 和 Azure CLI 设置初始环境
> * 创建 Azure Database for PostgreSQL 数据库
> * 将代码部署到 Azure 应用服务并连接到 PostgreSQL
> * 更新代码并重新部署
> * 查看诊断日志
> * 在 Azure 门户中管理 Web 应用

你还可以使用[本教程的 Azure 门户版本](/azure/developer/python/tutorial-python-postgresql-app-portal)。


## <a name="set-up-your-initial-environment"></a>设置初始环境

1. 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
1. 安装 <a href="https://www.python.org/downloads/" target="_blank">Python 3.6 或更高版本</a>。
1. 安装 <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 或更高版本，使用它可以在任何 shell 中运行命令来预配和配置 Azure 资源。

打开终端窗口并检查 Python 版本是否为 3.6 或更高版本：

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

检查 Azure CLI 版本是否为 2.0.80 或更高版本：

```azurecli
az --version
```

然后通过 CLI 登录到 Azure：

```azurecli
az login
```

此命令将打开浏览器以获取凭据。 命令完成后，会显示包含订阅相关信息的 JSON 输出。

登录后，可以使用 Azure CLI 运行 Azure 命令，处理订阅中的资源。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clone-or-download-the-sample-app"></a>克隆或下载示例应用

# <a name="git-clone"></a>[Git 克隆](#tab/clone)

克隆示例存储库：

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

然后导航到该文件夹：

```terminal
cd djangoapp
```

# <a name="download"></a>[下载](#tab/download)

访问 [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)，选择“克隆”，然后选择“下载 ZIP” 。 

将 ZIP 文件解压缩到名为“djangoapp”的文件夹中。 

然后，在该“djangoapp”文件夹中打开终端窗口。

---

Djangoapp 示例包含数据驱动的 Django 投票应用，该应用是根据 Django 文档中的[编写你的第一个 Django 应用](https://docs.djangoproject.com/en/3.1/intro/tutorial01/)创建的。 为便于参考，本文在此提供了已完成的应用。

示例还会修改为在应用服务等生产环境中运行：

- 生产设置位于“azuresite/production.py”文件中。 开发详细信息位于 azuresite/settings.py 中。
- 当 `DJANGO_ENV` 环境变量设置为“生产”时，应用将使用生产设置。 你将稍后在本教程中创建此环境变量以及用于 PostgreSQL 数据库配置的其他环境变量。

这些更改特定于将 Django 配置为在任何生产环境中运行，而不是特定于应用服务。 有关详细信息，请参阅 [Django 部署清单](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/)。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="create-postgres-database-in-azure"></a>在 Azure 中创建 Postgres 数据库

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

安装适用于 Azure CLI 的 `db-up` 扩展：

```azurecli
az extension add --name db-up
```

如果无法识别 `az` 命令，请确保按照[设置初始环境](#set-up-your-initial-environment)中所述安装 Azure CLI。

然后使用 [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) 命令在 Azure 中创建 Postgres 数据库：

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- 将 *\<postgres-server-name>* 替换为在所有 Azure 中唯一的名称（服务器终结点是 `https://<postgres-server-name>.postgres.database.azure.com`）。 良好的模式是结合使用公司名称和其他唯一值。
- 对于 \<admin-username> 和 \<admin-password>，请指定用来为此 Postgres 服务器创建管理员用户的凭据 。
- 此处使用的 B_Gen5_1（基本，第 5 代，1 核）[定价层](../postgresql/concepts-pricing-tiers.md)成本最低。 对于生产数据库，请省略 `--sku-name` 参数以改用 GP_Gen5_2（常规用途，第 5 代，2 核）层。

此命令将执行以下操作，可能需要花几分钟的时间：

- 创建名为 `DjangoPostgres-tutorial-rg` 的[资源组](../azure-resource-manager/management/overview.md#terminology)（如果尚未存在）。
- 创建 Postgres 服务器。
- 使用唯一的用户名和密码创建默认的管理员帐户。 （若要指定自己的凭据，请通过 `az postgres up` 命令使用 `--admin-user` 和 `--admin-password` 参数。）
- 创建 `pollsdb` 数据库。
- 支持从本地 IP 地址进行访问。
- 支持从 Azure 服务进行访问。
- 创建有权访问 `pollsdb` 数据库的数据库用户。

可以使用其他 `az postgres` 和 `psql` 命令单独执行每个步骤，但 `az postgres up` 可以完成所有这些操作。

当该命令完成时，它会输出一个 JSON 对象，其中包含数据库的不同连接字符串以及服务器 URL、生成的用户名（例如“joyfulKoala@msdocs-djangodb-12345”）和 GUID 密码。 将用户名和密码复制到临时文本文件，你需要在本教程稍后的内容中使用它们。

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>` 可以设置为任一个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 可以使用 [`az account list-locations`](/cli/azure/account#az-account-list-locations) 命令获取可供你的订阅使用的区域。 对于生产应用，请将数据库和应用放置在同一位置。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="deploy-the-code-to-azure-app-service"></a>将代码部署到 Azure 应用服务

在本部分中，你将在应用服务应用中创建应用主机，将此应用连接到 Postgres 数据库，然后将代码部署到该主机。

### <a name="create-the-app-service-app"></a>创建应用服务应用

在终端中，请确保位于包含应用代码的“djangoapp”存储库文件夹中。

使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令创建应用服务应用（主机进程）：

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- 对于 `--location` 参数，请使用与上一部分中数据库相同的位置。
- 将 *\<app-name>* 替换为在整个 Azure 中唯一的名称（服务器终结点是 `https://<app-name>.azurewebsites.net`）。 \<app-name> 允许的字符为 `A`-`Z`、`0`-`9` 和 `-`。 良好的模式是结合使用公司名称和应用标识符。

此命令将执行以下操作，可能需要花几分钟的时间：

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- 创建[资源组](../azure-resource-manager/management/overview.md#terminology)（如果尚未存在）。 （在此命令中，你将使用之前在其中创建数据库的同一资源组。）
- 在基本定价层 (B1) 中创建[应用服务计划](overview-hosting-plans.md)“DjangoPostgres-tutorial-plan”（如果不存在）。 `--plan` 和 `--sku` 都是可选的。
- 创建应用服务应用（如果不存在）。
- 为应用启用默认日志记录（如果尚未启用）。
- 在启用了生成自动化的情况下，使用 ZIP 部署上传存储库。
- 将常用参数（例如资源组和应用服务计划的名称）缓存到文件“.azure/config”中。因此，不需要使用后面的命令指定所有相同的参数。 例如，若要在进行更改后重新部署应用，则无需任何参数即可再次运行 `az webapp up`。 但是，来自 CLI 扩展的命令（如 `az postgres up`）目前不使用缓存，因此在初次使用 `az webapp up` 时需要在此处指定资源组和位置。

成功部署后，该命令会生成类似于以下示例的 JSON 输出：

![示例 az webapp up 命令输出](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> 如果在此时尝试访问应用的 URL，则会遇到错误“DisallowedHost at/”。 发生此错误的原因是你尚未将应用配置为使用前面讨论过的生产设置，你可以在以下部分中执行此操作。

### <a name="configure-environment-variables-to-connect-the-database"></a>配置环境变量以连接数据库

将代码部署到应用服务后，下一步是将应用连接到 Azure 中的 Postgres 数据库。

应用代码需要在多个环境变量中查找数据库信息。 若要在应用服务中设置环境变量，你需要使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令创建“应用设置”。

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>@<postgres-server-name>" DBPASS="<password>"
```

- 将 *\<postgres-server-name>* 替换为之前通过 `az postgres up` 命令使用的名称。
- 将 *\<username>* 和 *\<password>* 替换为命令也为你生成的凭据。 `DBUSER` 参数的格式必须为 `<username>@<postgres-server-name>`。
- 资源组和应用名称是从“.azure/config”文件中的缓存值中提取的。
- 命令按应用代码的预期方式创建名为 `DJANGO_ENV`、`DBHOST`、`DBNAME`、`DBUSER`和 `DBPASS` 的设置。
- 在 Python 代码中，可以使用 `os.environ.get('DJANGO_ENV')` 之类的语句来访问这些设置（作为环境变量）。 有关详细信息，请参阅[访问环境变量](configure-language-python.md#access-environment-variables)。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="run-django-database-migrations"></a>运行 Django 数据库迁移

Django 数据库迁移会确保 Azure 数据库上的 PostgreSQL 中的架构与代码中描述的架构相匹配。

1. 通过在浏览器中导航至以下 URL 并使用 Azure 帐户凭据（而不是数据库服务器凭据）登录来建立 SSH 会话。

    ```
    https://<app-name>.scm.azurewebsites.net/webssh/host
    ```

    将 `<app-name>` 替换为之前在 `az webapp up` 命令中使用的名称。

    在 macOS 和 Linux 上，可以使用 [`az webapp ssh`](/cli/azure/webapp?view=azure-cli-latest&preserve-view=true#az_webapp_ssh) 命令以其他方式连接到 SSH 会话。

1. 在 SSH 会话中运行以下命令（可以使用 Ctrl+Shift+V 粘贴命令）  ：

    ```bash
    # Change to the folder where the app code is deployed
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate

    # Install packages
    pip install -r requirements.txt

    # Run database migrations
    python manage.py migrate

    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. `createsuperuser` 命令会提示输入超级用户凭据。 针对本教程，请使用默认的用户名 `root`，对于电子邮件地址，按 Enter 以留空，并输入 `Pollsdb1` 作为密码。

1. 如果看到“数据库已锁定”错误，请确保已在上一部分运行 `az webapp settings` 命令。 如果没有这些设置，migrate 命令将无法与数据库通信，从而导致错误。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)
    
### <a name="create-a-poll-question-in-the-app"></a>在应用中创建投票问题

1. 在浏览器中打开 URL `http://<app-name>.azurewebsites.net`。 应用应显示消息“无可用投票”，因为数据库中尚没有特定的投票。

1. 浏览到 `http://<app-name>.azurewebsites.net/admin`。 使用上一节中的超级用户凭据登录（`root` 和 `Pollsdb1`）。 在“投票”下，选择“问题”旁边的“添加”，创建一个包含一些选项的投票问题  。

1. 再次浏览到 `http://<app-name>.azurewebsites.net`，确认现在是否向用户显示了问题。 回答你希望如何在数据库中生成某些数据。

祝贺你！ 你将在适用于 Linux 的 Azure 应用服务中使用主动 Postgres 数据库运行 Python Django Web 应用。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> 应用服务将通过在每个 `manage.py startproject` 默认创建的子文件夹中查找“wsgi.py”文件来检测 Django 项目。 应用服务找到该文件后，就会加载 Django Web 应用。 有关详细信息，请参阅[配置内置的 Python 映像](configure-language-python.md)。

## <a name="make-code-changes-and-redeploy"></a>进行代码更改并重新部署

在本部分中，你将对应用进行本地更改，并将代码重新部署到应用服务。 在此过程中，你将设置支持正在进行的工作的 Python 虚拟环境。

### <a name="run-the-app-locally"></a>在本地运行应用

在终端窗口中运行以下命令。 创建超级用户时，请确保遵循以下提示：

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

```cmd
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

完全加载 Web 应用后，Django 开发服务器会在以下消息中提供本地应用 URL：“在 http://127.0.0.1:8000/ 启动开发服务器”。 使用“CTRL BREAK”退出服务器。

![示例 Django 开发服务器输出](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

在本地测试应用，步骤如下：

1. 在浏览器中转到 `http://localhost:8000`，该浏览器应显示消息“无可用投票”。 

1. 转到 `http:///localhost:8000/admin`，并使用之前创建的管理员用户登录。 在“轮询”下方再次选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题  。 

1. 再次转到“http:\//localhost:8000”，回答问题以测试应用。 

1. 按 Ctrl+C 来停止 Django 服务器 。

在本地运行时，应用将使用本地 Sqlite3 数据库，而不会影响生产数据库。 如果需要，还可以使用本地 PostgreSQL 数据库来更好地模拟生产环境。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="update-the-app"></a>更新应用

在 `polls/models.py` 中，找到以 `choice_text` 开头的行，并将 `max_length` 参数更改为 100：

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

因为你更改了数据模型，所以请创建新的 Django 迁移并迁移数据库：

```
python manage.py makemigrations
python manage.py migrate
```

再次使用 `python manage.py runserver` 运行开发服务器，并在“http:\//localhost:8000/admin”中测试该应用：

使用 Ctrl +C 再次停止 Django Web 服务器 。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="redeploy-the-code-to-azure"></a>将代码重新部署到 Azure

在存储库根目录中运行以下命令：

```azurecli
az webapp up
```

此命令使用“.azure/config”文件中缓存的参数。 由于应用服务检测到应用已存在，因此仅重新部署代码。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新运行迁移

由于对数据模型进行了更改，因此需要在应用服务中重新运行数据库迁移。

通过在浏览器中导航到 `https://<app-name>.scm.azurewebsites.net/webssh/host` 再次建立 SSH 会话。 然后运行以下命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

### <a name="review-app-in-production"></a>在生产环境中查看应用

浏览到“`http://<app-name>.azurewebsites.net`”并再次在生产中测试应用。 （因为你只更改了数据库字段的长度，所以仅在创建问题时尝试输入较长的响应时，更改才会比较明显。）

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="stream-diagnostic-logs"></a>流式传输诊断日志

可以访问在 Azure 上托管应用的容器内部生成的控制台日志。

运行以下 Azure CLI 命令查看日志流。 此命令使用“.azure/config”文件中缓存的参数。

```azurecli
az webapp log tail
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

若要随时停止日志流式处理，可键入 Ctrl+C 。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> 也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。
>
> `az webapp up` 将为你启用默认日志记录。 出于性能原因，此日志记录在一段时间后会自动禁用，但每次重新运行 `az webapp up` 时，它又会重新启用。 若要手动启用此日志记录，请运行以下命令：
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 门户中管理应用

在 [Azure 门户](https://portal.azure.com)中，搜索应用名称并在结果中选择应用。

![在 Azure 门户中导航到 Python Django 应用](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

默认情况下，门户会显示应用的“概述”页，该页面提供一般的性能视图。 也可在此处执行基本的管理任务，例如浏览、停止、重启和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![在 Azure 门户的“概述”页中管理 Python Django 应用](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clean-up-resources"></a>清理资源

如果想要保留应用或者继续查看下一个教程，请直接跳到[接下来的步骤](#next-steps)。 否则，若要避免产生持续的费用，你可以删除为本教程创建的资源组：

```azurecli
az group delete --no-wait
```

此命令使用 .azure/config 文件中缓存的资源组名称。 通过删除资源组，还可以解除分配并删除其中包含的所有资源。

删除所有资源可能需要一些时间。 `--no-wait` 参数允许命令立即返回。

[存在问题？请告诉我们。](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>后续步骤

了解如何将自定义 DNS 名称映射到应用：

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](app-service-web-tutorial-custom-domain.md)

了解应用服务如何运行 Python 应用：

> [!div class="nextstepaction"]
> [配置 Python 应用](configure-language-python.md)
