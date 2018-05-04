---
title: Azure IoT 套件常见问题解答 | Microsoft Docs
description: 有关 IoT 套件的常见问题
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 49e94e771deb4582b922400d81e8388faf164f40
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="frequently-asked-questions-for-iot-suite"></a>有关 IoT 套件的常见问题

另请参阅[特定于连接工厂的常见问题解答](iot-suite-faq-cf.md)和[特定于远程监视的常见问题解答](iot-suite-faq-rm-v2.md)。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>在哪里可以找到预配置解决方案的源代码？

源代码存储在以下 GitHub 存储库中：

* [远程监视预配置解决方案 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [远程监视预配置解决方案 (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [预测性维护预配置解决方案](https://github.com/Azure/azure-iot-predictive-maintenance)
* [连接工厂预配置解决方案](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-preconfigured-solutions"></a>可以使用哪些 SDK 来为预配置解决方案开发设备客户端？

可以在 [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub 存储库中找到各种语言（C、NET、Java、Node.js、Python）的 IoT 设备 SDK。

如果在使用 DevKit 设备，可以在 [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub 存储库中找到资源和示例。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>新的微服务体系结构是否适用于所有三个预配置解决方案？

目前，只有远程监视解决方案使用微服务体系结构，因为它涵盖最广泛的方案。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>基于微服务的新开源体系结构在新的更新中提供哪些优势？

过去两年以来，云体系结构已发生重大的演变。 微服务应运而生，是能够在不降低开发速度的情况下实现可伸缩性和灵活性的极佳模式。 此体系结构模式已在多种 Microsoft 服务内部使用，且获得了出色的可靠性和可伸缩性。 我们正在将这些知识付诸实践，使客户能够从中受益。

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>新的预配置解决方案是否在现有解决方案所在的同一地理区域中可用？

是的，新的远程监视解决方案可在同一地理区域中使用。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 门户中删除资源组与在 azureiotsuite.com 中对预配置解决方案单击删除之间的区别是什么？

* 如果在 [azureiotsuite.com](https://www.azureiotsuite.com/) 中删除预配置解决方案，则会删除在创建预配置解决方案时预配的所有资源。 如果向资源组添加了其他资源，则也会删除这些资源。
* 如果在 [Azure 门户](https://portal.azure.com)中删除资源组，则只会删除该资源组中的资源。 此外还需要删除与预配置解决方案关联的 Azure Active Directory 应用程序。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>是否可以继续利用 Azure IoT 套件中的现有投资？

是的。 现存的任何解决方案可继续在 Azure 订阅中工作，GitHub 中仍会提供其源代码。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在一个订阅中可以设置多少个 IoT 中心实例？

默认情况下，可[为每个订阅预配 10 个 IoT 中心](../azure-subscription-service-limits.md#iot-hub-limits)。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以提高此限制。 由于每个预配置的解决方案将预配一个新的 IoT 中心，因此，在给定的订阅中，最多只能预配 10 个预配置的解决方案。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>在订阅中可以预配多少个 Azure Cosmos DB 实例？

50 个。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)提高此限制，但默认情况下，只能对每个订阅预配 50 个 Cosmos DB 实例。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>在订阅中可以设置多少个免费必应地图 API？

两个。 在一个 Azure 订阅中，只能创建两个面向企业的内部事务级别 1 必应地图计划。 默认情况下，远程监视解决方案是使用内部事务级别 1 计划预配的。 因此，如果不进行修改，则在订阅中只能设置最多两个远程监视解决方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我具有 Microsoft Azure for DreamSpark，是否可以创建预配置解决方案？

> [!NOTE]
> Microsoft Azure for DreamSpark 现在称为 Microsoft Imagine 学生版。

当前无法使用 [Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) 帐户创建预配置解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/free/)，以便创建预配置的解决方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>如果我有云解决方案提供商 (CSP) 订阅，是否可以创建预配置的解决方案？

当前无法通过云解决方案提供商 (CSP) 订阅创建预配置的解决方案。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/free/)，以便创建预配置的解决方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何删除 AAD 租户？

请参阅 Eric Golpe 的博客文章 [Walkthrough of Deleting an Azure AD Tenant](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)（有关删除 Azure AD 租户的演练）。

### <a name="next-steps"></a>后续步骤

还可以浏览 IoT 套件预配置的解决方案的一些其他特性和功能：

* [探索远程监视预配置解决方案的功能](iot-suite-remote-monitoring-explore.md)
* [预见性维护预配置解决方案概述](iot-suite-predictive-overview.md)
* [连接工厂预配置解决方案概述](iot-suite-connected-factory-overview.md)
* [从头保障 IoT 的安全](securing-iot-ground-up.md)