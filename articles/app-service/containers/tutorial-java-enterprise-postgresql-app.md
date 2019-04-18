---
title: 在 Linux 上构建 Java Enterprise Web 应用 - Azure 应用服务 | Microsoft Docs
description: 了解如何在基于 Linux 的 Azure 应用服务的 Wildfly 中运行 Java Enterprise 应用。
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: 472ff85adaf72f91948c4072b12cca3ff8e59f37
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545344"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>教程：在 Azure 中生成 Java EE 和 Postgres Web 应用

本教程介绍如何在 Azure 应用服务中创建 Java Enterprise Edition Web 应用，并将其连接到 Postgres 数据库。 完成本教程后，即可使用 [WildFly](https://www.wildfly.org/about/) 应用程序将数据存储到[用于 Postgres 的 Azure 数据库](https://azure.microsoft.com/services/postgresql/)，后者运行在 [Linux 上的 Azure 应用服务](app-service-linux-intro.md)中。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 使用 Maven 将 Java EE 应用部署到 Azure
> * 在 Azure 中创建一个 Postgres 数据库
> * 将 WildFly 服务器配置为使用 Postgres
> * 更新并重新部署应用
> * 在 WildFly 上运行单元测试

## <a name="prerequisites"></a>先决条件

1. [下载并安装 Git](https://git-scm.com/)
2. [下载并安装 Maven 3](https://maven.apache.org/install.html)
3. [下载并安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>克隆并编辑示例应用

在此步骤中，需克隆示例应用程序并配置 Maven 项目对象模型（POM 或 pom.xml），以便进行部署。

### <a name="clone-the-sample"></a>克隆示例

在终端窗口中导航到工作目录，然后克隆[示例存储库](https://github.com/Azure-Samples/wildfly-petstore-quickstart)。

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>更新 Maven POM

使用应用服务的所需名称和资源组更新 Maven Azure 插件。 不需预先创建应用服务计划或实例。 Maven 插件将创建资源组和应用服务（如果尚不存在）。 

可以向下滚动到 _pom.xml_ 的 `<plugins>` 节（第 200 行）来进行更改。 

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```
将 `YOUR_APP_NAME` 和 `YOUR_RESOURCE_GROUP` 替换为你的应用服务和资源组的名称。

## <a name="build-and-deploy-the-application"></a>生成并部署应用程序

现在将使用 Maven 来生成应用程序并将其部署到应用服务。

### <a name="build-the-war-file"></a>生成 .war 文件

将对此项目中的 POM 进行配置，以便将应用程序打包到 Web 存档 (WAR) 文件中。 使用 Maven 生成应用程序：

```bash
mvn clean install -DskipTests
```

此应用程序中的测试用例经过设计，可以在将应用程序部署到 WildFly 中后运行。 我们将跳过在本地生成所需的测试，请在应用程序部署到应用服务中以后运行测试。

### <a name="deploy-to-app-service"></a>部署到应用服务

WAR 就绪以后，即可使用部署到应用服务所需的 Azure 插件：

```bash
mvn azure-webapp:deploy
```

部署完成以后，请继续下一步。

### <a name="create-a-record"></a>创建记录

打开浏览器并导航到 `https://<your_app_name>.azurewebsites.net/` 。 恭喜！Java EE 应用程序已部署到 Azure 应用服务。

此时，应用程序使用的是内存中 H2 数据库。 单击导航栏中的“管理员”，创建一个新类别。 如果重启应用服务实例，将会丢失内存中数据库中的记录。 需在以下步骤中修复此问题，方法是在 Azure 上预配 Postgres 数据库，然后将 WildFly 配置为使用该数据库。

![“管理员”按钮位置](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>预配 Postgres 数据库

若要预配 Postgres 数据库服务器，请打开一个终端，然后使用需要用于服务器名称、用户名、密码和位置的值运行以下命令。 使用应用服务所在的资源组。 请记下密码供以后使用！

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

浏览到门户，搜索 Postgres 数据库。 当边栏选项卡出现以后，请复制“服务器名称”和“服务器管理员登录名”值，以后需用到它们。

### <a name="allow-access-to-azure-services"></a>允许访问 Azure 服务

在 Azure 数据库边栏选项卡的“连接安全性”面板中，将“允许访问 Azure 服务”按钮切换到“启用”位置。

![允许访问 Azure 服务](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>更新用于 Postgres 的 Java 应用

现在需对 Java 应用程序进行一些更改，让其可以使用 Postgres 数据库。

### <a name="add-postgres-credentials-to-the-pom"></a>将 Postgres 凭据添加到 POM

在 _pom.xml_ 中，将大写的占位符值替换为 Postgres 服务器名称、管理员登录名和密码。 这些字段在 Azure Maven 插件中。 （请确保替换 `<value>` 标记中的 `YOUR_SERVER_NAME`、`YOUR_PG_USERNAME` 和 `YOUR_PG_PASSWORD`，而非替换 `<name>` 标记中的！）

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>更新 Java 事务 API

接下来需编辑 Java 事务 API (JPA) 配置，使 Java 应用程序与 Postgres 而不是以前使用的内存中 H2 数据库通信。 打开编辑器，转到 _src/main/resources/META-INF/persistence.xml_。 将 `<jta-data-source>` 的值替换为 `java:jboss/datasources/postgresDS`。 JTA XML 现在应该有以下设置：

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>配置 WildFly 应用程序服务器

在部署重新配置的应用程序之前，必须使用 Postgres 模块及其依赖项更新 WildFly 应用程序服务器。 [配置 WildFly 服务器](configure-language-java.md#configure-wildfly-server)一文中提供了更多配置信息。

若要配置服务器，需要使用 `wildfly_config/` 目录中的四个文件：

- **postgresql-42.2.5.jar**：此 JAR 文件是用于 Postgres 的 JDBC 驱动程序。 有关详细信息，请查看[官方网站](https://jdbc.postgresql.org/index.html)。
- **postgres-module.xml**：此 XML 文件为 Postgres 模块 (org.postgres) 声明一个名称。 它还指定使用模块所需的资源和依赖项。
- **jboss_cli_commands.cl**：此文件包含将要由 JBoss CLI 执行的配置命令。 这些命令可将 Postgres 模块添加到 WildFly 应用程序服务器、提供凭据、声明 JNDI 名称、设置超时阈值，等等。如果不熟悉 JBoss CLI，请参阅[官方文档](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)。
- **startup_script.sh**：最终，只要启动应用服务实例，就会执行此 shell 脚本。 此脚本只执行一项功能：将 `jboss_cli_commands.cli` 中的命令通过管道输送到 JBoss CLI。

强烈建议读取这些文件（尤其是 _jboss_cli_commands.cli_）的内容。

### <a name="ftp-the-configuration-files"></a>通过 FTP 传输配置文件

需将 `wildfly_config/` 的内容通过 FTP 传输到应用服务实例。 若要获取 FTP 凭据，请单击 Azure 门户的应用服务边栏选项卡上的“获取发布配置文件”按钮。 FTP 用户名和密码将出现在已下载的 XML 文档中。 有关发布配置文件的详细信息，请参阅[此文档](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials)。

使用所选 FTP 工具，将 `wildfly_config/` 中的四个文件传输到 `/home/site/deployments/tools/`。 （请注意，不应传输目录，只传输文件本身。）

### <a name="finalize-app-service"></a>完成应用服务

在应用服务边栏选项卡中，导航到“应用程序设置”面板。 在“运行时”下，将“启动文件”字段设置为 `/home/site/deployments/tools/startup_script.sh`。 这样可确保在应用服务实例创建之后且在 WildFly 服务器启动之前运行 shell 脚本。

最后，请重启应用服务。 按钮位于“概览”面板中。

## <a name="redeploy-the-application"></a>重新部署应用程序

在终端窗口中，重新生成并重新部署应用程序。

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

祝贺你！ 应用程序现在使用 Postgres 数据库，在应用程序中创建的任何记录将存储在 Postgres 中，而不是以前的 H3 内存中数据库中。 若要对此进行确认，可以进行记录并重启应用服务。 当应用程序重启时，记录会保留。

## <a name="clean-up"></a>清理

如果不需将这些资源用于其他教程（请参阅“后续步骤”），则可通过运行以下命令将其删除：

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Maven 将 Java EE 应用部署到 Azure
> * 在 Azure 中创建一个 Postgres 数据库
> * 将 WildFly 服务器配置为使用 Postgres
> * 更新并重新部署应用
> * 在 WildFly 上运行单元测试

继续学习下一篇教程，了解如何将自定义 DNS 名称映射到应用。

> [!div class="nextstepaction"]
> [教程：将自定义 DNS 名称映射到应用](../app-service-web-tutorial-custom-domain.md)

或者，查看其他资源：

> [!div class="nextstepaction"]
> [配置 Java 应用](configure-language-java.md)
