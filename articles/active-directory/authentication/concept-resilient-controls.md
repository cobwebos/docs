---
title: 创建弹性访问控制管理策略-Azure AD
description: 本文档提供有关组织应采用的策略，以便在不可预见的中断期间降低锁定风险的能力
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908728"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>使用 Azure Active Directory 创建弹性访问控制管理策略

>[!NOTE]
> 本文档中包含的信息代表 Microsoft Corporation 的当前视图，其中包括发布日期所讨论的问题。 由于 Microsoft 必须对不断变化的市场情况做出响应，因此不应将其解释为 Microsoft 的承诺，并且 Microsoft 无法保证在发布日期后提供的任何信息的准确性。

依赖于单个访问控制（如多重身份验证（MFA）或单个网络位置）来保护其 IT 系统的组织容易遭受对其应用和资源的访问失败，因为该单一访问控制变为不可用或配置错误。 例如，自然灾害可能导致电信基础结构或公司网络的大型分段不可用。 此类中断会使最终用户和管理员无法登录。

本文档提供有关组织应采用的策略，以便在以下情况下降低在不可预见中断期间锁定风险的风险：

 1. 组织可以通过实施缓解策略或应变计划，增加复原能力，降低在**中断之前**锁定的风险。
 2. 组织可以继续通过实施缓解策略和应变计划来访问在**中断期间选择的**应用和资源。
 3. 组织应确保他们在**中断后**以及在回滚其实现的任何意外情况之前保留信息（如日志）。
 4. 尚未实现防护策略或替代计划的组织可能能够实施**紧急选项**来应对中断。

## <a name="key-guidance"></a>关键指南

本文档中有四个重要的结论：

* 避免使用紧急访问帐户进行管理员锁定。
* 使用条件性访问（CA）而不是按用户 MFA 实现 MFA。
* 通过使用多个条件访问（CA）控件来缓解用户锁定。
* 通过为每个用户设置多个身份验证方法或等效项来缓解用户锁定。

## <a name="before-a-disruption"></a>中断之前

缓解实际中断必须是组织在处理可能出现的访问控制问题时的主要焦点。 缓解措施包括：规划实际事件和实施策略，以确保在中断期间不影响访问控制和操作。

### <a name="why-do-you-need-resilient-access-control"></a>为什么需要弹性访问控制？

 标识是访问应用和资源的用户的控制平面。 标识系统控制用户可以访问应用程序的用户和条件，如访问控制或身份验证要求。 当一个或多个身份验证或访问控制要求无法供用户在由于无法预见的情况下进行身份验证时，组织可能会遇到以下一个或两个问题：

* **管理员锁定：** 管理员无法管理租户或服务。
* **用户锁定：** 用户无法访问应用或资源。

### <a name="administrator-lockout-contingency"></a>管理员锁定应急

若要解锁对你的租户的管理员访问权限，你应创建紧急访问帐户。 这些紧急访问帐户（也称为 "*中断玻璃*帐户"）允许在普通特权帐户访问过程不可用时管理 Azure AD 配置。 应按照[紧急访问帐户建议]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)创建至少两个紧急访问帐户。

### <a name="mitigating-user-lockout"></a>缓解用户锁定

 若要降低用户锁定的风险，请使用具有多个控件的条件性访问策略，使用户能够选择如何访问应用和资源。 通过向用户提供选择（例如，使用 MFA 登录**或**从托管设备登录**或**从公司网络登录），如果某个访问控制不可用，则用户有其他选项可继续工作。

#### <a name="microsoft-recommendations"></a>Microsoft 建议

在组织现有的条件性访问策略中合并以下访问控制：

