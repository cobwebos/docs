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
ms.openlocfilehash: 6b873508fe29ed0816a8b64b26cc5522ed23a8d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986656"
---
我们提供了不同的模板来处理特定载入方案。 选择最适合的选项，并确保修改参数文件以反映环境。 有关如何在部署中使用这些文件的详细信息，请参阅[将客户载入到 Azure 委派资源管理](../articles/lighthouse/how-to/onboard-customer.md)。

| **模板** | **说明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | 将客户的订阅载入到 Azure 委派资源管理。 必须为每个订阅执行单独的部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | 将客户的一个或多个资源组载入到 Azure 委派资源管理。 将 **rgDelegatedResourceManagement** 用于单个资源组，或使用 **multipleRgDelegatedResourceManagement**  载入同一订阅中的多个资源组。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | 如果已[向 Azure 市场发布托管服务产品](../articles/lighthouse/how-to/publish-managed-services-offers.md)，则可以选择使用此模板为已接受该产品的客户载入资源。 参数文件中的 marketplace 值必须与发布产品时使用的值匹配。 |

通常，每个载入的订阅都需要单独部署，但你也可以跨多个订阅部署模板。

| **模板** | **说明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 跨多个订阅部署 Azure 资源管理器模板。 |
