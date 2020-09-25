---
title: Azure Spring Cloud 简介
description: 了解 Azure Spring Cloud 的功能和优势，以便在 Azure 中部署和管理 Java Spring 应用程序。
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: f622042c9a2e8cc5bc11c8dc909d02afe38e5c78
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908369"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud 是什么？

借助 Azure Spring Cloud，可以轻松地将基于 Spring Boot 的微服务应用程序部署到 Azure，不需更改任何代码。  Azure Spring Cloud 管理 Spring Cloud 应用程序的基础结构，因此开发人员可以专注于其代码。  Spring Cloud 可以通过以下方法提供生命周期管理：综合性监视和诊断、配置管理、服务发现、CI/CD 集成、蓝绿部署等。

Azure Spring Cloud 同时支持 Java [Spring Boot](https://spring.io/projects/spring-boot) 和 ASP.NET Core [Steeltoe](https://steeltoe.io/) 应用。 Steeltoe 支持目前以公共预览版的形式提供。 使用公共预览版产品/服务，你可以在产品/服务正式发布之前体验新功能。  公共预览功能和服务并非供生产使用。  有关详细信息，请参阅[常见问题解答](https://azure.microsoft.com/support/faq/)或[支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。

Azure Spring Cloud 是 Azure 生态系统的一部分，用户可以通过它轻松绑定到其他 Azure 服务，其中包括存储、数据库、监视等。

本简介介绍 Azure Spring Cloud 的以下功能：

* 配置服务器
* 蓝绿部署
* 应用程序规模调整
* 与 Azure DevOps 集成
* 应用程序监视

## <a name="spring-cloud-config-server"></a>Spring Cloud 配置服务器

Azure Spring Cloud 配置服务器为分布式系统中的外部化配置提供服务器和客户端支持。  Azure Spring Cloud Config Server 是一个中心位置，可在其中管理所有环境中的应用程序属性。 有关详细信息，请参阅 [Spring Cloud Config Server 参考](https://spring.io/projects/spring-cloud-config.md)。 

## <a name="bluegreen-deployments"></a>蓝绿部署

Azure Spring Cloud 支持用于发布和更新代码的蓝/绿部署（部署到生产环境）。  利用这种变更管理模式，开发人员可以放心地实现功能和代码更改，必要时可以立即回退。  开发人员可以在多个生产环境中专注于编写代码，在不中断应用程序的情况下更新或回退代码更改。  若要详细了解过渡环境和蓝/绿部署，请参阅此[操作指南文章](spring-cloud-howto-staging-environment.md)。

## <a name="cicd-pipeline-automation"></a>CI/CD 管道自动化

Azure Spring Cloud 提供通过 Azure CLI 与 Azure DevOps 集成的功能。  使用 Azure DevOps，可以自动集成代码并将其部署到 Spring 应用程序。  若要了解详细信息，请参阅此[文章](spring-cloud-howto-cicd.md)。

## <a name="application-scaling"></a>应用程序规模调整

使用 Azure Spring Cloud，可以轻松地在 Azure Spring Cloud 仪表板中缩放微服务。  可以根据要求上下缩放可供微服务使用的 vCPU 数和内存量。  缩放在数秒内生效，不需代码更改或重新部署。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-scale-manual.md)。

## <a name="application-monitoring"></a>应用程序监视

开发人员可以通过 Spring Cloud 的分布式跟踪工具调试和监视应用程序中微服务之间的复杂互连。  通过将 [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) 与 Azure 的 [Application Insights](../azure-monitor/insights/insights-overview.md) 集成，Azure 可以直接在 Azure 门户中提供强大的分布式跟踪功能。  若要了解详细信息，请完成此[教程](spring-cloud-tutorial-distributed-tracing.md)。

## <a name="next-steps"></a>后续步骤

若要开始，请完成 [Spring Cloud 快速入门](spring-cloud-quickstart.md)

可在 GitHub 上找到示例：[Azure Spring Cloud 示例](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/)。
