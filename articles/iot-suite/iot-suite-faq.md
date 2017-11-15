---
title: "Azure IoT 套件常见问题解答 | Microsoft Docs"
description: "有关 IoT 套件的常见问题"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2017
ms.author: dobett
ms.openlocfilehash: 63b059cb91956231fd3bafbe9770a04a0b7e347a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>有关 IoT 套件的常见问题

另请参阅连接工厂相关的[常见问题解答](iot-suite-faq-cf.md)。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>在哪里可以找到预配置解决方案的源代码？

源代码存储在以下 GitHub 存储库中：

* [远程监视预配置解决方案 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [远程监视预配置解决方案 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [预测性维护预配置解决方案](https://github.com/Azure/azure-iot-predictive-maintenance)
* [连接工厂预配置解决方案](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>预配新的远程监视解决方案的费用是多少？

新的预配置解决方案提供两个部署选项：

* 基本选项适用于寻求更低开发成本的开发人员，或想要生成演示或概念证明的客户。
* 标准选项适用于想要部署生产就绪基础结构的企业。

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>如何确保在开发解决方案的同时不断降低成本？

除了提供两种不同的部署以外，新的远程监视解决方案还提供一项设置来按需启用或禁用所有模拟设备。 禁用模拟可以减少解决方案中引入的数据，因此可以降低总体成本。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>新的微服务体系结构是否适用于所有三个预配置解决方案？

目前，只有远程监视解决方案使用微服务体系结构，因为它涵盖最广泛的方案。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>基于微服务的新开源体系结构在新的更新中提供哪些优势？

过去两年以来，云体系结构已发生重大的演变。 微服务应运而生，是能够在不降低开发速度的情况下实现可伸缩性和灵活性的极佳模式。 此体系结构模式已在多种 Microsoft 服务内部使用，且获得了出色的可靠性和可伸缩性。 我们正在将这些知识付诸实践，使客户能够从中受益。

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>新的预配置解决方案是否在现有解决方案所在的同一地理区域中可用？

是的，新的远程监视解决方案可在同一地理区域中使用。

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>基本和标准部署选项有什么差别？ 如何在这两个部署选项之间做出选择？

每个部署选项对应于不同的需求。 基本部署旨在满足入门需要以及开发 PoC 和小型试验。 它提供一个简化的体系结构，包含最少量的必要资源，并且费用较低。 标准部署旨在生成和自定义生产就绪的解决方案，为实现部署提供所需的元素。 在可靠性和缩放，微服务应用程序构建为 Docker 容器，并使用某个业务流程协调程序（默认为 Kubernetes）进行部署。 该业务流程协调程序负责部署、缩放和管理应用程序。 应根据当前需求选择其中一个选项。 可以根据项目所处的阶段，使用其中的一个选项或两者的组合。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>是否可以继续利用 Azure IoT 套件中的现有投资？

是的。 现存的任何解决方案可继续在 Azure 订阅中工作，GitHub 中仍会提供其源代码。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 门户中删除资源组与在 azureiotsuite.com 中对预配置解决方案单击删除之间的区别是什么？

* 如果在 [azureiotsuite.com](https://www.azureiotsuite.com/) 中删除预配置解决方案，则会删除在创建预配置解决方案时预配的所有资源。 如果向资源组添加了其他资源，则也会删除这些资源。
* 如果在 [Azure 门户](https://portal.azure.com)中删除资源组，则只会删除该资源组中的资源。 此外还需要删除与预配置解决方案关联的 Azure Active Directory 应用程序。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在一个订阅中可以设置多少个 IoT 中心实例？

默认情况下，可[为每个订阅预配 10 个 IoT 中心](../azure-subscription-service-limits.md#iot-hub-limits)。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以提高此限制。 由于每个预配置的解决方案将预配一个新的 IoT 中心，因此，在给定的订阅中，最多只能预配 10 个预配置的解决方案。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>在订阅中可以预配多少个 Azure Cosmos DB 实例？

50 个。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)提高此限制，但默认情况下，只能对每个订阅预配 50 个 Cosmos DB 实例。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>在订阅中可以设置多少个免费必应地图 API？

两个。 在一个 Azure 订阅中，只能创建两个面向企业的内部事务级别 1 必应地图计划。 默认情况下，远程监视解决方案是使用内部事务级别 1 计划预配的。 因此，如果不进行修改，则在订阅中只能设置最多两个远程监视解决方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我具有 Microsoft Azure for DreamSpark，是否可以创建预配置解决方案？

当前无法使用 [Microsoft Azure for DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99) 帐户创建预配置解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/free/)，以便创建预配置的解决方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>如果我有云解决方案提供商 (CSP) 订阅，是否可以创建预配置的解决方案？

当前无法通过云解决方案提供商 (CSP) 订阅创建预配置的解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/free/)，以便创建预配置的解决方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何删除 AAD 租户？

请参阅 Eric Golpe 的博客文章 [Walkthrough of Deleting an Azure AD Tenant](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)（有关删除 Azure AD 租户的演练）。

### <a name="next-steps"></a>后续步骤

还可以浏览 IoT 套件预配置的解决方案的一些其他特性和功能：

* [预见性维护预配置解决方案概述](iot-suite-predictive-overview.md)
* [连接工厂预配置解决方案概述](iot-suite-connected-factory-overview.md)
* [从头保障 IoT 的安全](securing-iot-ground-up.md)