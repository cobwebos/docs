---
title: 使用 Azure Spring Boot Actuator 管理和监视应用
description: 了解如何通过春季 Boot 制动器管理和监视应用。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904286"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>使用 Azure Spring Boot Actuator 管理和监视应用

本文适用于：✔️ Java

将新的二进制文件部署到应用后，可能需要检查功能并查看有关正在运行的应用程序的信息。 本文介绍如何从 Azure 春季云中提供的测试终结点访问 API，并公开应用的生产就绪功能。

## <a name="prerequisites"></a>必备条件
本文假设你有一个春季 Boot 2.x 应用程序，该应用程序可以在 Azure 春季云服务上成功部署和启动。  请参阅 [快速入门：使用 Azure 门户启动现有的 Azure 春季云应用程序](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>通过测试终结点验证应用
1. 请在 **应用程序仪表板** 中，单击你的应用程序以进入应用程序概述页。

1. 在 " **概述** " 窗格中，应会看到 " **测试终结点**"。  从命令行或浏览器访问此终结点，并观察 API 响应。

1. 请注意将在即将推出的部分中使用的 **测试终结点** URI。

>[!TIP]
> * 如果应用返回前端页面并通过相对路径引用其他文件，请确认测试终结点以斜杠 (/) 结束。 这将确保正确加载 CSS 文件。
> * 如果从浏览器查看 API，且浏览器要求你输入登录凭据以查看页面，请使用 [URL 解码](https://www.urldecoder.org/) 对测试终结点进行解码。 Url 解码返回格式为 "https:// \<username> ： \<password> @ \<cluster-name> . test.azureapps.io/ \<app-name> / \<deployment-name> " 的 url。  使用此窗体可访问终结点。

## <a name="add-actuator-dependency"></a>添加传动装置依赖关系

若要将制动器添加到基于 Maven 的项目，请添加 "Starter" 依赖关系：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

编译新的二进制文件并将其部署到应用。

## <a name="enable-production-ready-features"></a>启用生产就绪功能
传动终结点用于监视应用程序和与应用程序进行交互。 默认情况下，弹簧 Boot 应用程序公开 `health` 和 `info` 终结点以显示任意应用程序信息和运行状况信息。

若要观察配置和可配置环境，还需要启用 `env` 和 `configgrops` 终结点。

1. 单击 "应用 **概述** " 窗格，单击 " **配置** "，然后在 "设置" 菜单中，选择 " **环境变量** " "配置" 页。
1. 添加以下属性，如 "键：值" 形式。 此环境将打开弹簧传动片 "环境"、"运行状况"、"信息"。

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. 单击 " **保存** " 按钮，应用程序将自动重新启动并加载新的环境变量。

你现在可以返回到应用概述窗格，并等待设置状态更改为 "成功"。  将有多个正在运行的实例。

> [!Note] 
> 将应用公开到公共应用后，还会将这些传动装置公开到公共。 可以通过删除环境变量来隐藏所有终结点 `management.endpoints.web.exposure.include` ，并将 `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>查看制动器终结点以查看应用程序信息
1. 你现在可以访问该 url `"<test-endpoint>/actuator/"` ，以查看由弹簧 Boot 制动器公开的所有终结点。
1. 访问 url `"<test-endpoint>/actuator/env"` ，可以看到应用使用的活动配置文件以及所有已加载的环境变量。
1. 如果要搜索特定环境，可以通过访问 url  `"<test-endpoint>/actuator/env/{toMatch}"` 来查看该环境。

若要查看内置的所有终结点，请参阅 [公开终结点](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>后续步骤

* [了解 Azure 春季云的指标](spring-cloud-concept-metrics.md)
* [了解 Azure Spring Cloud 中的应用状态](spring-cloud-concept-app-status.md)

