---
title: 教程：使用 Azure Spring Cloud 的断路器仪表板
description: 了解如何使用 Azure Spring Cloud 的断路器仪表板。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.openlocfilehash: a92c5fde68227bf94342dd380250429675bd084d
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248849"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>使用 Azure Spring Cloud 的断路器仪表板
Spring [Cloud Netflix Turbine](https://github.com/Netflix/Turbine) 广泛用于聚合多个 [Hystrix](https://github.com/Netflix/Hystrix) 指标流，以便可以使用 Hystrix 仪表板在单个视图中监视流。 本教程演示如何在 Azure Spring Cloud 上使用它们。
> [!NOTE]
> Netflix Hystrix 已广泛用于许多现有的 Spring Cloud 应用，但不再处于积极开发阶段。 如果要开发新项目，请改用 Spring Cloud 断路器实现，例如 [resilience4j](https://github.com/resilience4j/resilience4j)。 与本教程中所示的 Turbine 不同，新的 Spring Cloud 断路器框架将其指标数据管道的所有实现统一到 Micrometer 中。 我们仍在 Azure Spring Cloud 中支持 Micrometer，因此本教程不会对此进行介绍。

## <a name="prepare-your-sample-applications"></a>准备示例应用程序
示例从此[存储库](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine)分叉。

将示例存储库克隆到开发环境中：
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

生成将在本教程中使用的 3 个应用程序：
* user-service：具有单个 /personalized/{id} 终结点的简单 REST 服务
* recommendation-service：具有单个 /recommendations 终结点的简单 REST 服务，并将由 user-service 调用。
* hystrix-turbine：Hystrix 仪表板服务（用于显示 Hystrix 流）和 Turbine 服务（用于聚合来自其他服务的 Hystrix 指标流）。
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>预配 Azure Spring Cloud 实例
按照[在 Azure CLI 中预配服务实例](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)过程执行操作。

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>将应用程序部署到 Azure Spring Cloud
这些应用不使用配置服务器，因此无需为 Azure Spring Cloud 设置配置服务器。  按照以下步骤进行创建和部署：
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>验证应用
所有应用都已运行并且可发现后，请从浏览器中使用路径 https://<username>-user-service.azuremicroservices.io/personalized/1 访问 `user-service`。 如果 user-service 可以访问 `recommendation-service`，则应获得以下输出。 如果网页无法运行，请刷新几次。
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>访问 Hystrix 仪表板和指标流
使用公共终结点或专用测试终结点进行验证。

### <a name="using-public-endpoints"></a>使用公共终结点
从浏览器使用路径 `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` 访问 hystrix-turbine。  下图显示在此应用中运行的 Hystrix 仪表板。

![Hystrix 仪表板](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

将 Turbine 流 URL `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` 复制到文本框中，然后单击“监视流”。  此时将显示仪表板。 如果查看器中未显示任何内容，请点击 `user-service` 终结点以生成流。

![Hystrix 流](media/spring-cloud-circuit-breaker/hystrix-stream.png)现在，你可以体验断路器仪表板。
> [!NOTE] 
> 在生产环境中，Hystrix 仪表板和指标流不应向 Internet 公开。

### <a name="using-private-test-endpoints"></a>使用专用测试终结点
也可以从 `test-endpoint` 访问 Hystrix 指标流。 作为后端服务，我们没有为 `recommendation-service` 分配公共终结点，但我们可以在 `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream` 使用 test-endpoint 显示其指标

![Hystrix test-endpoint 流](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

作为 Web 应用，Hystrix 仪表板应在 `test-endpoint` 上正常工作。 如果无法正常工作，则可能有两个原因：第一，使用 `test-endpoint` 从 `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` 更改基 URL，或者，第二，Web 应用将绝对路径用于静态资源。 若要使其在 `test-endpoint` 上正常工作，可能需要手动编辑前端文件中的 <base>。

## <a name="next-steps"></a>后续步骤
* [在 Azure CLI 中预配服务实例](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#provision-a-service-instance-on-the-azure-cli)
* [准备要部署到 Azure Spring Cloud 中的 Java Spring 应用程序](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
