---
title: "在 Azure 中构建 Java 和 MySQL Web 应用 | Microsoft Docs"
description: "了解如何创建一个可连接到 Azure MySQL 数据库服务的、在 Azure appservice 中运行的 Java 应用。"
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: 
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/06/2017
ms.author: bbenz
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: fab7759154b38b8043a17c933f896d7af9514c85
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="build-a-java-and-mysql-web-app-in-azure"></a>在 Azure 中构建 Java 和 MySQL Web 应用
本教程介绍如何在 Azure 中创建一个可连接到 MySQL 数据库的 Java Web 应用。 第一步是将应用程序克隆到本地计算机，然后让它在本地 MySQL 实例上运行。
下一步是为 Java 应用和 MySQL 设置 Azure 服务，然后将应用程序部署到 Azure appservice。
完成本教程后，你将获得一个在 Azure 上运行的、可连接到 Azure MySQL 数据库服务的待办事项列表应用程序。

![在 Azure appservice 中运行的 Java 应用](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

## <a name="before-you-begin"></a>开始之前

运行本示例之前，请在本地安装以下必备组件：

1. [下载并安装 git](https://git-scm.com/)
1. [下载并安装 Java 7 或更高版本](http://Java.net/downloads.Java)
1. [下载并安装 Maven](https://maven.apache.org/download.cgi)
1. [下载、安装并启动 MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 
1. [下载和安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prepare-local-mysql-database"></a>准备本地 MySQL 数据库

此步骤在本地 MySQL 服务器供中创建一个数据库，以便在本教程中使用。

### <a name="connect-to-mysql-server"></a>连接到 MySQL 服务器
从命令行连接到本地 MySQL 服务器：

```bash
mysql -u root -p
```

如果命令成功运行，则表示你的 MySQL 服务器已在运行。 否则，请确保遵循 [MySQL 安装后步骤](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)启动本地 MySQL 服务器。

当系统提示输入密码时，请输入 `root` 帐户的密码。 如果你不记得自己的 root 帐户密码，请参阅 [MySQL：如何重置 Root 密码](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)。


### <a name="create-a-database-and-table"></a>创建数据库和表

在 `mysql` 提示符下，创建待办事项数据库和表。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

键入 `quit` 退出服务器连接。

```sql
quit
```

## <a name="create-local-java-application"></a>创建本地 Java 应用程序
此步骤克隆 GitHub 存储库，配置 MySQL 数据库连接，然后在本地运行应用。 

### <a name="clone-the-sample"></a>克隆示例

通过命令提示符导航到工作目录。  

运行下列命令以克隆示例存储库。 

```bash
git clone https://github.com/bbenz/azure-mysql-java-todo-app
```

接下来，遵循存储库自述文件中的步骤设置 lombok.jar。


### <a name="configure-mysql-connection"></a>配置 MySQL 连接

此应用程序使用 Maven Jetty 插件在本地运行应用程序并连接到 MySQL 数据库。
若要访问本地 MySQL 实例，请在 WebContent/WEB-INF/jetty-env.xml 中设置本地 MySQL 用户 ID 和密码。

使用本地 MySQL 实例的用户 ID 和密码更新 User 和 Password 值：

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql://localhost:3306/itemdb</Set>
           <Set name="User">root</Set>
           <Set name="Password"></Set>
        </New>
     </Arg>
    </New>
</Configure>

```

&gt; [!NOTE]
&gt;有关 Jetty 如何使用 `jetty-env.xml` 文件的信息，请参阅 [Jetty XML 参考](http://www.eclipse.org/jetty/documentation/9.4.x/jetty-env-xml.html)。
&gt; &gt;

### <a name="run-the-sample"></a>运行示例

使用 Maven 命令运行示例： 

```bash
mvn package jetty:run
```

接下来，在浏览器中导航到 `http://localhost:8080`。 在页面中添加一些任务。

若要随时停止应用程序，请在命令提示符下键入 `Ctrl`+`C`。 

## <a name="create-a-mysql-database-in-azure"></a>在 Azure 中创建 MySQL 数据库

此步骤在[用于 MySQL 的 Azure 数据库（预览版）](/azure/mysql)中创建一个 MySQL 数据库。 稍后需要将 Java 应用程序配置为连接到此数据库。

### <a name="log-in-to-azure"></a>登录 Azure

在终端窗口中使用 Azure CLI 2.0 创建所需的资源用于在 Azure appservice 中托管 Java 应用程序。 使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。 

```azurecli 
az login 
``` 

### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建[资源组](../azure-resource-manager/resource-group-overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在北欧区域创建资源组：

```azurecli
az group create --name myResourceGroup  --location "North Europe"
```

若要查看 `--location` 的可用值，请使用 [az appservice list-locations](/cli/azure/appservice#list-locations) 命令。

### <a name="create-a-mysql-server"></a>创建 MySQL 服务器

使用 [az mysql server create](/cli/azure/mysql/server#create) 命令在用于 MySQL 的 Azure 数据库（预览版）中创建一个服务器。

将出现的 `&lt;mysql_server_name&gt;` 占位符替换为你自己的唯一 MySQL 服务器名称。 此名称是 MySQL 服务器主机名 `&lt;mysql_server_name&gt;.database.windows.net` 的一部分，因此必须全局唯一。 此外，请将 `&lt;admin_user&gt;` 和 `&lt;admin_password&gt;` 替换为你自己的值。

```azurecli
az mysql server create --name &lt;mysql_server_name&gt; --resource-group myResourceGroup --location "North Europe" --user &lt;admin_user&gt; --password &lt;admin_password&gt;
```

创建 MySQL 服务器后，Azure CLI 将显示类似于以下示例的信息：

```json
{
  "administratorLogin": "&lt;admin_user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql_server_name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql_server_name&gt;",
  "location": "northeurope",
  "name": "&lt;mysql_server_name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-a-server-firewall"></a>配置服务器防火墙

使用 [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#create) 命令创建 MySQL 服务器的防火墙规则，以便能够建立客户端连接。 

```azurecli
az mysql server firewall-rule create --name allIPs --server &lt;mysql_server_name&gt; --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

&gt; [!NOTE]
&gt;用于 MySQL 的 Azure 数据库（预览版）尚不支持仅通过 Azure 服务建立连接。 由于 Azure 中的 IP 地址是动态分配的，因此最好是现在就启用所有 IP 地址。 该服务目前以预览版提供，我们即将推出保护数据库的更好方法。
&gt; &gt;

### <a name="connect-to-the-mysql-server"></a>连接到 MySQL 服务器

在终端窗口中，连接到 Azure 中的 MySQL 服务器。 使用前面为 `&lt;admin_user&gt;` 和 `&lt;mysql_server_name&gt;` 指定的值。

```bash
mysql -u &lt;admin_user&gt;@&lt;mysql_server_name&gt; -h &lt;mysql_server_name&gt;.database.windows.net -P 3306 -p
```

### <a name="create-a-database-and-table-in-the-azure-mysql-service"></a>在 Azure MySQL 服务中创建数据库和表

在 `mysql` 提示符下，创建待办事项数据库和表。

```sql
CREATE DATABASE todoItemDb;
USE todoItemDb;
CREATE TABLE ITEMS ( id varchar(255), name varchar(255), category varchar(255), complete bool);
```

### <a name="create-a-user-with-permissions"></a>创建具有权限的用户

创建一个数据库用户并向其授予 `todoItemDb` 数据库中的所有特权。 将占位符 `&lt;Javaapp_user&gt;` 和 `&lt;Javaapp_password&gt;` 替换为你自己的唯一应用名称。

```sql
CREATE USER '&lt;Javaapp_user&gt;' IDENTIFIED BY '&lt;Javaapp_password&gt;'; 
GRANT ALL PRIVILEGES ON todoItemDb.* TO '&lt;Javaapp_user&gt;';
```

键入 `quit` 退出服务器连接。

```sql
quit
```

### <a name="configure-the-local-mysql-connection-with-the-new-azure-mysql-service"></a>使用新的 Azure MySQL 服务配置本地 MySQL 连接

此步骤将 Java 应用程序连接到在用于 MySQL 的 Azure 数据库（预览版）中创建的 MySQL 数据库。 

若要从本地应用程序访问 Azure MySQL 服务，请在 WebContent/WEB-INF/jetty-env.xml 中设置新的 MySQL 终结点、用户 ID 和密码：

```
<Configure id='wac' class="org.eclipse.jetty.webapp.WebAppContext">
  <New id="itemdb" class="org.eclipse.jetty.plus.jndi.Resource">
     <Arg></Arg>
     <Arg>jdbc/todoItemDb</Arg>
     <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
           <Set name="Url">jdbc:mysql:<paste the endpoint for the Azure MySQL Service>/itemdb</Set>
           <Set name="User">Azure MySQL userID</Set>
           <Set name="Password">Azure MySQL Password</Set>
        </New>
     </Arg>
    </New>
</Configure>

```

保存所做更改。

## <a name="test-the-application"></a>测试应用程序

使用前面所用的相同 maven 命令再次在本地运行示例，但这一次是要连接到 Azure MySQL 服务： 

```bash
mvn package jetty:run
```

在浏览器中导航至 `http://localhost:8080`。 如果页面可加载且未出错，则表示 Java 应用程序正在连接到 Azure 中的 MySQL 数据库。 

请不要执行“在页面中添加一些任务”中所述的步骤。

若要随时停止应用程序，请在终端中键入 `Ctrl`+`C`。 

### <a name="secure-sensitive-data"></a>保护敏感数据

确保不要将 `WebContent/WEB-INF/jetty-env.xml` 中的敏感数据提交到 Git。

为此，请打开存储库根目录中的 `.gitignore`，并在新的代码行中添加 `WebContent/WEB-INF/jetty-env.xml`。 保存所做更改。

将更改提交到 `.gitignore`。

```bash
git add .gitignore
git commit -m "keep sensitive data in WebContent/WEB-INF/jetty-env.xml out of git"
```

## <a name="deploy-the-java-application-to-azure"></a>将 Java 应用程序部署到 Azure
接下来，我们将 Java 应用程序部署到 Azure。

### <a name="create-an-appservice-plan"></a>创建 appservice 计划

使用 [az appservice plan create](/cli/azure/appservice/plan#create) 命令创建 appservice 计划。 

&gt; [!NOTE] 
&gt;appservice 计划表示用于托管应用的物理资源集合。 分配到 appservice 计划的所有应用程序将共享该计划定义的资源，在托管多个应用时可以节省成本。 
&gt;&gt; appservice 计划定义：&gt;&gt; * 区域（北欧、美国东部、东南亚）&gt; * 实例大小（小、中、大）&gt; * 规模计数（一个、两个、三个实例，等等）&gt; * SKU（免费、共享、基本、标准、高级）&gt; 

以下示例使用“免费”定价层创建名为 `myAppServicePlan` 的 appservice 计划。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

创建 appservice 计划后，Azure CLI 将显示类似于以下示例的信息：

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
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
``` 

### <a name="create-an-azure-web-app"></a>创建 Azure Web 应用

创建 appservice 计划后，请在 `myAppServicePlan` appservice 计划中创建 Azure Web 应用。 该 Web 应用提供托管空间用于部署代码，并提供一个 URL 用于查看已部署的应用程序。 使用 [az appservice web create](/cli/azure/appservice/web#create) 命令创建该 Web 应用。 

在以下命令中，请将 `&lt;app_name&gt;` 占位符替换为你自己的唯一应用名称。 该唯一名称将用作 Web 应用的默认域名的一部分，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 稍后，可以先将任何自定义 DNS 条目映射到 Web 应用，然后向用户公开该条目。 

```azurecli
az appservice web create --name &lt;app_name&gt; --resource-group myResourceGroup --plan myAppServicePlan
```

创建 Web 应用后，Azure CLI 将显示类似于以下示例的信息： 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "&lt;app_name&gt;.azurewebsites.net",
    "&lt;app_name&gt;.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "&lt;app_name&gt;.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    &lt; JSON data removed for brevity. &gt;
}
```

### <a name="set-the-java-version-the-java-application-server-type-and-the-application-server-version"></a>设置 Java 版本、Java 应用程序服务器类型和应用程序服务器版本

使用 [az appservice web config update](/cli/azure/appservice/web/config#update) 命令设置 Java 版本、Java 应用程序服务器（容器）和容器版本。

以下命令将 Java 版本设置为 8，将 Java 应用服务器设置为 Jetty，将 Jetty 版本设置为最新的 Jetty 9.3。

```azurecli
az appservice web config update --name &lt;app_name&gt; --resource-group myResourceGroup --java-version 1.8 --java-container Jetty --java-container-version 9.3
```


### <a name="get-credentials-for-deployment-to-the-web-app-using-ftp"></a>获取使用 FTP 部署到 Web 应用所用的凭据 

可以通过不同的方法将应用程序部署到 Azure appservice，包括 FTP、本地 Git、 GitHub、Visual Studio Team Services 和 BitBucket。 本示例使用 Maven 编译 .WAR 文件，并使用 FTP 将 .WAR 文件部署到 Web 应用

若要确定要在 ftp 命令中将哪些凭据传递给 Web 应用，请使用 [az appservice web deployment list-publishing-profiles](https://docs.microsoft.com/cli/azure/appservice/web/deployment#list-publishing-profiles) 命令，如下所示： 

```azurecli

az appservice web deployment list-publishing-profiles --name &lt;app_name&gt; --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --o table

```
### <a name="compile-the-local-application-to-deply-to-the-web-app"></a>编译要部署到 Web 应用的本地应用程序 

若要准备需要在 Azure Web 应用中运行的本地 Java 应用程序，请将 Java 应用程序中的所有资源重新编译为随时可供部署的单个 .WAR 文件。 导航到 applications pom.xml 所在的目录并键入：

```bash 
mvn clean package
``` 
Maven 打包过程即将结束时，你将看到 .WAR 文件的位置。  输出应类似于：

```bash

[INFO] Processing war project
[INFO] Copying webapp resources [local-location\GitHub\mysql-java-todo-app\WebContent]
[INFO] Webapp assembled in [1519 msecs]
[INFO] Building war: C:\Users\your\localGitHub\mysql-java-todo-app\target\azure-appservice-mysql-java-sample-0.0.1-SNAPSHOT.war
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------

```

请记下 .War 文件的位置，并使用偏好的 FTP 方法将 .WAR 文件部署到 Jetty WebApps 文件夹。  请注意，Jetty WebApps 文件夹位于 Azure Web 应用中的 /site/wwwroot/webapps 下。 

### <a name="browse-to-the-azure-web-app"></a>浏览到 Azure Web 应用

浏览到 `http://&lt;app_name&gt;.azurewebsites.net/&lt;app_name&gt;` 并在列表中添加一些任务。 

![在 Azure appservice 中运行的 Java 应用](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)


**祝贺你！** 你的数据驱动的 Java 应用正在 Azure appservice 中运行。
若要更新该应用，请重复 maven clean package 命令，然后通过 FTP 重新部署该应用。

## <a name="manage-your-azure-web-app"></a>管理 Azure Web 应用

转到 Azure 门户 [https://portal.azure.com](https://portal.azure.com) 并登录以查看创建的 Web 应用。

在左侧菜单中单击“appservice”，然后单击 Azure Web 应用的名称。

现在，你应已进入 Web 应用的_边栏选项卡_（水平打开的门户页）。

默认情况下，Web 应用的边栏选项卡显示“概述”页。 在此页中可以查看应用的运行状况。 在此处还可以执行基本的管理任务，例如浏览、停止、启动、重新启动和删除。 边栏选项卡左侧的选项卡显示可以打开的不同配置页。

在“应用程序设置”页中， 

![Azure appservice Web 应用的应用程序设置](./media/app-service-web-tutorial-java-mysql/appservice-web-app-application-settings.png)



边栏选项卡中的这些选项卡显示了可添加到 Web 应用的许多强大功能。 以下列表只是列出了一部分可用的功能：

* 映射自定义 DNS 名称
* 绑定自定义 SSL 证书
* 配置持续部署
* 扩展和缩减
* 添加用户身份验证

## <a name="more-resources"></a>更多资源

- [将现有的自定义 DNS 名称映射到 Azure Web 应用](app-service-web-tutorial-custom-domain.md)
- [将现有的自定义 SSL 证书绑定到 Azure Web 应用](app-service-web-tutorial-custom-ssl.md)
- [Web 应用 CLI 脚本](app-service-cli-samples.md)</PRE>

