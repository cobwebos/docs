---
title: 教程：在虚拟网络中使用应用服务和 Azure Database for PostgreSQL 灵活服务器（预览版）部署 Django 应用
description: 在虚拟网络中使用应用服务和 Azure Database for PostgreSQL 灵活服务器（预览版）部署 Django 应用
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/22/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 3366f39f3aca8ad0114244c122d1003b5e9b91a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90929259"
---
# <a name="tutorial-deploy-django-app-with-app-service-and-azure-database-for-postgresql---flexible-server-preview"></a>教程：使用应用服务和 Azure Database for PostgreSQL 灵活服务器（预览版）部署 Django 应用

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器以预览版提供

在本教程中，了解如何在虚拟网络中使用应用服务和 Azure Database for PostgreSQL 灵活服务器在 Azure 中部署 Django 应用。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

本文要求在本地运行 Azure CLI 2.0 或更高版本。 若要查看安装的版本，请运行 `az --version` 命令。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

你将需要使用 [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) 命令登录到你的帐户。 记下与订阅名称相对应的命令输出中的 **id** 属性。

```azurecli
az login
```

如果有多个订阅，请选择应计费的资源所在的相应订阅。 使用 [az account set](/cli/azure/account) 命令选择帐户下的特定订阅 ID。 将 az login 输出中的你的订阅的订阅 ID 属性替换到订阅 ID 占位符中 。

```azurecli
az account set --subscription <subscription id>
```
## <a name="clone-or-download-the-sample-app"></a>克隆或下载示例应用

# <a name="git-clone"></a>[Git 克隆](#tab/clone)

克隆示例存储库：

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

然后转到该文件夹：

```terminal
cd djangoapp
```

# <a name="download"></a>[下载](#tab/download)

访问 [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp)，选择“克隆”，然后选择“下载 ZIP” 。

将 ZIP 文件解压缩到名为“djangoapp”的文件夹中。

然后，在该“djangoapp”文件夹中打开终端窗口。

---

