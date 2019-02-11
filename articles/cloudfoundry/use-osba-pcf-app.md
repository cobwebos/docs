---
title: 将 Open Service Broker for Azure 数据库与 Pivotal Cloud Foundry 上的应用程序配合使用
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: 介绍如何配置 Pivotal Cloud Foundry 应用程序，以便使用 Open Service Broker for Azure 数据库
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258853"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>教程：将 Open Service Broker for Azure 数据库与 Pivotal Cloud Foundry 上的应用程序配合使用

将 Open Service Broker for Azure 与 Pivotal Cloud Foundry 实例配合使用即可在 Azure 中直接通过 Cloud Foundry CLI 和 Pivotal Cloud Foundry 实例预配各种服务（例如数据库）。 也可将这些服务绑定到在 Pivotal Cloud Foundry 实例中运行的应用程序。 以这种方式将应用程序绑定到服务时，不需在应用程序中更新任何代码或配置。 本文介绍如何将适用于数据库的 Open Service Broker for Azure 服务与 Pivotal Cloud Foundry 中的应用程序配合使用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Pivotal Cloud Foundry 中准备应用程序空间。
> * 克隆 GitHub 中的示例应用程序源。
> * 准备用于部署的应用程序。
> * 部署应用程序。
> * 使用 Open Service Broker for Azure 创建数据库。
> * 将数据库绑定到应用程序。

## <a name="prerequisites"></a>先决条件

在继续操作之前，必须满足以下条件：

