---
title: 为 Linux 版 Azure 应用服务配置 Java APM 和监视工具
description: 了解如何将在 Linux 版应用服务上运行的 Java 应用程序的日志和指标发送到 NewRelic 和 App Dynamics APM 提供程序
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 11/27/2018
ms.author: astay;routlaw
ms.openlocfilehash: 06ae71fea1b85a74d87588d2635038c64c8540cc
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52576948"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>操作说明：对 Linux 版 Azure 应用服务上的 Java 应用使用应用程序性能监视工具

本文展示了如何使用 NewRelic 和 AppDynamics 应用程序性能监视 (APM) 平台连接 Linux 版 Azure 应用服务上部署的 Java 应用程序。

## <a name="configure-new-relic"></a>配置 NewRelic
1. 在 [NewRelic.com](https://newrelic.com/signup) 上创建一个 NewRelic 帐户
1. 从 NewRelic 下载 Java 代理，它将具有类似于 `newrelic-java-x.x.x.zip` 的文件名。
1. 复制你的许可证密钥，稍后需要使用它来配置代理。
1. [通过 SSH 登录到应用服务实例](/azure/app-service/containers/app-service-linux-ssh-support)并创建一个新目录 `/home/site/wwwroot/apm`。 
1. 将解压缩的 NewRelic Java 代理文件上传到 `/home/site/wwwroot/apm` 下的一个目录中。 你的代理的文件应当位于 `/home/site/wwwroot/apm/newrelic` 中。
1. 修改位于 `/home/site/wwwroot/apm/newrelic/newrelic.yml` 处的 YAML 文件并将占位符许可证值替换为你自己的许可证密钥。
1. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你的应用使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` 的环境变量。
    - 如果你已有 `JAVA_OPTS` 或 `CATALINA_OPTS` 的环境变量，请将 `javaagent` 选项追加到当前值的末尾。

## <a name="configure-appdynamics"></a>配置 AppDynamics
1. 在 [AppDynamics.com](https://www.appdynamics.com/community/register/) 上创建一个 AppDynamics 帐户
1. 从 AppDynamics 网站下载 Java 代理，文件名将类似于 `AppServerAgent-x.x.x.xxxxx.zip`
1. [通过 SSH 登录到应用服务实例](/azure/app-service/containers/app-service-linux-ssh-support)并创建一个新目录 `/home/site/wwwroot/apm`。 
1. 将 Java 代理文件上传到 `/home/site/wwwroot/apm` 下的一个目录中。 你的代理的文件应当位于 `/home/site/wwwroot/apm/appdynamics` 中。
1. 在 Azure 门户中，浏览到你在应用服务中的应用程序并创建一个新的应用程序设置。
    - 如果你使用的是 **Java SE**，请创建一个名为 `JAVA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` 的环境变量，其中，`<YOUR_SITE_NAME>` 是你的应用服务名称。
    - 如果你使用的是 **Tomcat**，请创建一个名为 `CATALINA_OPTS` 且值为 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` 的环境变量，其中，`<YOUR_SITE_NAME>` 是你的应用服务名称。