Djangoapp 示例包含数据驱动的 Django 投票应用，该应用是根据 Django 文档中的[编写你的第一个 Django 应用](https://docs.djangoproject.com/en/2.1/intro/tutorial01/)创建的。 为便于参考，本文在此提供了已完成的应用。

示例还会修改为在应用服务等生产环境中运行：

- 生产设置位于“azuresite/production.py”文件中。 开发详细信息位于 azuresite/settings.py 中。
- 当 `DJANGO_ENV` 环境变量设置为“生产”时，应用将使用生产设置。 你将稍后在本教程中创建此环境变量以及用于 PostgreSQL 数据库配置的其他环境变量。

这些更改特定于将 Django 配置为在任何生产环境中运行，而不是特定于应用服务。 有关详细信息，请参阅 [Django 部署清单](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/)。

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>在新的虚拟网络中创建 PostgreSQL 灵活服务器

使用以下命令在虚拟网络 (VNET) 中创建专用灵活服务器和数据库：
```azurecli
# Create Flexible server in a VNET

az postgres flexible-server create --resource-group myresourcegroup --location westus2

```

此命令将执行以下操作，可能需要花几分钟的时间：

- 创建资源组（如果尚不存在）。
- 生成服务器名称（如果未提供）。
- 为新的 PostgreSQL 服务器创建新的虚拟网络。 记下为服务器创建的虚拟网络名称和子网名称，因为你需要将 Web 应用添加到同一虚拟网络。
- 创建服务器的管理员用户名和密码（如果未提供）。 记下用户名和密码，以供下一步使用。
- 创建可用于开发的数据库 ```postgres```。 可以运行 [psql](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql) 连接到该数据库，以创建其他数据库。

> [!NOTE]
> 记下你的密码，如果未提供密码，系统将为你生成一个。 如果忘记密码，则必须使用 ``` az postgres flexible-server update``` 命令重置密码


## <a name="deploy-the-code-to-azure-app-service"></a>将代码部署到 Azure 应用服务

在本部分中，你将在应用服务应用中创建应用主机，将此应用连接到 Postgres 数据库，然后将代码部署到该主机。


### <a name="create-the-app-service-web-app-in-a-virtual-network"></a>在虚拟网络中创建应用服务 Web 应用

在终端中，请确保位于包含应用代码的存储库根（`djangoapp`）。

使用 [`az webapp up`](/cli/azure/webapp#az-webapp-up) 命令创建应用服务应用（主机进程）：

```azurecli

# Create a web app
az webapp up --resource-group myresourcegroup --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>

# Enable VNET integration for web app.
# Replace <vnet-name> and <subnet-name> with the virtual network and subnet name that the flexible server is using.

az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet <vnet-name> --subnet <subnet-name>

# Configure database information as environment variables
# Use the postgres server name , database name , username , password for the database created in the previous steps

az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>"
```
- 对于 `--location` 参数，请使用与上一部分中数据库相同的位置。
- 将 *\<app-name>* 替换为在整个 Azure 中唯一的名称（服务器终结点是 `https://\<app-name>.azurewebsites.net`）。 \<app-name> 允许的字符为 `A`-`Z`、`0`-`9` 和 `-`。 良好的模式是结合使用公司名称和应用标识符。
- 在基本定价层 (B1) 中创建[应用服务计划](../../app-service/overview-hosting-plans.md)“DjangoPostgres-tutorial-plan”（如果不存在）。 `--plan` 和 `--sku` 都是可选的。
- 创建应用服务应用（如果不存在）。
- 为应用启用默认日志记录（如果尚未启用）。
- 在启用了生成自动化的情况下，使用 ZIP 部署上传存储库。
- az webapp vnet-integration 命令将 Web 应用添加到与 Postgres 服务器相同的虚拟网络中。
- 应用代码需要在多个环境变量中查找数据库信息。 若要在应用服务中设置环境变量，你需要使用 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) 命令创建“应用设置”。

> [!TIP]
> 许多 Azure CLI 命令将常见参数（例如资源组和应用服务计划的名称）缓存到文件“.azure/config”中。因此，不需要使用后面的命令指定所有相同的参数。 例如，若要在进行更改后重新部署应用，则无需任何参数即可再次运行 `az webapp up`。

### <a name="run-django-database-migrations"></a>运行 Django 数据库迁移

Django 数据库迁移会确保 Azure 数据库上的 PostgreSQL 中的架构与代码中描述的架构相匹配。

1. 通过导航至“https://\<app-name>.scm.azurewebsites.net/webssh/host”在浏览器中打开 SSH 会话，并使用 Azure 帐户凭据（而不是数据库服务器凭据）登录。

1. 在 SSH 会话中运行以下命令（可以使用 Ctrl+Shift+V 粘贴命令）  ：

    ```bash
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

1. `createsuperuser` 命令会提示输入超级用户凭据。 针对本教程，请使用默认的用户名 `root`，对于电子邮件地址，按 Enter 以留空，并输入 `postgres1` 作为密码。

### <a name="create-a-poll-question-in-the-app"></a>在应用中创建轮询问题

1. 在浏览器中打开 URL“http:\//\<app-name>.azurewebsites.net”。 应用应显示消息“无可用轮询”，因为数据库中尚没有特定的轮询。

1. 浏览到“http:\//\<app-name>.azurewebsites.net/admin”。使用上一节中的超级用户凭据登录（`root` 和 `postgres1`）。 在“轮询”下，选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题  。

1. 再次浏览到“http:\//\<app-name>.azurewebsites.net/”，确认现在是否向用户显示了问题。 回答你希望如何在数据库中生成某些数据。

祝贺你！ 你将在适用于 Linux 的 Azure 应用服务中使用主动 Postgres 数据库运行 Python Django Web 应用。

> [!NOTE]
> 应用服务将通过在每个 `manage.py startproject` 默认创建的子文件夹中查找“wsgi.py”文件来检测 Django 项目。 应用服务找到该文件后，就会加载 Django Web 应用。 有关详细信息，请参阅[配置内置的 Python 映像](../../app-service/configure-language-python.md)。

## <a name="make-code-changes-and-redeploy"></a>进行代码更改并重新部署

在本部分中，你将对应用进行本地更改，并将代码重新部署到应用服务。 在此过程中，你将设置支持正在进行的工作的 Python 虚拟环境。

### <a name="run-the-app-locally"></a>在本地运行应用

在终端窗口中运行以下命令。 创建超级用户时，请确保遵循以下提示：

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
完全加载 Web 应用后，Django 开发服务器会在以下消息中提供本地应用 URL：“在 http://127.0.0.1:8000/ 启动开发服务器”。 使用“CTRL BREAK”退出服务器。

:::image type="content" source="./media/tutorial-django-app-service-postgres/django-dev-server-output.png" alt-text="示例 Django 开发服务器输出":::

在本地测试应用，步骤如下：

1. 在浏览器中转到“http:\//localhost:8000”，应再次看到消息“无可用轮询”。

1. 转到“http:\//localhost:8000/admin”，使用之前创建的管理员用户登录。 在“轮询”下方再次选择“问题”旁边的“添加”，创建一个包含一些选项的轮询问题  。

1. 再次转到“http:\//localhost:8000”，回答问题以测试应用。

1. 按 Ctrl+C 来停止 Django 服务器 。

在本地运行时，应用将使用本地 Sqlite3 数据库，而不会影响生产数据库。 如果需要，还可以使用本地 PostgreSQL 数据库来更好地模拟生产环境。



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


### <a name="redeploy-the-code-to-azure"></a>将代码重新部署到 Azure

在存储库根目录中运行以下命令：

```azurecli
az webapp up
```

此命令使用“.azure/config”文件中缓存的参数。 由于应用服务检测到应用已存在，因此仅重新部署代码。



### <a name="rerun-migrations-in-azure"></a>在 Azure 中重新运行迁移

由于对数据模型进行了更改，因此需要在应用服务中重新运行数据库迁移。

通过导航至“https://\<app-name>.scm.azurewebsites.net/webssh/host”在浏览器中再次打开 SSH 会话。 然后运行以下命令：

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>在生产环境中查看应用

浏览到“http:\//\<app-name>.azurewebsites.net”并再次在生产中测试应用。 （因为你只更改了数据库字段的长度，所以仅在创建问题时尝试输入较长的响应时，更改才会比较明显。）

> [!TIP]
> 可以使用 [django-storages](https://django-storages.readthedocs.io/en/latest/backends/azure.html) 将静态资产和媒体资产存储在 Azure 存储中。 可以使用 Azure CDN 对静态文件进行 gzip 操作。


## <a name="manage-your-app-in-the-azure-portal"></a>在 Azure 门户中管理应用

在 [Azure 门户](https://portal.azure.com)中，搜索应用名称并在结果中选择应用。

:::image type="content" source="./media/tutorial-django-app-service-postgres/navigate-to-django-app-in-app-services-in-the-azure-portal.png" alt-text="示例 Django 开发服务器输出":::

默认情况下，门户会显示应用的“概述”页，该页面提供一般的性能视图。 也可在此处执行基本的管理任务，例如浏览、停止、重启和删除。 该页左侧的选项卡显示可以打开的不同配置页。

:::image type="content" source="./media/tutorial-django-app-service-postgres/manage-django-app-in-app-services-in-the-azure-portal.png" alt-text="示例 Django 开发服务器输出":::


## <a name="clean-up-resources"></a>清理资源

如果想要保留应用或者继续查看下一个教程，请直接跳到[接下来的步骤](#next-steps)。 否则，若要避免产生持续的费用，你可以删除为本教程创建的资源组：

```azurecli
az group delete -g myresourcegroup
```

此命令使用 .azure/config 文件中缓存的资源组名称。 通过删除资源组，还可以解除分配并删除其中包含的所有资源。

## <a name="next-steps"></a>后续步骤

了解如何将自定义 DNS 名称映射到应用：

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../../app-service/app-service-web-tutorial-custom-domain.md)

了解应用服务如何运行 Python 应用：

> [!div class="nextstepaction"]
> [配置 Python 应用](../../app-service/configure-language-python.md)
