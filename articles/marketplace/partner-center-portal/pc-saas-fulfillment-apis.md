---
title: SaaS 履行 API |Azure 应用商店
description: 介绍履行 API 的版本，使您能够将 SaaS 产品与 Azure 应用商店集成。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275724"
---
# <a name="saas-fulfillment-apis"></a>SaaS 履行 API

SaaS 履行 API 使独立的软件供应商 （ISV） 能够将其 SaaS 应用程序与 Azure 应用商店集成。 这些 API 使 ISV 应用程序能够参与所有支持商务的渠道：直接、合作伙伴主导（经销商）和现场主导。  它们是在 Azure 应用商店中列出可交易的 SaaS 产品/服务的要求。

> [!WARNING]
> 此 API 的当前版本为版本 2，应用于所有新的 SaaS 产品。  API 的版本 1 被弃用，并且正在维护以支持现有产品/服务。


## <a name="business-model-support"></a>业务模式支持

此 API 支持以下业务模型功能;您可以：

* 为产品/服务指定多个计划。 这些计划具有不同的功能，定价可能不同。
* 根据每个站点或每个用户计费模型提供优惠。
* 提供每月和每年（预付）计费选项。
* 根据协商的业务协议为客户提供私人定价。


## <a name="next-steps"></a>后续步骤

如果尚未这样做，请在[Azure 门户](https://ms.portal.azure.com)中注册 SaaS 应用程序，如注册[Azure AD 应用程序](./pc-saas-registration.md)中所述。  之后，使用此接口的最新版本进行开发[：SaaS 履行 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
