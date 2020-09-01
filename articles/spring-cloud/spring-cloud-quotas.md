---
title: Azure 春季云的服务计划和配额
description: 了解 Azure 春季云的服务配额和服务计划
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d47311385848f96a12a50c1dcf8b8284869c71d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260350"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>适用于 Azure 春季云的配额和服务计划

所有 Azure 服务都设置针对资源和功能的默认限制和配额。   Azure 春季云提供两个定价层：基本和标准。 本文将详细介绍这两个层的限制。

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Azure 春季云服务层和限制

| 资源 | 基本 | 标准
------- | ------- | -------
vCPU | 每个服务实例1个 | 每个服务实例4个
内存 | 每个服务实例 2 GB | 每个服务实例 8 GB
每个区域每个订阅的 Azure Spring Cloud 服务实例数 | 10 | 10
每个 Azure Spring Cloud 服务实例的应用实例总数 | 25 | 500
永久性卷 | 1 GB/应用 x 10 个应用 | 50 GB/app x 10 应用

## <a name="next-steps"></a>后续步骤

可以增加一些默认限制。 如果安装程序需要增加，请 [创建支持请求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。
