---
title: Azure 春季云的服务计划和配额
description: 了解 Azure 春季云的服务配额和服务计划
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: jeconnoc
ms.openlocfilehash: a5352b371c5754672e668e53eb5e4211de9c46cc
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75891496"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>适用于 Azure 春季云的配额和服务计划

所有 Azure 服务都为资源和功能设置默认限制和配额。  在预览期间，Azure 春季云仅提供一个服务计划。

本文详细介绍了当前预览期间提供的服务配额。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季云服务层和配额

在预览期间，Azure 春季云仅提供一个服务层。

资源 | 总量
------- | -------
vCPU | 每个服务实例4个
内存 | 每个服务实例 8 Gb
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10
每个 Azure Spring Cloud 服务实例的应用实例总数 | 500
每个春季应用程序的应用程序实例总数 | 20
永久性卷 | 10 x 50 GB

当达到配额时，你将收到400错误，该错误在*创建 Azure 春季云服务*的区域区域中读取 "配额*超出订阅的*限制"。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源需要增加，请[创建支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
