---
title: "Azure Active Directory B2C：有关使用自定义策略的开发人员说明 | Microsoft Docs"
description: "有关使用自定义策略配置和维护 B2C 的开发人员说明"
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
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 72447a234c5889f36b10b828d28775fc5995c4d4
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 自定义策略公共预览版发行说明
**自定义策略**功能集现已推出公共预览版，供所有 Azure Active Directory B2C (Azure AD B2C) 客户评估。  此功能集面向构建最复杂标识解决方案的高级标识开发人员。  

目前，此功能集要求开发人员直接通过 XML 文件编辑配置标识体验框架 (IEF)。  这种配置方法非常有效，但也比较复杂。  使用 IEF 的高级标识开发人员应该投入时间完成演练并阅读参考文档，学习如何运用此方法。 

## <a name="features-included-in-this-public-preview"></a>此公共预览版包含的功能
公共评审版中引入的新功能可让开发人员执行以下任务：
1. 使用自定义策略创作并上传自定义身份验证用户旅程
* 将用户旅程逐步描述为声明提供程序之间的交换
* 在用户旅程中定义条件分支
2. 在自定义身份验证用户旅程中集成已启用 REST API 的服务
3. 添加与符合 OpenIDConnect 标准的标识提供者的联合
4. 添加与遵守 SAML 2.0 协议的标识提供者的联合



## <a name="terms-of-the-public-preview"></a>公共预览版条款
1. 建议只将新功能用于评估目的
2. 不应在生产环境中使用新功能
3. 服务级别协议 (SLA) 不适用于新功能。 可以通过普通支持渠道提出支持请求
5. 公式版没有确定的发布日期
6. Microsoft 可以出于任何原因，根据自身的判断，标识并拒绝或限制已超出充当客户标识和访问管理 (CIAM) 平台的 Azure AD B2C 产品规章范围的方案和用户旅程

## <a name="responsibilities-for-custom-policy-feature-set-developers"></a>自定义策略功能集开发人员的责任
手动策略配置授予对 Azure AD B2C 基础平台的较低访问级别，因此要求创建唯一的、完全可自定义的信任框架。  自定义标识提供者、信任关系、与外部服务的集成以及分步工作流的近乎无限组合方式，对使用这些资源的高级开发人员提出了更高的要求。
为了充分利用公共预览版，我们建议使用自定义策略功能集的开发人员遵守以下规则：
* 熟悉标识体验引擎 (IEE) 和密钥/机密管理的配置语言
* 取得方案和自定义集成的所有权
* 执行有序的方案测试
* 在至少一个开发/测试环境和一个生产环境中遵循软件开发与过渡最佳做法
* 通过所要集成的标识提供者和服务随时了解新的开发，例如，跟踪机密的更改，以及对服务所做的计划/计划外更改，等等 - 设置有效的监视，并监视生产环境的响应能力
* 保留最新的联系电子邮件，并快速回复 Microsoft 实时站点团队的电子邮件
* 根据 Microsoft 实时站点团队的通告及时采取措施 


>[!NOTE]
>这些功能最终可能会包含在 Azure AD 内置策略中，使所有开发人员能够更方便地访问。

## <a name="next-steps"></a>后续步骤
[自定义策略入门](active-directory-b2c-get-started-custom.md)。

