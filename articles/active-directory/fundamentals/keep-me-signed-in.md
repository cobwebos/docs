---
title: 为 Azure Active Directory 帐户 配置“保持登录状态？”提示
description: 了解“使我保持登录状态 (KMSI)”，它会显示“保持登录状态？”提示，以及如何在 Azure Active Directory 门户中对其进行配置、如何对登录问题进行故障排除。
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed6bc43dfc15abf2bdf9f38a5de2240d348d6fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89320250"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>为 Azure AD 帐户 配置“保持登录状态？”提示

用户成功登录后，“使我保持登录状态 (KMSI)”会显示“保持登录状态？”提示。 如果用户对此提示的回答为“是”，则“使我保持登录状态”服务向用户提供永久[刷新令牌](../develop/developer-glossary.md#refresh-token)。 对于联合租户，此提示在使用联合标识服务成功进行身份验证后显示。

下图显示了托管租户和联合租户的用户登录流，以及新的“使我保持登录状态”提示。 该流包含智能逻辑，如果机器学习系统检测到高风险登录或来自共享设备的登录，就不会显示“保持登录状态？”选项。

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="显示托管与联合租户的用户登录流的图示":::

> [!NOTE]
> 配置“使我保持登录状态”选项需要使用 Azure Active Directory (Azure AD) Premium 1、Premium 2 或 Basic 版本，或拥有 Microsoft 365 许可证。 有关许可和版本的详细信息，请参阅[注册 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>在中国，使用 Azure AD 全球实例的客户可以使用 Azure AD Premium 和 Basic 版本。 中国区 21Vianet 运营的 Azure 服务目前不支持 Azure AD Premium 和 Azure AD Basic 版本。 有关详细信息，请通过 [Azure AD 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)与我们联系。

## <a name="configure-kmsi"></a>配置 KMSI

1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。
1. 依次选择“Azure Active Directory”、“公司品牌”和“配置”  。
1. 在“高级设置”部分中，找到“显示保持登录状态的选项”设置 。

   使用此设置，你可以选择是否在用户显式注销之前使用户保持登录到 Azure AD 的状态。
   * 如果选择“否”，则在用户成功登录后隐藏“保持登录状态？”选项，用户在浏览器每次关闭并重新打开时都需要登录 。
   * 如果选择“是”，则向用户显示“保持登录状态？”选项 。

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="显示托管与联合租户的用户登录流的图示":::

## <a name="troubleshoot-sign-in-issues"></a>排查登录问题

如果用户未在显示“保持登录状态？”提示时进行操作，如下图所示，而是放弃登录尝试，你将看到一个指示过程中断的登录日志条目。

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="显示托管与联合租户的用户登录流的图示":::

有关登录错误的详细信息如下所示，并在示例中突出显示。

* **登录错误代码**：50140
* **失败原因**：此错误是由于用户登录时出现“使我保持登录状态”中断而发生的。

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="显示托管与联合租户的用户登录流的图示":::

你可以通过在高级品牌设置中将“显示保持登录状态的选项”设置为“否”来阻止用户看到中断信息 。 对于 Azure AD 目录中的所有用户，这会禁用 KMSI 提示。

你还可以在条件访问中使用持久性浏览器会话控件来防止用户查看 KMSI 提示。 使用此选项可以对选择的用户组（例如全局管理员）禁用 KMSI 提示，而不会影响目录中其他用户的登录行为。 有关详细信息，请参阅 [用户登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)。 

为了确保 KMSI 提示只在有益于用户的情况下显示，在以下情况下我们有意不显示 KMSI 提示：

* 用户是通过无缝 SSO 和集成 Windows 身份验证 (IWA) 登录的
* 用户是通过 Active Directory 联合身份验证服务和 IWA 登录的
* 用户是租户中的来宾
* 用户的风险评分高
* 登录发生在用户或管理员同意流期间
* 持久性浏览器会话控制在条件访问策略中配置

## <a name="next-steps"></a>后续步骤

了解影响登录会话超时的其他设置：

* Microsoft 365 – [空闲会话超时](/sharepoint/sign-out-inactive-users)
* Azure AD 条件访问- [用户登录频率](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure 门户– [目录级非活动超时](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)