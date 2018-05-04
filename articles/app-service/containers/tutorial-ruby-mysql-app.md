---
title: 在基于 Linux 的 Azure 应用服务中生成 Ruby 和 MySQL Web 应用 | Microsoft Docs
description: 了解如何创建一个可在 Azure 中运行的 Ruby 应用，并将其连接到 MySQL 数据库。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a026eafeb71c67a2cb98c20c4fc5af16073be083
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>在基于 Linux 的 Azure 应用服务中生成 Ruby 和 MySQL Web 应用

[Linux 应用服务](app-service-linux-intro.md)使用 Linux 操作系统，提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何创建 Ruby Web 应用，并将其连接到 MySQL 数据库。 完成本教程后，Linux 上的应用服务将会运行一个 [Ruby on Rails](http://rubyonrails.org/) 应用。

![Azure 应用服务中运行的 Ruby on Rails 应用](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 Ruby on Rails 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

完成本教程：

* [安装 Git](https://git-scm.com/)
* [安装 Ruby 2.3](https://www.ruby-lang.org/documentation/installation/)
* [安装 Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html)
* [安装并启动 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>准备本地 MySQL

此步骤在本地 MySQL 服务器供中创建一个数据库，以便在本教程中使用。

### <a name="connect-to-local-mysql-server"></a>连接到本地 MySQL 服务器

在终端窗口中连接到本地 MySQL 服务器。 可使用此终端窗口运行本教程中的所有命令。

```bash
mysql -u root -p
```

当系统提示输入密码时，请输入 `root` 帐户的密码。 如果不记得自己的 root 帐户密码，请参阅 [MySQL：如何重置 Root 密码](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果命令成功运行，则表示 MySQL 服务器正在运行。 否则，请确保遵循 [MySQL 安装后步骤](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)启动本地 MySQL 服务器。

键入 `quit` 退出服务器连接。

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>在本地创建 Ruby on Rails 应用
此步骤创建一个 Ruby on Rails 示例应用程序、配置其数据库连接，并在本地运行该应用程序。 

### <a name="clone-the-sample"></a>克隆示例

在终端窗口中，通过 `cd` 转到工作目录。

运行下列命令以克隆示例存储库。

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

通过 `cd` 转到克隆目录。 安装所需程序包。

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>配置 MySQL 连接

在存储库中，打开 _config/database.yml_ 并提供本地 MySQL 的 root 用户名和密码（第 13 行）。 如果使用 [Homebrew](https://brew.sh/) 等工具安装了 MySQL，则该文件中的凭据已设置为默认值（用户名为 `root`，密码为空）。

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>在本地运行示例

运行 [Rails 迁移](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations)以创建应用程序所需的表。 若要查看迁移中创建了哪些表，请查看 Git 存储库中的 _db/migrate_ 目录。

```bash
rake db:create
rake db:migrate
```

运行应用程序。

```bash
rails server
```

在浏览器中导航至 `http://localhost:3000`。 在页面中添加一些任务。

![Ruby on Rails 已成功连接到 MySQL](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>在 Azure 中创建 MySQL

此步骤在[用于 MySQL 的 Azure 数据库（预览版）](/azure/mysql)中创建一个 MySQL 数据库。 稍后需要将 Ruby on Rails 应用程序配置为连接到此数据库。

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>创建 MySQL 服务器

使用 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 命令在 Azure Database for MySQL（预览版）中创建一个服务器。

在以下命令中，请将 &lt;mysql_server_name> 占位符替换为你自己的唯一 MySQL 服务器名称（有效字符是 `a-z`、`0-9` 和 `-`）。 此名称是 MySQL 服务器主机名 (`<mysql_server_name>.mysql.database.azure.com`) 的一部分，必须全局唯一。

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

创建 MySQL 服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>配置服务器防火墙

使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 命令创建 MySQL 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)。
>

### <a name="connect-to-production-mysql-server-locally"></a>在本地连接到生产 MySQL 服务器

在终端窗口中，连接到 Azure 中的 MySQL 服务器。 使用前面为 &lt;mysql_server_name> 指定的值。

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

当提示输入密码时，请使用在创建数据库服务器时指定的 _My5up3r$tr0ngPa$w0rd!_。

### <a name="create-a-production-database"></a>创建生产数据库

在 `mysql` 提示中创建数据库。

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>创建具有权限的用户

创建一个名为 _railsappuser_ 的数据库用户并向其授予 `sampledb` 数据库中的所有特权。

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

键入 `quit` 退出服务器连接。

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>将应用连接到 Azure MySQL

此步骤将 Ruby on Rails 应用程序连接到在用于 MySQL 的 Azure 数据库（预览版）中创建的 MySQL 数据库。

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>配置数据库连接

在存储库中，打开 _config/database.yml_。 在该文件的底部，将生产变量替换为以下代码。 对于 _&lt;mysql_server_name>_ 占位符，请使用创建的 MySQL 服务器的名称。

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

保存更改。

> [!NOTE]
> 随即将会添加指向示例存储库中现有 _.pem_ 文件的 `sslca`。 默认情况下，用于 MySQL 的 Azure 数据库强制执行来自客户端的 SSL 连接。 可以使用此 `.pem` 证书来与 Azure Database for MySQL 建立 SSL 连接。 有关详细信息，请参阅[配置应用程序中的 SSL 连接性以安全连接到 Azure Database for MySQL](../../mysql/howto-configure-ssl.md)。
>

### <a name="test-the-application-locally"></a>在本地测试应用程序

在本地终端中设置以下环境变量：

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

使用刚刚配置的生产值运行 Rails 数据库迁移，在 Azure Database for MySQL（预览版）中的 MySQL 数据库内创建表。 

```bash
rake db:migrate RAILS_ENV=production
```

在生产环境中运行时，Rails 应用程序需要预编译的资产。 使用以下命令生成所需的资产：

```bash
rake assets:precompile
```

Rails 生产环境还使用机密来管理安全性。 生成机密密钥

```bash
rails secret
```

将机密密钥保存到 Rails 生产环境使用的相应变量。 为方便起见，可对两个变量使用相同的密钥。

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

启用 Rails 生产环境，以提供 JavaScript 和 CSS 文件。

```bash
export RAILS_SERVE_STATIC_FILES=true
```

在生产环境中运行示例应用程序。

```bash
rails server -e production
```

导航到 `http://localhost:3000`。 如果页面可加载且未出错，则表示 Ruby on Rails 应用程序正在连接到 Azure 中的 MySQL 数据库。

在页面中添加一些任务。

![Ruby on Rails 已成功连接到用于 MySQL 的 Azure 数据库（预览版）](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

### <a name="commit-your-changes"></a>提交更改

运行以下的 Git 命令，提交更改：

```bash
git add .
git commit -m "database.yml updates"
```

应用已可用于部署。

## <a name="deploy-to-azure"></a>“部署到 Azure”

此步骤将已连接 MySQL 的 Rails 应用程序部署到 Azure 应用服务。

### <a name="configure-a-deployment-user"></a>配置部署用户

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>创建应用服务计划

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>创建 Web 应用

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>配置数据库设置

在应用服务的 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 命令将环境变量设置为应用设置。

以下 Cloud Shell 命令配置应用设置 `DB_HOST`、`DB_DATABASE`、`DB_USERNAME` 和 `DB_PASSWORD`。 替换占位符 &lt;appname> 和 &lt;mysql_server_name>。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>配置 Rails 环境变量

在本地终端中，为 Azure 中的 Rails 生产环境生成新的机密密钥。

```bash
rails secret
```

配置 Rails 生产环境所需的变量。

在以下 Cloud Shell 命令中，将两个 _&lt;output_of_rails_secret>_ 占位符替换在本地终端中生成的新机密密钥。

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"` 告知默认 Ruby 容器要在每次进行 Git 部署时预编译资产。

### <a name="push-to-azure-from-git"></a>从 Git 推送到 Azure

在本地终端中，将 Azure 远程功能添加到本地 Git 存储库。

```bash
git remote add azure <paste_copied_url_here>
```

推送到 Azure 远程功能以部署 Ruby on Rails 应用程序。 系统会提示输入前面在创建部署用户期间提供的密码。

```bash
git push azure master
```

在部署期间，Azure 应用服务会向 Git 告知其进度。

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

浏览到 `http://<app_name>.azurewebsites.net` 并在列表中添加一些任务。

![Azure 应用服务中运行的 Ruby on Rails 应用](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

恭喜，你已在 Azure 应用服务中运行了一个数据驱动的 Ruby 应用。

## <a name="update-model-locally-and-redeploy"></a>在本地更新模型和重新部署

在此步骤中，对 `task` 数据模型和 webapp 进行简单更改，然后将更新发布到 Azure。

对于任务方案，需要修改应用程序，以便能够将任务标记为已完成。

### <a name="add-a-column"></a>添加列

在终端中，导航到 Git 存储库中的根路径。

生成新的迁移，用于将名为 `Done` 的布尔列添加到 `Tasks` 表：

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

此命令在 _db/migrate_ 目录中生成新的迁移文件。


在终端中运行 Rails 数据库迁移，以便在本地数据库中进行更改。

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>更新应用程序逻辑

打开 *app/controllers/tasks_controller.rb* 文件。 在该文件的末尾找到以下行：

```rb
params.require(:task).permit(:Description)
```

修改此行，以包含新的 `Done` 参数。

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>更新视图

打开 *app/views/tasks/_form.html.erb* 文件，即“编辑”窗体。

找到 `<%=f.error_span(:Description) %>` 行，并紧接在其下方插入以下代码：

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

打开 *app/views/tasks/show.html.erb* 文件，即单记录“视图”页。 

找到 `<dd><%= @task.Description %></dd>` 行，并紧接在其下方插入以下代码：

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

打开 *app/views/tasks/index.html.erb* 文件，即所有记录的“索引”页。

找到 `<th><%= model_class.human_attribute_name(:Description) %></th>` 行，并紧接在其下方插入以下代码：

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

在同一文件中找到 `<td><%= task.Description %></td>` 行，并紧接在其下方插入以下代码：

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>在本地测试更改

在本地终端中运行 Rails 服务器。

```bash
rails server
```

若要查看任务状态的变化，请导航到 `http://localhost:3000` 并添加或编辑项。

![将复选框添加到任务](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

若要停止 Rails 服务器，请在终端中键入 `Ctrl + C`。

### <a name="publish-changes-to-azure"></a>发布对 Azure 所做的更改

在终端中，针对生产环境运行 Rails 数据库迁移，在 Azure 数据库中进行更改。

```bash
rake db:migrate RAILS_ENV=production
```

提交在 Git 中进行的所有更改，然后将代码更改推送到 Azure。

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push` 完成后，请导航至 Azure Web 应用，然后测试新功能。

![发布到 Azure 的模型和数据库更改](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

如果添加任何任务，则它们保留在数据库中。 更新数据架构不会改变现有数据。

## <a name="manage-the-azure-web-app"></a>管理 Azure Web 应用

转到 [Azure 门户](https://portal.azure.com)管理创建的 Web 应用。

在左侧菜单中单击“应用服务”，然后单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/tutorial-php-mysql-app/access-portal.png)

将看到 Web 应用的概述页。 在此处可以执行基本的管理任务，例如停止、启动、重启、浏览和删除。

左侧菜单提供用于配置应用的页面。

![Azure 门户中的应用服务页](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将 Ruby on Rails 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新数据模型并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](../app-service-web-tutorial-custom-domain.md)
