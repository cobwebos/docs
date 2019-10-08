---
title: Azure 春季云的服务计划和配额
description: 了解 Azure 春季云的服务配额和服务计划
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 9/27/2019
ms.author: jeconnoc
ms.openlocfilehash: 89934ee0ab0c901a904a1a5ac2fb620185571a23
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038777"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>适用于 Azure 春季云的配额和服务计划

所有 Azure 服务都设置针对资源和功能的默认限制和配额。  在预览期间，Azure 春季云仅提供一个服务计划。

本文详细介绍了当前预览期间提供的服务配额。

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Azure 春季云服务层和配额

在预览期间，Azure 春季云仅提供一个服务层。

Resource | 金额
------- | -------
vCPU | 4
内存 | 8 Gb
Azure 春季云订阅 | 第
每个订阅每个区域的 Azure 春季云服务实例 | 2
每个 Azure 春季云服务实例的应用实例总数 | 50
每个春季应用程序的应用程序实例总数 | 20
永久性卷 | 10 x 50 Gb

达到配额时，会收到400错误，其中显示："配额超出在*其中创建 Azure 春季云服务*的区域区域*内订阅*订阅的限制。

## <a name="next-steps"></a>后续步骤

某些默认限制和配额可以提高。 如果资源需要增加，请向我们发送请求： azure-spring-cloud@service.microsoft.com。
