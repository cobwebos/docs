---
title: 在 Azure 中构建 Java 和 MySQL Web 应用
description: 了解如何创建一个可连接到 Azure MySQL 数据库服务的、在 Azure appservice 中运行的 Java 应用。
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 46e222ffe40db186343250efc71e20d41adbc285
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
ms.locfileid: "33203113"
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>教程：在 Azure 中构建 Java 和 MySQL Web 应用

> [!NOTE]
> 本文将应用部署到 Windows 上的应用服务。 若要部署到基于 _Linux_ 的应用服务，请参阅[将容器化 Spring Boot 应用部署到 Azure](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin)。
>

本教程介绍如何在 Azure 中创建 Java Web 应用，并将其连接到 MySQL 数据库。 完成本教程后，即可使用 [Spring Boot](https://projects.spring.io/spring-boot/) 应用程序将数据存储到[用于 MySQL 的 Azure 数据库](https://docs.microsoft.com/azure/mysql/overview)，后者运行在 [Azure 应用服务 Web 应用](app-service-web-overview.md)中。

![在 Azure appservice 中运行的 Java 应用](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将示例应用连接到数据库
> * 将应用部署到 Azure
> * 更新并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中监视应用

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

1. [下载并安装 Git](https://git-scm.com/)
1. [下载并安装 Java 7 JDK 或更高版本](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [下载、安装并启动 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>准备本地 MySQL 

此步骤在本地 MySQL 服务器中创建一个数据库，用于在计算机上对应用进行本地测试。

### <a name="connect-to-mysql-server"></a>连接到 MySQL 服务器

在终端窗口中连接到本地 MySQL 服务器。 可使用此终端窗口运行本教程中的所有命令。

```bash
mysql -u root -p
```

当系统提示输入密码时，请输入 `root` 帐户的密码。 如果不记得自己的 root 帐户密码，请参阅 [MySQL：如何重置 Root 密码](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。

如果命令成功运行，则表示 MySQL 服务器已在运行。 否则，请确保遵循 [MySQL 安装后步骤](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)启动本地 MySQL 服务器。

### <a name="create-a-database"></a>创建数据库 

在 `mysql` 提示符下，创建待办事项数据库和表。

```sql
CREATE DATABASE tododb;
```

键入 `quit` 退出服务器连接。

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>创建并运行示例应用 

此步骤克隆示例 Spring Boot 应用，将其配置为使用本地 MySQL 数据库，然后在计算机上运行。 

### <a name="clone-the-sample"></a>克隆示例

在终端窗口中导航到工作目录，然后克隆示例存储库。 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>将应用配置为使用 MySQL 数据库

使用打开 MySQL 提示符时所用的根密码更新 `spring.datasource.password` 和 *spring-boot-mysql-todo/src/main/resources/application.properties* 中的值：

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>生成并运行示例

使用存储库中包括的 Maven 包装器生成并运行示例：

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

打开浏览器并转到 `http://localhost:8080`，查看正在操作的示例。 向列表添加任务时，请在 MySQL 提示符处使用以下 SQL 命令，查看 MySQL 中存储的数据。

```SQL
use testdb;
select * from todo_item;
```

在终端处按 `Ctrl`+`C` 停止应用程序。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>创建 Azure MySQL 数据库

此步骤使用 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 创建[用于 MySQL 的 Azure 数据库](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md)实例。 请配置示例应用程序，以便在本教程后面使用该数据库。

### <a name="create-a-resource-group"></a>创建资源组

使用 [`az group create`](/cli/azure/group#az_group_create) 命令创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理相关资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在北欧区域创建资源组：

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

若要查看可以用于 `--location` 的可能值，请使用 [`az appservice list-locations`](/cli/azure/appservice#list-locations) 命令。

### <a name="create-a-mysql-server"></a>创建 MySQL 服务器

在 Cloud Shell 中，使用 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 命令在 Azure Database for MySQL 中创建一个服务器。

在下列命令中，用唯一的服务器名称替换 *\<mysql_server_name>* 占位符，用用户名替换 *\<admin_user>* 占位符，并用密码替换 *\<admin_password>* 占位符。 此服务器名称将用作 PostgreSQL 终结点 (`https://<mysql_server_name>.mysql.database.azure.com`) 的一部分，因此需要在 Azure 中的所有服务器之间保持唯一。

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>--location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> 因为本教程中考虑多个凭据，为避免混淆，`--admin-user` 和 `--admin-password` 设置为虚构值。 在生产环境中，为 Azure 中的 MySQL 服务器选择适当的用户名和密码时，请遵循安全最佳做法。
>
>

创建 MySQL 服务器后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
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

在 Cloud Shell 中，使用 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) 命令创建 MySQL 服务器的防火墙规则，以便建立客户端连接。 若同时将起始 IP 和结束 IP 设置为 0.0.0.0，防火墙将仅对其他 Azure 资源开启。 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> 甚至可以让防火墙规则更严格，即[只使用应用所使用的出站 IP 地址](app-service-ip-addresses.md#find-outbound-ips)。
>

## <a name="configure-the-azure-mysql-database"></a>配置 Azure MySQL 数据库

在本地终端窗口中，连接到 Azure 中的 MySQL 服务器。 使用前面为 &lt;mysql_server_name> 指定的值。 出现输入密码的提示时，请使用在 Azure 中创建数据库时指定的密码。

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>创建数据库 

在 `mysql` 提示符下，创建待办事项数据库和表。

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>创建具有权限的用户

创建一个数据库用户并向其授予 `tododb` 数据库中的所有特权。 将占位符 `<Javaapp_user>` 和 `<Javaapp_password>` 替换成自己的唯一应用名称。

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

键入 `quit` 退出服务器连接。

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>将示例部署到 Azure 应用服务

使用 [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) CLI 命令通过**免费**定价层创建 Azure 应用服务计划。 appservice 计划定义用于托管应用的物理资源。 分配到 appservice 计划的所有应用程序共享这些资源，因此在托管多个应用时可以节省成本。 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

计划就绪后，Azure CLI 显示与以下示例类似的输出：

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>创建 Azure Web 应用

在 Cloud Shell 中，使用 [`az webapp create`](/cli/azure/appservice/web#az_appservice_web_create) CLI 命令在 `myAppServicePlan` 应用服务计划中创建 Web 应用定义。 Web 应用定义提供了一个用于访问应用程序的 URL，并配置了多个将代码部署到 Azure 的选项。 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

将占位符 `<app_name>` 替换为你自己的唯一应用名称。 该唯一名称是 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 可以先将任何自定义域名条目映射到 Web 应用，然后向用户公开该条目。

在 Web 应用定义就绪后，Azure CLI 将显示类似于以下示例的信息： 

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

### <a name="configure-java"></a>配置 Java 

在 Cloud Shell 中，使用 [`az webapp config set`](/cli/azure/webapp/config#az_webapp_config_set) 命令，设置应用所需的 Java 运行时配置。

以下命令配置的 Web 应用可在最新的 Java 8 JDK 和 [Apache Tomcat](http://tomcat.apache.org/) 8.0 上运行。

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>将应用配置为使用 Azure SQL 数据库

在运行示例应用之前，请在 Web 应用上设置应用程序设置，以便使用在 Azure 中创建的 Azure MySQL 数据库。 这些属性以环境变量的形式公开给 Web 应用程序，并替代在已打包 Web 应用的 application.properties 中设置的值。 

在 Cloud Shell 的 CLI 中使用 [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings) 设置应用程序设置：

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>获取 FTP 部署凭据 
可以通过不同的方法将应用程序部署到 Azure appservice，包括 FTP、本地 Git、 GitHub、Visual Studio Team Services 和 BitBucket。 就本示例来说，请通过 FTP 将此前在本地计算机上生成的 .WAR 文件部署到 Azure 应用服务。

若要确定要在 ftp 命令中将哪些凭据传递给 Web 应用，请在 Cloud Shell 中使用 [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) 命令： 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>使用 FTP 上传应用

使用最喜欢的 FTP 工具将 .WAR 文件部署到 /site/wwwroot/webapps 文件夹，该文件夹所在的服务器地址是在以前的命令中从 `URL` 字段提取的。 删除现有的默认 (ROOT) 应用程序目录，将现有的 ROOT.war 替换为在本教程前面生成的 .WAR 文件。

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>测试 Web 应用

浏览到 `http://<app_name>.azurewebsites.net/` 并在列表中添加一些任务。 

![在 Azure appservice 中运行的 Java 应用](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**祝贺你！** 你的数据驱动的 Java 应用正在 Azure 应用服务中运行。

## <a name="update-the-app-and-redeploy"></a>更新应用并重新部署

更新应用程序，在待办事项列表中针对所创建项的日期添加另一列。 Spring Boot 负责更新数据库架构，因为数据模型在更改时不会更改现有的数据库记录。

1. 在本地系统上，打开 src/main/java/com/example/fabrikam/TodoItem.java 并将以下 import 添加到类：   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. 将 `String` 属性 `timeCreated` 添加到 src/main/java/com/example/fabrikam/TodoItem.java，使用创建对象时的时间戳将其初始化。 在编辑此文件时，为新的 `timeCreated` 属性添加 getters/setters。

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. 更新 src/main/java/com/example/fabrikam/TodoDemoController.java，使用 `updateTodo` 方法中的一行来设置时间戳：

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. 在 `Thymeleaf` 模板中添加对新字段的支持。 更新 src/main/resources/templates/index.html，对时间戳使用新的表头，并在每个表数据行中使用新字段来显示时间戳的值。

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. 重新生成应用程序：

    ```bash
    mvnw clean package 
    ```

6. 像以前一样通过 FTP 上传更新的 .WAR，删除现有的 site/wwwroot/webapps/ROOT 目录和 ROOT.war，然后将更新的 .WAR 文件作为 ROOT.war 上传。 

刷新应用后，即可看到“创建时间”列。 添加新任务时，应用会自动填充时间戳。 现有的任务保持不变且适用于应用，即使基础数据模型已更改。 

![Java 应用更新了一个新列](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>流式传输诊断日志 

当 Java 应用程序在 Azure 应用服务中运行时，可将控制台日志通过管道直接传输到终端。 如此，可以获得相同的诊断消息，以便调试应用程序错误。

若要启动日志流式处理，请在 Cloud Shell 中使用 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) 命令。

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 [Azure 门户](https://portal.azure.com)查看已创建的 Web 应用。

从左侧菜单中单击“应用服务”，并单击 Azure Web 应用的名称。

![在门户中导航到 Azure Web 应用](./media/app-service-web-tutorial-java-mysql/access-portal.png)

默认情况下，Web 应用页显示“概览”页。 在此页中可以查看应用的运行状况。 在此处还可以执行管理任务，例如停止、启动、重启和删除。 该页左侧的选项卡显示可以打开的不同配置页。

![Azure 门户中的应用服务页](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

页面中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：
* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

## <a name="clean-up-resources"></a>清理资源

如果不需要将这些资源用于其他教程（请参阅[后续步骤](#next)），则可通过在 Cloud Shell 中运行以下命令将其删除： 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

> [!div class="checklist"]
> * 在 Azure 中创建 MySQL 数据库
> * 将示例 Java 应用连接到 MySQL
> * 将应用部署到 Azure
> * 更新并重新部署应用
> * 从 Azure 流式传输诊断日志
> * 在 Azure 门户中管理应用

转到下一教程，了解如何向应用映射自定义 DNS 名称。

> [!div class="nextstepaction"] 
> [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)
