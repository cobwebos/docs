---
title: Azure 春季云的服务计划和配额
description: 了解 Azure 春季云的服务配额和服务计划
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: 41a2b1a7d9aa5089ba2ee73cd3c5c5c5e31f5225
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607667"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>适用于 Azure 春季云的配额和服务计划

所有 Azure 服务都设置针对资源和功能的默认限制和配额。  在预览期间，Azure 春季云仅提供一个服务计划。

本文详细介绍了当前预览期间提供的服务配额。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季云服务层和配额

在预览期间，Azure 春季云仅提供一个服务层。

资源 | 金额
------- | -------
vCPU | 4
内存 | 8 Gb
Azure 春季云订阅 | 1
每个订阅每个区域的 Azure 春季云服务实例 | 2
每个 Azure 春季云服务实例的应用实例总数 | 50
每个春季应用程序的应用程序实例总数 | 20
永久性卷 | 10 x 50 Gb

当达到配额时，你将收到400错误，该错误在*创建 Azure 春季云服务*的区域区域中读取 "配额*超出订阅的*限制"。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源需要增加，请[创建支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。
