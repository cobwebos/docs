---
title: include 文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456719"
---
我们提供了不同的模板来处理特定载入方案。 选择最适合的选项，并确保修改参数文件以反映环境。 有关如何在部署中使用这些文件的详细信息，请参阅[将客户载入到 Azure 委派资源管理](../articles/lighthouse/how-to/onboard-customer.md)。

| **模板** | **说明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | 将客户的订阅载入到 Azure 委派资源管理。 必须为每个订阅执行单独的部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | 将客户的一个或多个资源组载入到 Azure 委派资源管理。 将 **rgDelegatedResourceManagement** 用于单个资源组，或使用 **multipleRgDelegatedResourceManagement**  载入同一订阅中的多个资源组。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | 如果已[向 Azure 市场发布托管服务产品](../articles/lighthouse/how-to/publish-managed-services-offers.md)，则可以选择使用此模板为已接受该产品的客户载入资源。 参数文件中的 marketplace 值必须与发布产品时使用的值匹配。 |

通常，每个载入的订阅都需要单独部署，但你也可以跨多个订阅部署模板。

| **模板** | **说明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 跨多个订阅部署 Azure 资源管理器模板。 |
