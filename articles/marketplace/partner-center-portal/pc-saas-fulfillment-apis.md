---
title: Microsoft 商业应用商店中的 SaaS 履单 Api
description: 介绍实现 Api，使你能够在 Microsoft AppSource 和 Azure Marketplace 中集成 SaaS 产品。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858083"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>Microsoft 商业应用商店中的 SaaS 履单 Api

SaaS 履单 Api 使独立软件供应商（Isv）能够将其 SaaS 应用程序集成到 Microsoft AppSource 和 Azure Marketplace 中。 这些 Api 使 ISV 应用程序能够参与所有启用了商业功能的频道：直接、伙伴 led （经销商）和现场 led。 它们需要列出 Microsoft AppSource 和 Azure Marketplace 中的事务 SaaS 产品/服务。

> [!WARNING]
> 此 API 的当前版本是版本2，适用于所有新的 SaaS 产品/服务。  API 的版本1已弃用，并将进行维护以支持现有的产品/服务。

## <a name="business-model-support"></a>业务模型支持

此 API 支持以下业务模型功能;您可以：

* 为产品/服务指定多个计划。 这些计划具有不同的功能，并且价格可能不同。
* 针对每个站点或按用户计费模型提供产品/服务。
* 提供每月和每年（付费）计费选项。
* 根据协商的业务协议为客户提供专用定价。


## <a name="next-steps"></a>后续步骤

如果尚未这样做，请在[Azure 门户](https://ms.portal.azure.com)中注册 SaaS 应用程序，如[注册 Azure AD 应用程序](./pc-saas-registration.md)中所述。  之后，使用此接口的最新版本进行开发： [SaaS 履单 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
