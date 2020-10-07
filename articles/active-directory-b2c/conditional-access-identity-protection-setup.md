---
title: 在 Azure AD B2C 中设置标识保护和条件访问
description: 了解如何为 Azure AD B2C 租户配置标识保护和条件访问，以查看风险登录和其他风险事件并基于风险检测创建策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5554cfcde9aba1b0e5c9c8b60e2e6a7e9a8ba378
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89270644"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>在 Azure AD B2C 中设置标识保护和条件访问

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

标识保护为 Azure AD B2C 租户提供持续的风险检测。 如果 Azure AD B2C 租户定价层是 Premium P2，则可以在 Azure 门户中查看详细的标识保护风险事件。 还可以使用基于这些风险检测的[条件访问](../active-directory/conditional-access/overview.md)策略来确定操作并强制执行组织策略。

## <a name="prerequisites"></a>先决条件

- Azure AD B2C 租户必须[关联到 Azure AD 订阅](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)。
- 需要 Azure AD B2C Premium P2 才能使用登录和基于用户风险的条件访问。 如有必要，[将 Azure AD B2C 定价层更改为 Premium P2](https://aka.ms/exid-pricing-tier)。 
- 若要在 B2C 租户中管理标识保护和条件访问，你需要分配有全局管理员角色或安全管理员角色的帐户。
- 若要在租户中使用这些功能，首先需要切换到 Azure AD B2C Premium P2 定价层。

## <a name="set-up-identity-protection"></a>设置标识保护

默认情况下，标识保护处于启用状态。 若要能够在 Azure AD B2C 租户中查看标识保护风险事件，只需将 Azure AD B2C 租户关联到 Azure AD 订阅，并选择 Azure AD B2C Premium P2 定价层。 可以在 Azure 门户中查看详细的风险事件报告。

### <a name="supported-identity-protection-risk-detections"></a>支持的标识保护风险检测

Azure AD B2C 当前支持以下风险检测：  

|风险检测类型  |说明  |
|---------|---------|
| 异常位置登录     | 从异常位置（基于用户最近的登录）进行登录。        |
|匿名 IP 地址     | 从匿名 IP 地址登录（例如：Tor 浏览器，匿名程序 VPN）        |
|不熟悉的登录属性     | 使用给定用户最近未曾出现过的属性进行登录。        |
|受恶意软件感染的 IP 地址     | 从受恶意软件感染的 IP 地址进行登录         |
|Azure AD 威胁智能     | Microsoft 的内部和外部威胁智能源已识别出已知的攻击模式        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>查看 Azure AD B2C 租户的风险事件

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。

1. 在“安全性”下，选择“风险用户(预览版)” 。

   ![有风险用户](media/conditional-access-identity-protection-setup/risky-users.png)

1. 在“安全性”下，选择“风险检测(预览版)” 。

   ![风险检测](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>添加条件访问策略 

若要基于标识保护风险检测添加条件访问策略，请确保为 Azure AD B2C 租户禁用了安全默认值，然后创建条件访问策略。

### <a name="to-disable-security-defaults"></a>禁用安全默认值

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

3. 在 Azure 门户中，搜索并选择“Azure Active Directory”。

4. 选择“属性”，然后选择“管理安全默认值” 。

   ![禁用安全默认值](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. 在“启用安全默认值”下，选择“否”。 

   ![将“启用安全默认值”切换键设置为“否”](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>创建条件访问策略

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

1. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。

1. 在“安全性”下，选择“条件访问(预览版)” 。 此时将打开“条件访问策略”页。 

1. 选择“新建策略”并按照 Azure AD 条件访问文档创建新策略。 以下是一个示例：

   - [基于登录风险的条件访问：启用条件访问策略](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy)

   > [!IMPORTANT]
   > 在选择要对其应用策略的用户时，请不要仅选择“所有用户”，否则你将无法登录。

## <a name="test-the-conditional-access-policy"></a>测试条件访问策略

1. 使用以下设置创建上述条件访问策略：
   
   - 对于“用户和组”，选择测试用户。 不要选择“所有用户”，否则你将无法登录。
   - 对于“云应用或操作”，选择“选择应用”，然后选择依赖方应用程序 。
   - 对于“条件”，选择“登录风险”以及“高”、“中”和“低”风险级别   。
   - 对于“授权”，选择“阻止访问” 。

      ![选择“阻止访问”](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. 通过选择“创建”启用测试条件访问策略。

1. 使用 [Tor 浏览器](https://www.torproject.org/download/)来模拟风险登录。 

1. 在用于尝试登录的 jwt.ms 解码令牌中，你应看到已阻止登录：

   ![测试阻止的登录](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>查看审核报告中的条件访问结果

查看条件访问事件的结果：

1. 登录 [Azure 门户](https://portal.azure.com/)。

2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录  。

3. 在 Azure 门户中，搜索并选择“Azure AD B2C”  。

4. 在“活动”下，选择“审核日志” 。

5. 通过将“类别”设置为“B2C”并将“活动资源类型”设置为“IdentityProtection”来筛选审核日志   。 然后，选择“应用”。

6. 查看最多过去 7 天的审核活动。 包括以下类型的活动：

   - **评估条件访问策略**：此审核日志条目指示在身份验证过程中执行了条件访问评估。
   - **修正用户**：此条目指示最终用户符合条件访问策略的授权或要求，并已向风险引擎报告此活动以降低（缓解）用户风险。

7. 在列表中选择“评估条件访问策略”日志条目以打开“活动详细信息：审核日志” 页，其中显示审核日志标识符，以及“其他详细信息”部分中的以下信息：

   - ConditionalAccessResult：条件策略评估所需的授权。
   - AppliedPolicies：满足条件且策略处于启用状态的所有条件访问策略的列表。
   - ReportingPolicies：设置为仅限报告模式且满足条件的条件访问策略的列表。

## <a name="next-steps"></a>后续步骤

[向用户流添加条件访问](conditional-access-user-flow.md)。