* [安装并配置 Pivotal Cloud Foundry](create-cloud-foundry-on-azure.md)
* 通过 Cloud Foundry 实例[安装并配置 Open Service Broker for Azure](https://network.pivotal.io/products/azure-open-service-broker-pcf)

下面是 Pivotal Cloud Foundry Ops Manager 屏幕的示例，其中已安装并配置 Open Service Broker for Azure 磁贴：

![安装了 Open Service Broker for Azure 的 Pivotal Cloud Foundry](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>在 Pivotal Cloud Foundry 中准备应用程序空间

若要将应用程序部署到 Pivotal Cloud Foundry 实例，需使用 `cf` 命令行工具登录。 此外还必须以所需组织和空间作为目标。

若要验证自己是否已登录并显示目标空间，请使用 `cf target`。 下面的示例显示用户已作为 *admin* 登录，该用户使用 *myorg* 组织并以 *dev* 空间为目标：

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

若要登录，请使用 `cf login`：

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

若要创建新组织和空间，请使用 `cf create-org` 和 `cf create-space`：

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

若要以某个空间为目标，请使用 `cf target`：

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>获取应用程序代码

若要让某个应用程序使用 Open Service Broker for Azure，该应用程序需使用数据源（例如数据库）。 在本文中，我们将使用 [Cloud Foundry 提供的 Spring Music 示例应用程序](https://github.com/cloudfoundry-samples/spring-music)来演示一个使用数据源的应用程序。

从 GitHub 克隆此应用程序，然后导航到其目录中：

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> 若要查看此应用程序的数据源，请打开 [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml) 文件。


## <a name="prepare-the-application-for-deployment"></a>准备用于部署的应用程序

在将此应用程序部署到 Pivotal Cloud Foundry 实例之前，需先生成它。 我们还会启用一些 *DEBUG* 日志记录功能，以便记录数据源连接信息，以供演示之用。

若要启用 *DEBUG* 日志记录功能，以便记录数据库连接详细信息，请将以下 yaml 属性添加到 *application.yml* 末尾：

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

示例应用程序使用 gradle 将该应用程序构建到可供 Spring Boot 运行的 jar 中。 可运行的 jar 将部署到 Pivotal Cloud Foundry 实例。 若要生成应用程序，请执行以下操作：

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>部署应用程序

使用 `cf push` 命令将此应用程序部署到 Pivotal Cloud Foundry 实例：

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

复制在 `cf push` 的输出中显示的 *routes* 中的值。 此路由是一个 URL，用于访问正在运行的应用程序。 例如：

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


部署应用程序以后，即可查看应用程序的日志，了解应用程序使用的连接 URL。 下面的命令显示应用程序的日志，并使用 `grep` 来搜索 *jdbcUrl* 配置。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

请注意，应用程序使用 *h2:mem:testdb*，这是内存中数据库。 当内存中数据库依赖项位于 classpath 上且[自动配置](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html)已启用时，系统会自动将 Spring 应用程序配置为使用内存中数据库。 示例应用程序已在 [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8) 中[配置 h2 内存中数据库依赖项](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49)，并已启用自动配置。

在浏览器中使用应用程序的路由导航到它。 路由或 URL 显示在 `cf push` 命令的输出中。

> [!TIP]
> 也可通过运行 `cf apps` 来显示应用程序的 URL。

使用浏览器导航到应用程序以后，即可与之交互：删除一些现有的专辑，并创建一些新的。 示例应用程序使用内存中数据库来保存所做的更改。 你还会注意到，应用程序已填充了一些[默认数据](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json)。 

![使用默认数据的 Spring Music 应用](media/music-app.png)

由于应用程序使用内存中数据库，因此如果应用程序重启或重新部署，则所做的更改不会保留。 若要显示更改没有保留，请在进行某些更改以后，使用 `cf restage` 来重新暂存应用程序：

```cmd
cf restage spring-music
```

将应用程序重新暂存以后，请使用同一 URL 在浏览器中导航到该应用程序。 注意所做的更改已消失，显示的是默认数据。

![使用默认数据的 Spring Music 应用](media/music-app.png)

## <a name="create-a-database"></a>创建数据库

若要通过 Open Service Broker 在 Azure 上创建持久数据库，请使用 `cf create-service` 命令。 以下命令在 Azure 中预配 PostgreSQL 数据库，所在的资源组为 *MyResourceGroup*，所在区域为 *eastus*。 有关 *resourceGroup*、*location* 以及其他特定于 Azure 的 JSON 参数的详细信息，请参阅 [PostgreSQL 模块参考文档](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision)：

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

此数据库在 Pivotal Cloud Foundry 实例中作为名为 *mypgsql* 的服务公开。 数据库完成预配（应该需要数分钟）以后，即可将其绑定到应用程序。 若要验证数据库是否已完成预配，请使用 `cf services` 命令：

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

完成预配后，服务的 *last operation* 值将是 *create succeeded*。

## <a name="bind-the-database-to-your-application"></a>将数据库绑定到应用程序

使用 `bind-service` 命令将服务绑定到应用程序。

```cmd
cf bind-service spring-music mypgsql
```

将服务绑定到应用程序以后，需将应用程序重新暂存，然后所做的更改才会生效。

```cmd
cf restage spring-music
```

应用程序[配置为使用 Spring Cloud Connector](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46)，这样 Pivotal Cloud Foundry 实例就可以在应用程序的数据源上使用[自动重新配置](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto)。 在此示例中，当应用程序重新暂存时，Pivotal Cloud Foundry 实例会自动针对数据源重新配置应用程序，让其使用绑定到的服务。

应用程序在重新暂存以后，就会使用 *mypgsql* 而不是内存中数据库来存储数据。

现在，重启和重新部署时会保留所做的更改。 也可再次查看应用程序的日志，这样就可以看到应用程序使用的连接 URL。

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

注意有两个条目：

* *h2:mem:testdb* 的原始值。
* 重新暂存应用程序时 PostgreSQL 数据库的已更新值。

若要验证数据是否已保存到 PostgreSQL 数据库，请在浏览器中导航到该应用程序，进行一些更改，然后将应用程序重新暂存：

```cmd
cf restage spring-music
```

将应用程序重新暂存以后，请使用同一 URL 在浏览器中导航到该应用程序。 注意所做的更改仍在该处。

## <a name="cleanup"></a>清理

若要断开应用程序与数据库的连接，可以使用 `cf unbind-service` 命令将其解除绑定。

```cmd
cf unbind-service spring-music mypgsql
```

与将应用程序绑定到服务类似，必须将应用程序重新暂存，然后这些更改才会生效。 此操作不影响 *mypgsql* 和应用程序，但应用程序就会开始使用内存中数据库来代替 *mypgsql*。

若要删除数据库，可以使用 `cf delete-service` 命令。 不能撤消此操作，因此在继续操作之前，请确保你需要删除数据库。

```cmd
cf delete-service mypgsql
```

若要将应用程序从 Pivotal Cloud Foundry 实例中删除，请使用以下命令：
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>后续步骤

本教程介绍如何使用 Open Service Broker for Azure 将应用程序部署到 Pivotal Cloud Foundry 并创建一个数据库。 另外还介绍了如何将数据库绑定到 Pivotal Cloud Foundry 实例中的应用程序。 若要详细了解如何将应用程序部署到 Azure 上的 Cloud Foundry，请参阅：

* [Azure 上的 Cloud Foundry](../virtual-machines/linux/cloudfoundry-get-started.md)
* [将首个应用部署到 Microsoft Azure 上的 Cloud Foundry](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)