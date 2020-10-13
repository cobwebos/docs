---
title: 在 Azure 安全中心监视标识和访问 | Microsoft Docs
description: 了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: memildin
ms.openlocfilehash: 02e78969ce30f109f16309075b040b06c773b0dd
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946208"
---
# <a name="monitor-identity-and-access"></a>监视标识和访问

安全边界已从网络边界演进成标识边界。 在此开发中，安全性不太能保护您的网络，更多的是管理您的应用程序、数据和用户的安全性。

通过监视与标识相关的活动和配置设置，你可以在事件发生之前采取主动措施，或者采取反应操作来停止尝试的攻击。

## <a name="what-identity-and-access-safeguards-does-security-center-provide"></a>安全中心提供哪些身份验证和访问保护？ 

Azure 安全中心具有两个专用安全控制，可确保满足组织的身份和安全要求： 

 - **管理访问权限和权限** -我们鼓励你采用 [最小特权访问模式](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) ，并确保你只向用户授予完成其工作所需的访问权限。 此控件还包含用于实现 [基于角色的访问控制 (RBAC) ](../role-based-access-control/overview.md) 来控制对资源的访问的建议。
 
 - **启用 mfa** [-启用 mfa，你的帐户](https://www.microsoft.com/security/business/identity/mfa) 更安全，用户仍然可以通过单一登录向几乎所有应用程序进行身份验证。

### <a name="example-recommendations-for-identity-and-access"></a>标识和访问的示例建议

在安全中心的 " **建议** " 页上的以下两个控件中可能会看到的建议示例：

- 应在对订阅拥有所有者权限的帐户上启用 MFA
- 只多只为订阅指定 3 个所有者
- 应从订阅中删除拥有读取权限的外部帐户
- 应从订阅中删除弃用的帐户 (不推荐使用的帐户是不再需要的帐户，并通过 Azure Active Directory 阻止登录) 

> [!TIP]
> 若要详细了解这些建议以及这些建议中可能会显示的其他建议，请参阅 [身份验证和访问建议](recommendations-reference.md#recs-identity)。

### <a name="limitations"></a>限制

安全中心的标识和访问保护存在一些限制：

- 标识建议不适用于超过600个帐户的订阅。 在这种情况下，这些建议将在 "不可用评估" 下列出。
- 标识建议不适用于云解决方案提供商 (CSP) 伙伴的管理代理。
- 标识建议不标识使用特权标识管理 (PIM) 系统管理的帐户。 如果你使用的是 PIM 工具，你可能会在 **管理访问权限和权限** 控制中看到不准确的结果。

## <a name="multi-factor-authentication-mfa-and-azure-active-directory"></a>多重身份验证 (MFA) 和 Azure Active Directory 

启用 MFA 需要 [Azure Active Directory (AD) 租户权限](../active-directory/users-groups-roles/directory-assign-admin-roles.md)。

- 如果拥有高级版 AD，请使用[条件访问](../active-directory/conditional-access/concept-conditional-access-policy-common.md)启用 MFA。
- 如果使用的是 AD 免费版，请启用 **安全默认设置** ，如 [Azure Active Directory 文档](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)中所述。

## <a name="identify-accounts-without-multi-factor-authentication-mfa-enabled"></a>找不到 (MFA) 启用多因素身份验证的帐户

若要查看哪些帐户未启用 MFA，请使用以下 Azure 资源关系图查询。 查询将返回所有不正常的资源-"应对订阅拥有所有者权限的帐户上启用 MFA" 的 "帐户"。 

1. 打开 **Azure 资源图资源管理器**。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="正在启动 Azure 资源图资源管理器 * * 建议页&quot; :::

1. 输入以下查询，然后选择 " **运行查询**"。

    ```kusto
    securityresources
     | where type == "microsoft.security/assessments"
     | where properties.displayName == "MFA should be enabled on accounts with owner permissions on your subscription"
     | where properties.status.code == "Unhealthy"
    ```

1. `additionalData`属性显示未强制执行 MFA 的帐户的帐户对象 id 列表。 

    > [!NOTE]
    > 帐户显示为对象 Id 而不是帐户名称以保护帐户持有者的隐私。

> [!TIP]
> 或者，可以使用安全中心的 REST API 方法 [评估-获取](https://docs.microsoft.com/rest/api/securitycenter/assessments/get)。


## <a name="next-steps"></a>后续步骤
若要详细了解适用于其他 Azure 资源类型的建议，请参阅以下文章：

- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)