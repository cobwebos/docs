---
title: Require MFA from untrusted networks - Azure Active Directory
description: Learn how to configure a Conditional Access policy in Azure Active Directory (Azure AD) to for access attempts from untrusted networks.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379994"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to: Require MFA for access from untrusted networks with Conditional Access   

Azure Active Directory (Azure AD) 允许从任何位置以单一登录方式登录到设备、应用和服务。 用户不但可以从组织的网络访问云应用，而且可以从任何不受信任的 Internet 位置访问云应用。 对于来自不受信任网络的访问，常见的最佳做法是要求其进行多重身份验证 (MFA)。

This article gives you the information you need to configure a Conditional Access policy that requires MFA for access from untrusted networks. 

## <a name="prerequisites"></a>必备组件

本文假定你熟悉以下内容： 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>方案描述

为掌控安全性与工作效率之间的平衡，对于你来说，对于来自你的组织网络的登录，只需要要求其提供密码可能就足够了。 但是，对于来自不受信任网络位置的访问，登录不是由合法用户执行的这一风险会增大。 要解决此顾虑，可以阻止来自不受信任网络的访问。 另外，还可以要求进行多重身份验证 (MFA) 来获得额外的保证，以确保访问尝试是由该帐户的合法所有者执行的。 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access: 

- 授予对所选云应用的访问权限
- 为所选用户和组授予权限  
- 要求进行多重身份验证 
- 当访问来自： 
   - 不受信任的位置

## <a name="implementation"></a>实现

The challenge of this scenario is to translate *access from an untrusted network location* into a Conditional Access condition. In a Conditional Access policy, you can configure the [locations condition](location-condition.md) to address scenarios that are related to network locations. 使用位置条件，你可以选择已命名位置，这些位置是 IP 地址范围、国家和地区的逻辑分组。  

Typically, your organization owns one or more address ranges, for example, 199.30.16.0 - 199.30.16.15.
可以通过以下方式配置命名位置：

- Specifying this range (199.30.16.0/28) 
- 分配一个描述性名称，例如**公司网络** 

可以选择以下选项，而不是尝试定义不受信任的所有位置：

- 包括任何位置 

   ![条件访问](./media/untrusted-networks/02.png)

- 排除所有受信任的位置 

   ![条件访问](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>策略部署

With the approach outlined in this article, you can now configure a Conditional Access policy for untrusted locations. 若要确保你的策略按预期工作，建议的最佳做法是在将其推广到生产环境之前对其进行测试。 理想情况下，使用一个测试租户来验证新策略是否按预期方式工作。 有关详细信息，请参阅[如何部署新策略](best-practices.md#how-should-you-deploy-a-new-policy)。 

## <a name="next-steps"></a>后续步骤

If you would like to learn more about Conditional Access, see [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
