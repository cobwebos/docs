---
title: 在 Linux 上生成 Java Web 应用 - Azure 应用服务
description: 使用 Linux 和 Azure Cosmos DB 上的 Azure 应用服务生成、部署与缩放 Spring Boot Java Web 应用。
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 792346edf1d2b2326f7f5f5f53304ceca347508d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901111"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>教程：使用 Spring 和 Azure Cosmos DB 生成 Java Web 应用

本教程将指导你完成在 Azure 上生成、配置、部署和缩放 Java Web 应用的过程。 完成本教程后，你将获得一个 [Spring Boot](https://projects.spring.io/spring-boot/) 应用程序，该应用程序可在 [Linux 上运行的 Azure 应用服务](/azure/app-service/containers)运行的 [Azure Cosmos DB](/azure/cosmos-db) 中存储数据。

![在 Azure appservice 中运行的 Java 应用](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 Cosmos DB 数据库。
> * 将示例应用连接到数据库并在本地对其进行测试
> * 将示例应用部署到 Azure
> * 从应用服务流式传输诊断日志
> * 添加更多实例以横向扩展示例应用

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 已在自己的计算机上安装 [Azure CLI](https://docs.microsoft.com/cli/azure/overview)。 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>克隆示例 TODO 应用并准备存储库

本教程使用一个示例 TODO 列表应用，该应用的 Web UI 可以调用 [Spring Data Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb) 支持的 Spring REST API。 [GitHub](https://github.com/Microsoft/spring-todo-app) 上提供了该应用的代码。 若要详细了解如何使用 Spring 和 Cosmos DB 编写 Java 应用，请参阅 [Spring Boot Starter 与 Azure Cosmos DB SQL API 教程](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db )和 [Spring Data Azure Cosmos DB 快速入门](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)。


在终端中运行以下命令，以克隆示例存储库并设置示例应用环境。

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>创建 Azure Cosmos DB

遵循以下步骤在订阅中创建 Azure Cosmos DB 数据库。 TODO 列表应用在运行时将连接到此数据库并存储自身的数据，不管你在哪个位置运行应用程序，它都会保存应用程序状态。

1. 登录到 Azure CLI；（可选）如果有多个订阅连接到登录凭据，请设置订阅。

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. 创建 Azure 资源组并记下资源组名称。

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. 创建 `GlobalDocumentDB` 类型的 Azure Cosmos DB。 Cosmos DB 的名称只能使用小写字母。 记下命令响应中的 `documentEndpoint` 字段。

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. 获取用于连接应用的 Azure Cosmos DB 密钥。 将 `primaryMasterKey` 和 `documentEndpoint` 保存在方便的位置，因为在下一步骤中需要用到。

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>配置 TODO 应用属性

在计算机上打开终端。 复制所克隆的存储库中的示例脚本文件，以便可以根据刚刚创建的 Cosmos DB 数据库自定义该文件。

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
在偏好的编辑器中编辑 `.scripts/set-env-variables.sh`，并提供 Azure Cosmos DB 连接信息。 对于应用服务 Linux 配置，请使用前面在创建 Cosmos DB 数据库时所用的相同区域 (`your-resource-group-region`) 和资源组 (`your-azure-group-name`)。 选择唯一的 WEBAPP_NAME，因为任何 Azure 部署中不能出现任何 Web 应用名称重复的情况。

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

然后运行脚本：

```bash
source .scripts/set-env-variables.sh
```
   
TODO 列表应用的 `application.properties` 中使用了这些环境变量。 properties 文件中的字段为 Spring Data 设置默认的存储库配置：

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

然后，示例应用使用从 `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` 导入的 `@Document` 注释，来设置 Cosmos DB 要存储和管理的实体类型：

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>运行示例应用

使用 Maven 运行示例。

```bash
mvn package spring-boot:run
```

输出应如下所示。

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

启动应用后，可在本地使用以下链接访问 Spring TODO 应用：[http://localhost:8080/](http://localhost:8080/)。

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

如果看到了异常而不是“已启动 TodoApplication”消息，请检查上一步骤所述的 `bash` 脚本是否正确导出了环境变量，并且是否为创建的 Azure Cosmos DB 数据库提供了正确的值。

## <a name="configure-azure-deployment"></a>配置 Azure 部署

打开 `initial/spring-boot-todo` 目录中的 `pom.xml` 文件，并添加以下[适用于 Azure 应用服务的 Maven 插件](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)配置。

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>部署到 Linux 上的应用服务

使用 `azure-webapp:deploy` Maven 目标将 TODO 应用部署到 Linux 上的 Azure 应用服务。

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

输出包含部署的应用程序的 URL（在本示例中为 `https://spring-todo-app.azurewebsites.net`）。 可将此 URL 复制到 Web 浏览器，或者在终端窗口中运行以下命令，以加载应用。

```bash
open https://spring-todo-app.azurewebsites.net
```

在地址栏中，应会看到正在使用远程 URL 运行的应用：

 ![](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="view-logs-to-troubleshoot-the-app"></a>查看日志以排查应用问题

为 Linux 上的应用服务中部署的 Java Web 应用启用日志记录：

```bash
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
  --web-server-logging filesystem
```

然后将 Web 应用日志流式传输到终端：

```bash
az webapp log tail --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME}
```

将会看到最近生成的输出行；对 TODO 应用发出新请求时，这些日志行将流式传输到控制台。 若要退出控制台，请按 Ctrl+C。

```bash
bash-3.2$ az webapp log tail --name ${WEBAPP_NAME}  --resource-group ${RESOURCEGROUP_NAME}
2018-10-28T22:50:17  Welcome, you are now connected to log-streaming service.
2018-10-28T22:44:56.265890407Z   _____                               
2018-10-28T22:44:56.265930308Z   /  _  \ __________ _________   ____  
2018-10-28T22:44:56.265936008Z  /  /_\  \___   /  |  \_  __ \_/ __ \ 
2018-10-28T22:44:56.265940308Z /    |    \/    /|  |  /|  | \/\  ___/ 
2018-10-28T22:44:56.265944408Z \____|__  /_____ \____/ |__|    \___  >
2018-10-28T22:44:56.265948508Z         \/      \/                  \/ 
2018-10-28T22:44:56.265952508Z A P P   S E R V I C E   O N   L I N U X
2018-10-28T22:44:56.265956408Z Documentation: https://aka.ms/webapp-linux
2018-10-28T22:44:56.266260910Z Setup openrc ...
2018-10-28T22:44:57.396926506Z Service `hwdrivers' needs non existent service `dev'
2018-10-28T22:44:57.397294409Z  * Caching service dependencies ... [ ok ]
2018-10-28T22:44:57.474152273Z Starting ssh service...
...
...
2018-10-28T22:46:13.432160734Z [INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
2018-10-28T22:46:13.744859424Z [INFO] TomcatWebServer - Tomcat started on port(s): 80 (http) with context path ''
2018-10-28T22:46:13.783230205Z [INFO] TodoApplication - Started TodoApplication in 57.209 seconds (JVM running for 70.815)
2018-10-28T22:46:14.887366993Z 2018-10-28 22:46:14.887  INFO 198 --- [p-nio-80-exec-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-28T22:46:14.887637695Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization started
2018-10-28T22:46:14.998479907Z [INFO] DispatcherServlet - FrameworkServlet 'dispatcherServlet': initialization completed in 111 ms

2018-10-28T22:49:20.572059062Z Sun Oct 28 22:49:20 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:25.850543080Z Sun Oct 28 22:49:25 GMT 2018 DELETE ======= /api/todolist/{4f41ab03-1b12-4131-a920-fe5dfec106ca} ======= 
2018-10-28T22:49:26.047126614Z Sun Oct 28 22:49:26 GMT 2018 GET ======= /api/todolist =======
2018-10-28T22:49:30.201740227Z Sun Oct 28 22:49:30 GMT 2018 POST ======= /api/todolist ======= Milk
2018-10-28T22:49:30.413468872Z Sun Oct 28 22:49:30 GMT 2018 GET ======= /api/todolist =======
```

## <a name="scale-out-the-todo-app"></a>横向扩展 TODO 应用

通过添加另一个辅助角色来横向扩展应用程序：

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>清理资源

如果不需要将这些资源用于其他教程（请参阅[后续步骤](#next)），则可通过在 Cloud Shell 中运行以下命令将其删除： 
  
```bash
az group delete --name your-azure-group-name
```

<a name="next"></a>

## <a name="next-steps"></a>后续步骤

[面向 Java 开发人员的 Azure](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot)，[Spring Data for Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable)，[Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) 和 [Linux 版应用服务](/azure/app-service/containers/app-service-linux-intro)。

在开发人员指南中详细了解在 Linux 上的应用服务中运行 Java 应用。

> [!div class="nextstepaction"] 
> [Linux 版应用服务中的 Java 开发指南](/azure/app-service/containers/app-service-linux-java)
