---
title: "Azure Active Directory B2C：有关使用自定义策略的开发人员说明 | Microsoft Docs"
description: "有关使用自定义策略配置和维护 Azure AD B2C 的开发人员说明"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/05/2017
ms.author: joroja
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: a5f222e5b11e05286152a9f1cc55d2c3fc27a9dc
ms.contentlocale: zh-cn
ms.lasthandoff: 06/01/2017

---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 自定义策略公共预览版发行说明
自定义策略功能集现已推出公共预览版，供所有 Azure Active Directory B2C (Azure AD B2C) 客户评估。 此功能集面向构建最复杂标识解决方案的高级标识开发人员。  

目前，此功能集要求开发人员直接通过 XML 文件编辑配置标识体验框架。 这种配置方法非常有效，但也比较复杂。 使用标识体验框架的高级标识开发人员应投入时间完成演练并阅读参考文档。 

## <a name="features-included-in-this-public-preview"></a>此公共预览版包含的功能
公共预览版中引入的新功能可让开发人员执行以下任务：<br>

* 使用自定义策略创作并上传自定义身份验证用户旅程。 
   * 将用户旅程逐步描述为声明提供程序之间的交换。 
   * 在用户旅程中定义条件分支。 
* 在自定义身份验证用户旅程中集成已启用 REST API 的服务。  
* 添加与符合 OpenIDConnect 标准的标识提供者的联合。 <br>
* 添加与遵守 SAML 2.0 协议的标识提供者的联合。 

## <a name="terms-of-the-public-preview"></a>公共预览版条款

* 建议将新功能仅用于评估。<br>
* 不应在生产环境中使用新功能。<br>
* 服务级别协议 (SLA) 不适用于新功能。 <br>
* 可通过普通支持渠道提出支持请求。 <br>
* 尚未确定公开上市的日期。<br>
* Microsoft 可以出于任何原因，根据自身的判断，标识并拒绝或限制已超出充当客户标识和访问管理 (CIAM) 平台的 Azure AD B2C 产品规章范围的方案和用户旅程。

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任
手动策略配置授予对 Azure AD B2C 基础平台的较低访问级别，因此要求创建唯一的、完全可自定义的信任框架。 自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的各种排列组合方式，对使用这些资源的高级开发人员提出了更高的要求。

为充分利用公共预览版，建议使用自定义策略功能集的开发人员遵守以下准则：
* 熟悉标识体验引擎和密钥/机密管理的配置语言。
* 取得方案和自定义集成的所有权。
* 执行有序的方案测试。
* 在至少一个开发和测试环境以及一个生产环境中遵循软件开发与过渡最佳做法。
* 随时了解与之集成的标识提供程序和服务的新进展。 例如，跟踪机密的更改情况以及对服务的计划内和计划外更改。
* 设置主动监控，监控生产环境的响应能力。
* 保留最新的联系电子邮件地址，并快速回复 Microsoft 活动站点团队的电子邮件。
* 根据 Microsoft 活动站点团队的通知及时采取措施。 


>[!NOTE]
>这些功能最终可能会包含在 Azure AD 内置策略中，使所有开发人员能够更方便地访问使用。

## <a name="next-steps"></a>后续步骤
[自定义策略入门](active-directory-b2c-get-started-custom.md)。