1. 为依赖不同信道的每个用户设置多个身份验证方法，例如 Microsoft Authenticator 应用（基于 internet）、OATH 令牌（在设备上生成）和 SMS （telephonic）。
2. 部署 windows 10 设备上的 Windows Hello 企业版，以直接从设备登录满足 MFA 要求。
3. 通过[Azure AD 混合联接](https://docs.microsoft.com/azure/active-directory/devices/overview)或[Microsoft Intune 托管设备](https://docs.microsoft.com/intune/planning-guide)使用受信任的设备。 受信任的设备将改进用户体验，因为受信任的设备本身可以满足策略的强身份验证要求，而不会向用户收取 MFA 质询。 当注册新设备时，以及从不受信任的设备访问应用或资源时，将需要 MFA。
4. 使用 Azure AD identity protection 风险的策略，这些策略可防止在用户或登录有权使用固定 MFA 策略时进行访问。

>[!NOTE]
> 基于风险的策略需要[Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/)许可证。

下面的示例介绍了必须创建的策略，以便为用户提供对其应用和资源的访问权限控制。 在此示例中，你将需要一个安全组**AppUsers** ，其中包含要向其授予访问权限的目标用户、名为**CoreAdmins**的名为 "核心管理员" 的组以及一个名为**EmergencyAccess**的组，其中包含紧急访问帐户。
此策略设置将在**AppUsers**中授予选定的用户，如果已从受信任的设备连接或提供强身份验证（例如 MFA），则可访问所选应用。 它排除紧急帐户和核心管理员。

**CA 缓解策略集：**

* 策略1：阻止访问目标组以外的人员
  * 用户和组：包含所有用户。 排除 AppUsers、CoreAdmins 和 EmergencyAccess
  * 云应用：包括所有应用
  * 条件：（无）
  * 授予控制权限：阻止
* 策略2：向需要 MFA 或受信任设备的 AppUsers 授予访问权限。
  * 用户和组：包括 AppUsers。 排除 CoreAdmins 和 EmergencyAccess
  * 云应用：包括所有应用
  * 条件：（无）
  * 授权控制：授予访问权限，需要多重身份验证，要求设备符合要求的设备。 对于多个控件：需要一个选定的控件。

### <a name="contingencies-for-user-lockout"></a>用户锁定的紧急情况

或者，你的组织也可以创建应变策略。 若要创建应急策略，必须在业务连续性、运营成本、财务成本和安全风险之间定义权衡标准。 例如，你可以仅为一部分用户、部分应用程序、部分客户端或位置子集激活应急策略。） 当未实施任何缓解方法时，应急策略将为管理员和最终用户授予对应用和资源的访问权限。
在中断期间了解你的暴露程度有助于降低风险，这是规划过程中必不可少的一部分。 若要创建应变计划，请首先确定组织的以下业务要求：

1. 提前确定你的任务关键型应用程序：你必须为其提供访问权限的应用是什么，甚至具有更低的风险/安全状态？ 生成这些应用的列表，并确保其他利益干系人（业务、安全性、法律、领导）都同意如果所有访问控制都消失，这些应用仍必须继续运行。 你可能最终会得到的类别：
   * **类别1任务关键型应用**程序，这些应用程序不能使用超过几分钟的时间，例如直接影响组织收入的应用。
   * **类别 2**需要在数小时内访问业务的重要应用。
   * **类别3低优先级应用**，可能会经受几天的中断。
2. 对于类别1和2中的应用，Microsoft 建议您预先计划您要允许的访问级别：
   * 是否允许完全访问或受限会话，例如限制下载？
   * 是否允许访问应用的一部分，而不允许访问整个应用？
   * 是否想要在访问控制还原之前允许信息工作者访问和阻止管理员访问？
3. 对于这些应用程序，Microsoft 还建议您计划您将有意打开的访问途径，以及您将关闭哪些访问：
   * 是否要允许浏览器访问和阻止可保存脱机数据的丰富客户端？
   * 是否只允许企业网络中的用户访问并且阻止外部用户访问？
   * 是否只允许在中断期间从特定国家或地区访问？
   * 是否想要将策略应用于紧急情况策略，尤其是对于任务关键型应用程序，如果其他访问控制不可用，会失败或成功？

#### <a name="microsoft-recommendations"></a>Microsoft 建议

应急条件性访问策略是一个**禁用的策略**，该策略忽略 Azure MFA、第三方 MFA、基于风险的控件或基于设备的控件。 然后，当你的组织决定激活你的应变计划时，管理员可以启用该策略并禁用常规的基于控制的策略。

>[!IMPORTANT]
> 禁用强制用户安全性的策略（即使暂时会这样做），会降低安全状况，同时制定应变计划。

* 如果一种凭据类型或一种访问控制机制的中断影响到你的应用的访问，则配置一组回退策略。 配置处于禁用状态的策略，该策略需要将域加入为控制，作为需要第三方 MFA 提供程序的活动策略的备份。
* 根据[密码指南](https://aka.ms/passwordguidance)白皮书中的做法，在不需要 MFA 的情况下，降低不良参与者猜测密码的风险。
* 部署[Azure AD 自助服务密码重置（SSPR）](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)并[Azure AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy)，以确保用户不会使用你选择禁止的常见密码和条款。
* 如果未获得特定身份验证级别而不是只是回退到完全访问权限，请使用限制应用内的访问权限的策略。 例如:
  * 配置将受限制的会话声明发送到 Exchange 和 SharePoint 的备份策略。
  * 如果你的组织使用 Microsoft Cloud App Security，请考虑回退到 MCAS 的策略，然后 MCAS 允许只读访问，但不允许上传。
* 将策略命名为，以确保在中断期间轻松找到它们。 在策略名称中包括以下元素：
  * 策略的*标签号*。
  * 要显示的文本，此策略仅适用于紧急情况。 例如：**在紧急情况下启用**
  * 应用的*中断*。 例如：**在 MFA 中断期间**
  * 用于显示订单的*序列号*，必须激活策略。
  * 应用到的*应用*。
  * 要应用的*控件*。
  * 要求的*条件*。
  
以下应急策略的命名标准如下： 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

下面的示例：**一个用于还原对关键任务的协作应用程序的访问的应急 CA 策略示例**，是典型的公司应急措施。 在此方案中，组织通常要求对所有 Exchange Online 和 SharePoint Online 访问进行 MFA，而在这种情况下，客户端的 MFA 提供程序有中断（无论是 Azure MFA、本地 MFA 提供程序还是第三方 MFA）。 此策略通过允许特定的目标用户从受信任的 Windows 设备访问这些应用时，仅在他们从受信任的企业网络访问应用时，才能减少此中断。 它还将从这些限制中排除紧急帐户和核心管理员。 然后，目标用户将获得对 Exchange Online 和 SharePoint Online 的访问权限，而其他用户仍将无法访问应用，因为服务中断。 此示例将需要一个名为 "网络位置**CorpNetwork** " 的安全组，以及一个包含目标用户的安全组 " **ContingencyAccess** "、"具有核心管理员的名为**CoreAdmins**的组"，以及一个名为 " **EmergencyAccess** "、"紧急访问帐户 应急要求使用四个策略来提供所需的访问权限。 

**用于还原对关键任务的协作应用的访问权限的应急 CA 策略示例：**

* 策略1：需要适用于 Exchange 和 SharePoint 的已加入域的设备
  * 名称： EM001-在紧急情况下启用： MFA 中断 [1/4]-Exchange SharePoint-需要混合 Azure AD 联接
  * 用户和组：包括 ContingencyAccess。 排除 CoreAdmins 和 EmergencyAccess
  * 云应用： Exchange Online 和 SharePoint Online
  * 条件：任意
  * 授权控制：需要加入域
  * 状态：已禁用
* 策略2：阻止 Windows 以外的平台
  * 名称： EM002-在紧急情况下启用： MFA 中断 [2/4]-Exchange SharePoint-阻止访问，Windows 除外
  * 用户和组：包含所有用户。 排除 CoreAdmins 和 EmergencyAccess
  * 云应用： Exchange Online 和 SharePoint Online
  * 条件：设备平台包括所有平台，排除 Windows
  * 授予控制权限：阻止
  * 状态：已禁用
* 策略3：阻止除 CorpNetwork 以外的网络
  * 名称： EM003-在紧急情况下启用： MFA 中断 [3/4]-Exchange SharePoint-阻止访问，企业网络除外
  * 用户和组：包含所有用户。 排除 CoreAdmins 和 EmergencyAccess
  * 云应用： Exchange Online 和 SharePoint Online
  * 条件：位置包含任何位置，排除 CorpNetwork
  * 授予控制权限：阻止
  * 状态：已禁用
* 策略4：显式阻止 EAS
  * 名称： EM004-在紧急情况下启用： MFA 中断 [4/4]-Exchange-阻止所有用户使用 EAS
  * 用户和组：包括所有用户
  * 云应用：包括 Exchange Online
  * 条件：客户端应用： Exchange Active Sync
  * 授予控制权限：阻止
  * 状态：已禁用

激活顺序：

1. 排除现有 MFA 策略中的 ContingencyAccess、CoreAdmins 和 EmergencyAccess。 验证 ContingencyAccess 中的用户是否可以访问 SharePoint Online 和 Exchange Online。
2. 启用策略1：验证不在排除组中的已加入域的设备上的用户是否能够访问 Exchange Online 和 SharePoint Online。 验证 "排除" 组中的用户可以从任何设备访问 SharePoint Online 和 Exchange。
3. 启用策略2：验证不在排除组中的用户是否无法从其移动设备访问 SharePoint Online 和 Exchange Online。 验证 "排除" 组中的用户可以从任何设备（Windows/iOS/Android）访问 SharePoint 和 Exchange。
4. 启用策略3：验证不在排除组中的用户是否无法访问 SharePoint 和 Exchange 企业网络（即使已加入域的计算机）。 验证 "排除" 组中的用户可以从任何网络访问 SharePoint 和 Exchange。
5. 启用策略4：验证所有用户不能从移动设备上的本机邮件应用程序中获取 Exchange Online。
6. 禁用 SharePoint Online 和 Exchange Online 的现有 MFA 策略。

在下一个示例中，**示例 B-应急 CA 策略允许移动访问 Salesforce**，企业应用的访问权限已还原。 在此方案中，客户通常要求其销售员工从移动设备访问 Salesforce （使用 Azure AD 进行单一登录，配置为单一登录），以便仅允许从合规设备使用。 在这种情况下的中断是，评估设备符合性时出现问题，并且发生服务中断时，销售团队需要访问 Salesforce 才能关闭交易。 这些应急策略将授予关键用户从移动设备访问 Salesforce 的权限，以便他们可以继续关闭交易，而不会中断业务。 在此示例中， **SalesforceContingency**包含需要保留访问权限的所有销售雇员， **SalesAdmins**包含必要的 Salesforce 管理员。

**示例 B-应急 CA 策略：**

* 策略1：阻止不在 SalesContingency 团队中的每个人
  * 名称： EM001-在紧急情况下启用：设备合规性中断 [1/2]-Salesforce-阻止所有用户（SalesforceContingency 除外）
  * 用户和组：包含所有用户。 排除 SalesAdmins 和 SalesforceContingency
  * 云应用： Salesforce。
  * 条件：无
  * 授予控制权限：阻止
  * 状态：已禁用
* 策略2：通过移动以外的任何平台阻止销售团队（以降低攻击面）
  * 名称： EM002-在紧急情况下启用：设备一致性中断 [2/2]-Salesforce-阻止除 iOS 和 Android 之外的所有平台
  * 用户和组：包括 SalesforceContingency。 排除 SalesAdmins
  * 云应用： Salesforce
  * 条件：设备平台包括所有平台，排除 iOS 和 Android
  * 授予控制权限：阻止
  * 状态：已禁用

激活顺序：

1. 从 Salesforce 的现有设备符合性策略中排除 SalesAdmins 和 SalesforceContingency。 验证 SalesforceContingency 组中的用户是否可以访问 Salesforce。
2. 启用策略1：验证 SalesContingency 之外的用户无法访问 Salesforce。 验证 SalesAdmins 和 SalesforceContingency 中的用户是否可以访问 Salesforce。
3. 启用策略2：验证 SalesContingency 组中的用户是否无法从其 Windows/Mac 便携式计算机访问 Salesforce，但仍可从其移动设备进行访问。 验证 SalesAdmin 仍可从任何设备访问 Salesforce。
4. 禁用 Salesforce 的现有设备符合性策略。

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>部署密码哈希同步（即使你是联合身份验证或使用传递身份验证）

如果满足以下条件，也可能发生用户锁定：

- 你的组织将混合标识解决方案用于直通身份验证或联合身份验证。
- 本地标识系统（如 Active Directory、AD FS 或从属组件）不可用。 
 
为了获得更高的弹性性，你的组织应[启用密码哈希同步](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)，因为在本地标识系统关闭的情况下，你可以[切换到使用密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin)。

#### <a name="microsoft-recommendations"></a>Microsoft 建议
 使用 Azure AD Connect 向导启用密码哈希同步，无论你的组织使用的是联合身份验证还是传递身份验证。

>[!IMPORTANT]
> 不需要将用户从联合身份验证转换为使用密码哈希同步。

## <a name="during-a-disruption"></a>中断期间

如果选择实施缓解计划，将能够自动经受单个访问控制中断。 但是，如果你选择创建应变计划，你将能够在访问控制中断期间激活你的应变策略：

1. 启用向目标用户授予特定网络访问特定应用的应急策略。
2. 禁用常规的基于控制的策略。

### <a name="microsoft-recommendations"></a>Microsoft 建议

根据在中断期间使用的缓解措施或偶然因素，你的组织可能只是使用密码授予访问权限。 无需谨慎权衡安全风险。 组织必须：

1. 作为更改控制策略的一部分，记录每个更改和以前的状态，以便在访问控制完全操作后立即回滚已实现的任何意外情况。
2. 假设恶意执行组件在禁用 MFA 时，会尝试通过密码喷涂或仿冒攻击获取密码。 同样，错误的执行组件可能已经有一些密码，而这些密码先前并未向此窗口中尝试的任何资源授予访问权限。 对于高级用户（如高级管理人员），可以通过在为其禁用 MFA 之前重置密码来部分降低风险。
3. 存档所有登录活动，以确定在禁用 MFA 期间访问哪些内容。
4. 会审此窗口期间[报告的所有风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。

## <a name="after-a-disruption"></a>中断后

还原引发中断的服务后，撤消已激活应变计划的一部分所做的更改。 

1. 启用常规策略
2. 禁用你的应急策略。 
3. 回滚在中断过程中所做的任何其他更改。
4. 如果你使用了紧急访问帐户，请记住重新生成凭据，并在你的紧急访问帐户过程中物理保护新的凭据详细信息。
5. 继续会审发生可疑活动中断后[报告的所有风险检测](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。
6. 撤消[使用 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)颁发的所有刷新令牌以面向一组用户。 撤消所有刷新令牌对于在中断期间使用的特权帐户很重要，并且执行该操作将强制用户重新进行身份验证并满足对还原策略的控制。

## <a name="emergency-options"></a>紧急选项

 在紧急情况下，如果你的组织以前未实施缓解或应变计划，请在 "[用户锁定的紧急](#contingencies-for-user-lockout)情况" 部分中执行建议，前提是他们已使用条件性访问策略来强制执行 MFA。
如果你的组织使用的是基于用户的 MFA 旧策略，则可以考虑使用以下替代方法：

1. 如果你有企业网络出站 IP 地址，则可以将其添加为受信任的 ip，以便仅对企业网络启用身份验证。
   1. 如果你没有出站 IP 地址的清单，或者你需要启用企业网络内部和外部的访问权限，则可以通过指定 0.0.0.0/1 和 128.0.0.0/1 将整个 IPv4 地址空间添加为受信任的 Ip。

>[!IMPORTANT]
 > 如果你放宽了受信任的 IP 地址以取消阻止访问，将不会生成与 IP 地址（例如，不可能的旅行或不熟悉的位置）关联的风险检测。

>[!NOTE]
 > 为 Azure MFA 配置[受信任的 ip](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings)仅适用于[Azure AD Premium 许可证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)。

## <a name="learn-more"></a>了解更多信息

* [Azure AD 身份验证文档](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [管理 Azure AD 中的紧急访问管理帐户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [在 Azure Active Directory 中配置命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-msoldomainfederationsettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [如何配置已加入混合 Azure Active Directory 设备](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Windows Hello 企业版部署指南](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [密码指南-Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Azure Active Directory 条件访问中的条件是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [Azure Active Directory 条件访问中的访问控制是什么？](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
