---
title: 有关在 Spring Boot 应用中使用功能标志的教程 - Azure 应用程序配置 | Microsoft Docs
description: 本教程介绍如何在 Spring Boot 应用中实施功能标志。
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: d924975d852320fcddd5ae988f1d52f10d366f81
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790739"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>教程：在 Spring Boot 应用中使用功能标志

Spring Boot Core 功能管理库支持在 Spring Boot 应用程序中实施功能标志。 使用这些库能够以声明方式将功能标志添加到代码。

功能管理库还会在幕后管理功能标志生命周期。 例如，这些库可刷新和缓存标志状态，或者保证标志状态在请求调用期间保持不变。 此外，Spring Boot 库还提供集成，包括 MVC 控制器操作、路由和中间件。

[将功能标志添加到 Spring Boot 应用快速入门](./quickstart-feature-flag-spring-boot.md)介绍了在 Spring Boot 应用程序中添加功能标志的多种方法。 本教程将更详细地介绍这些方法。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 在应用程序的关键组件中添加功能标志，以控制功能可用性。
> * 使用应用程序配置来管理功能标志时，与应用程序配置相集成。

## <a name="set-up-feature-management"></a>设置功能管理

Spring Boot 功能管理器 `FeatureManager` 从框架的本机配置系统获取功能标志。 因此，可以使用 Spring Boot 支持的任何配置源（包括本地 *bootstrap.yml* 文件或环境变量）来定义应用程序的功能标志。 `FeatureManager` 依赖于依赖项注入。 可以使用标准约定来注册功能管理服务。

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

我们建议将功能标志保留在应用程序的外部，并单独对其进行管理。 这样便可以随时修改标志状态，并使这些更改在应用程序中立即生效。 应用程序配置提供一个中心位置用于通过专用门户 UI 来组织和控制所有功能标志。 应用程序配置还直接通过其 Spring Boot 客户端库将标志传送到应用程序。

将 Spring Boot 应用程序连接到应用程序配置的最简单方法是使用配置提供程序：

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>功能标志声明

每个功能标志有两个组成部分：一个名称，以及用于评估功能状态是否为“打开”（即，其值为 `True`）的一个或多个筛选器的列表。  筛选器定义有关何时应启用某个功能的用例。

如果某个功能标志包含多个筛选器，则会按顺序遍历筛选器列表，直到其中一个筛选器确定应启用该功能。 此时，该功能标志为“打开”，并会跳过所有剩余的筛选器结果。  如果没有任何筛选器指示应启用该功能，则表示该功能标志为“关闭”。 

功能管理器支持将 *application.yml* 用作功能标志的配置源。 以下示例演示如何在 YAML 文件中设置功能标志：

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

按照约定，此 YML 文档的 `feature-management` 节用于功能标志设置。 以上示例演示了三个功能标志，其筛选器已在 `EnabledFor` 属性中定义：

* `feature-a` 状态为“打开”。 
* `feature-b` 状态为“关闭”。 
* `feature-c` 指定包含 `parameters` 属性的名为 `Percentage` 的筛选器。 `Percentage` 是可配置的筛选器。 在此示例中，`Percentage` 指定打开 `feature-c` 标志的概率为 50%。 

## <a name="feature-flag-checks"></a>功能标志检查

功能管理的基本模式是首先检查功能标志是否设置为“打开”。  如果是，则功能管理器将运行该功能包含的操作。 例如：

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>依赖项注入

在 Spring Boot 中，可以通过依赖项注入访问功能管理器 `FeatureManager`。

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>控制器操作

在 MVC 控制器中，使用 `@FeatureGate` 属性控制是否启用特定的操作。 以下 `Index` 操作要求 `feature-a` 状态为“打开”才能运行该操作： 

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

当由于控制功能标志状态为“关闭”而阻止了 MVC 控制器或操作时，将调用已注册的 `IDisabledFeaturesHandler`接口。  默认的 `IDisabledFeaturesHandler` 接口向客户端返回 404 状态代码，但不返回响应正文。

## <a name="mvc-filters"></a>MVC 筛选器

可以设置 MVC 筛选器，以根据功能标志的状态激活这些筛选器。 以下代码添加名为 `FeatureFlagFilter` 的 MVC 筛选器。 仅当已启用 `feature-a` 时，才会在 MVC 管道内部触发此筛选器。

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>路由

可以使用功能标志来重定向路由。 以下代码从启用的 `feature-a` 重定向用户：

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>后续步骤

本教程已介绍如何使用 `spring-cloud-azure-feature-management-web` 库在 Spring Boot 应用程序中实施功能标志。 有关 Spring Boot 和应用程序配置中的功能管理支持的详细信息，请参阅以下资源：

* [Spring Boot 功能标记示例代码](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [管理功能标志](./manage-feature-flags.md)
