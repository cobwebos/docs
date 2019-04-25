---
title: Dynamics 365 for Customer Engagement 产品/服务先决条件 - Azure 市场| Microsoft Docs
description: 在 Azure 市场上发布 Azure 应用程序产品/服务的先决条件。
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: b08312040f9702b8a9100886c198138431012e3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308665"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 for Customer Engagement 先决条件

本文介绍了在 AppSource 市场上发布 Dynamics 365 for Customer Engagement 应用程序产品/服务的技术和业务先决条件。  如果尚未这样做，请查看[Office 365，Dynamics 365，PowerApps 和 Power BI 产品/服务发布指南](../../appsource-offer-publishing-guide.md)。


## <a name="technical-requirements"></a>技术要求

Dynamics 365 for Customer Engagement 应用程序必须符合[Microsoft AppSource 应用审核指南](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf)，其中包括以下要求：


|              要求             |        描述           |
|            ---------------           |      ---------------         |
| Azure Active Directory 集成   | 在已启用许可的情况下，应用须允许使用 Azure Active Directory 联合单一登录（AAD 联合 SSO）。 有关详细信息，请参阅[如何使 AppSource 通过 Azure Active Directory 的认证](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified)。 |
| 与 Microsoft 云服务集成（可选） | 如果需要此功能，应将应用与 Microsoft Power BI、Microsoft Flow 或 Microsoft Azure 服务（如机器学习或认知服务）等其他 Microsoft 云服务集成。 |
| 聚焦业务线            |  应用必须专注于明确的业务流程或问题，主要面向企业客户，并使用户能够使用工作凭据（用户名和密码）登录。  |
| 免费试用期和试用体验 |  提供“立即获取”免费应用、指定时间段内“免费试用”、“体验版”试用产品或“与我联系”请求选项，使客户能够在限定的时间范围内免费使用应用。  |
| 无/低配置                 | 应用的配置和设置必须便捷（无需开发或自定义）。  |
| 客户支持                     | 对应用的支持必须包含支持链接，客户可以在其中找到帮助。  |
| 可用性/运行时间                  | 应用的正常运行时间必须至少达到 99.9%。 |
|  |  |


## <a name="business-requirements"></a>业务要求

业务需求包括在规程、合同和法律方面的以下义务：

* 必须在 [Microsoft 合作伙伴网络 (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)上注册或者是已注册的云市场发布者。 如果尚未注册，请执行[成为云市场发布者](../../become-publisher.md)中的步骤。  （对于第三步，请使用 [AppSource 合作伙伴提名表](https://appsource.microsoft.com/partners/signup)）。 

    >[!NOTE]
    >应当使用相同的 Microsoft Developer Center 注册帐户登录到云合作伙伴门户。 对于 Azure 市场产品/服务，只应具有一个 Microsoft 帐户。 此帐户不应特定于个别服务或产品/服务。

* AppSource 不提供支持商务的发布选项，因此必须使用当前的订购和计费基础结构，无需额外的投资或更改。
* 你需要以商业上合理的方式负责向客户提供技术支持。 支持可以采用免费、付费或社区支持等多种方式。
* 负责获得自己的软件和任何第三方软件依赖项的许可。
* 你应该已创建了相关的营销材料，例如官方应用名、说明（HTML 格式）、PNG 格式的徽标图像（40 x 40、90 x 90、115 x 115 和 255 x 115 像素）以及使用条款和隐私策略。  


## <a name="next-steps"></a>后续步骤

满足这些要求就可以[创建 Dynamics 365 Customer Engagement 产品/服务](./cpp-create-offer.md) 
