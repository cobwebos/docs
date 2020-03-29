---
title: Azure 春云的服务计划和配额
description: 了解 Azure 春云的服务配额和服务计划
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278891"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Azure 春云的配额和服务计划

所有 Azure 服务都设置针对资源和功能的默认限制和配额。  在预览期间，Azure 春云仅提供一个服务计划。

本文详细介绍了当前预览期间提供的服务配额。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季云服务层和配额

在预览期间，Azure 春云仅提供一个服务层。

资源 | 金额
------- | -------
vCPU | 每个服务实例 4 个
内存 | 每个服务实例 8 GB
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10
每个 Azure Spring Cloud 服务实例的应用实例总数 | 500
每个 Spring 应用程序的应用实例总数 | 20
永久性卷 | 10 x 50 GB

达到配额时，您将收到一个 400 个错误，该错误如下："*在创建 Azure Spring 云服务的区域*中，*订阅*的配额超过限制。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源需要增加，[请创建支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
