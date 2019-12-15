---
title: Azure Spring Cloud 简介
description: 了解 Azure Spring Cloud 的功能和优势，以便在 Azure 中部署和管理 Java Spring 应用程序。
author: jpconnock
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: jeconnoc
ms.openlocfilehash: 87bcea5e9ada4f095896c1ebe4cbe1928f158eb0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895419"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud 是什么？

借助 Azure Spring Cloud，可以轻松地将基于 Spring Boot 的微服务应用程序部署到 Azure，不需更改任何代码。  Azure Spring Cloud 管理 Spring Cloud 应用程序的生命周期，因此开发人员可以专注于其代码。  Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

Azure Spring Cloud 是 Azure 生态系统的一部分，用户可以通过它轻松绑定到其他 Azure 服务，其中包括存储、数据库、监视等。

Azure Spring Cloud 目前以公共预览版的形式提供。 使用公共预览版产品/服务，客户可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。  有关预览期间支持的详细信息，请参阅[常见问题解答](https://azure.microsoft.com/support/faq/)或提交[支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。

若要开始，请通过 [Azure CLI](spring-cloud-quickstart-launch-app-cli.md)、[Azure 门户](spring-cloud-quickstart-launch-app-portal.md)或 [Maven](spring-cloud-quickstart-launch-app-maven.md) 完成 Spring Cloud 快速入门。

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。

## <a name="application-configuration"></a>应用程序配置

### <a name="spring-cloud-config-server"></a>Spring Cloud 配置服务器

Azure Spring Cloud 配置服务器为分布式系统中的外部化配置提供服务器和客户端支持。  使用配置服务器可在一个中心位置管理所有环境中的应用程序属性。  有关详细信息，请访问 [Spring Cloud 配置服务器参考](https://spring.io/projects/spring-cloud-config.md)并完成本教程。

### <a name="enable-bluegreen-deployments"></a>启用蓝/绿部署

Azure Spring Cloud 支持用于发布和更新代码的蓝/绿部署（部署到生产环境）。  利用此更改管理模式，开发人员可以放心地实现功能和代码更改，必要时可以立即回退。  Azure 可以管理多个生产环境，并且可以轻松地更新或回退代码更改，不会中断应用程序，让开发人员可以专注于代码编写。  若要详细了解过渡环境和蓝/绿部署，请参阅此[操作指南文章](spring-cloud-howto-staging-environment.md)。

### <a name="automate-cicd-pipelines"></a>自动操作 CI/CD 管道

Azure Spring Cloud 提供通过 Azure CLI 与 Azure DevOps 集成的功能。  使用 Azure DevOps，可以自动集成代码并将其部署到 Spring 应用程序。  若要了解详细信息，请参阅此[文章](spring-cloud-howto-cicd.md)。

### <a name="scale-your-application"></a>缩放应用程序

使用 Azure Spring Cloud，可以轻松地在 Azure Spring Cloud 仪表板中缩放微服务。  可以根据要求上下缩放可供微服务使用的 vCPU 数和内存量。  缩放在数秒内生效，不需代码更改或重新部署。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-scale-manual.md)。

## <a name="application-monitoring"></a>应用程序监视

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>使用分布式跟踪功能和 Azure App Insights 监视应用程序

开发人员可以通过 Spring Cloud 的分布式跟踪工具调试和监视其应用程序的微服务之间的复杂互连。  通过将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](../azure-monitor/insights/insights-overview.md) 集成，Azure 可以直接在 Azure 门户中提供强大的分布式跟踪功能。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="next-steps"></a>后续步骤

- [从 CLI 启动 Spring Cloud 应用](spring-cloud-quickstart-launch-app-cli.md)
