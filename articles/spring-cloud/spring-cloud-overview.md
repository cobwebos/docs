---
title: Azure Spring Cloud 简介
description: 了解 Azure Spring Cloud 的功能和优势，以便在 Azure 中部署和管理 Java Spring 应用程序。
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255233"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud 是什么？

借助 Azure Spring Cloud，可以轻松地将基于 Spring Boot 的微服务应用程序部署到 Azure，不需更改任何代码。  Azure Spring Cloud 管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。  Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

Azure Spring Cloud 是 Azure 生态系统的一部分，用户可以通过它轻松绑定到其他 Azure 服务，其中包括存储、数据库、监视等。

本简介介绍 Azure Spring Cloud Config Server，说明如何启用蓝/绿部署，缩放应用，以及如何监视应用性能。

## <a name="spring-cloud-config-server"></a>Spring Cloud 配置服务器

Azure Spring Cloud 配置服务器为分布式系统中的外部化配置提供服务器和客户端支持。  Azure Spring Cloud Config Server 是一个中心位置，可在其中管理所有环境中的应用程序属性。 有关详细信息，请参阅 [Spring Cloud Config Server 参考](https://spring.io/projects/spring-cloud-config.md)。 

## <a name="enable-bluegreen-deployments"></a>启用蓝/绿部署

Azure Spring Cloud 支持用于发布和更新代码的蓝/绿部署（部署到生产环境）。  利用这种变更管理模式，开发人员可以放心地实现功能和代码更改，必要时可以立即回退。  开发人员可以在多个生产环境中专注于编写代码，在不中断应用程序的情况下更新或回退代码更改。  若要详细了解过渡环境和蓝/绿部署，请参阅此[操作指南文章](spring-cloud-howto-staging-environment.md)。

## <a name="automate-cicd-pipelines"></a>自动操作 CI/CD 管道

Azure Spring Cloud 提供通过 Azure CLI 与 Azure DevOps 集成的功能。  使用 Azure DevOps，可以自动集成代码并将其部署到 Spring 应用程序。  若要了解详细信息，请参阅此[文章](spring-cloud-howto-cicd.md)。

## <a name="scale-your-application"></a>缩放应用程序

使用 Azure Spring Cloud，可以轻松地在 Azure Spring Cloud 仪表板中缩放微服务。  可以根据要求上下缩放可供微服务使用的 vCPU 数和内存量。  缩放在数秒内生效，不需代码更改或重新部署。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-scale-manual.md)。

## <a name="application-monitoring"></a>应用程序监视

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>使用分布式跟踪功能和 Azure App Insights 监视应用程序

开发人员可以通过 Spring Cloud 的分布式跟踪工具调试和监视应用程序中微服务之间的复杂互连。  通过将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](../azure-monitor/insights/insights-overview.md) 集成，Azure 可以直接在 Azure 门户中提供强大的分布式跟踪功能。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="next-steps"></a>后续步骤
若要开始，请完成 Spring Cloud 快速入门：
> [!div class="nextstepaction"]
> [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)

GitHub 中提供了更多示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)。
