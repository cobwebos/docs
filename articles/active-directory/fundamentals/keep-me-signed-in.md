---
title: 配置 "保持登录？" Azure Active Directory 帐户提示
description: 了解 "使我保持登录" （KMSI），这会显示 "保持登录"？提示，如何在 Azure Active Directory 门户中配置它，以及如何对登录问题进行故障排除。
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
ms.openlocfilehash: fd24e6847dbf02bc7efe5d9e6ea02043879f720b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86054706"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>配置 "保持登录？" Azure AD 帐户提示

使我保持登录状态（KMSI）在用户成功登录后，将显示 **"始终登录"** 提示。 如果用户对此提示回答 **"是"** ，则 "使我保持登录状态" 服务会为其提供一个持久的[刷新令牌](../develop/developer-glossary.md#refresh-token)。 对于联合租户，在用户成功通过联合身份验证服务进行身份验证后，会显示提示。

下图显示了托管租户和联合租户的用户登录流，以及新的 "使我保持登录状态" 提示。 此流程包含智能逻辑，以便在机器学习系统检测到来自共享设备的高风险登录或登录时，不会显示 "**保持登录"** 选项。

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="显示托管与联合租户的用户登录流的图示":::

> [!NOTE]
> 若要配置 "使我保持登录" 选项，需要使用 Azure Active Directory （Azure AD）高级版1、高级版2或基本版，或者使用 Microsoft 365 许可证。 有关许可和版本的详细信息，请参阅[注册 Azure AD Premium](active-directory-get-started-premium.md)。<br><br>Azure AD Premium 和 Basic 版本可供中国的客户使用 Azure AD 的全球实例使用。 中国区 21Vianet 运营的 Azure 服务目前不支持 Azure AD Premium 和 Azure AD Basic 版本。 有关详细信息，请使用[Azure AD 论坛](https://feedback.azure.com/forums/169401-azure-active-directory/)告诉我们。

## <a name="configure-kmsi"></a>配置 KMSI

1. 使用目录的全局管理员帐户登录到 [Azure 门户](https://portal.azure.com/)。
1. 选择 " **Azure Active Directory**"，选择 "**公司品牌**"，然后选择 "**配置**"。
1. 在 "**高级设置**" 部分中，找到 "**显示已登录的选项**" 设置。

   此设置使你可以选择你的用户是否一直登录到 Azure AD，直到它们显式注销。
   * 如果你选择 "**否**"，则在用户成功登录后将隐藏 "**保持登录状态"** 选项，用户必须在每次关闭并重新打开浏览器时登录。
   * 如果选择 **"是**"，则会向用户显示 "**保持登录"** 选项。

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="屏幕截图显示保持登录设置的 "显示" 选项":::

## <a name="troubleshoot-sign-in-issues"></a>排查登录问题

如果用户不执行 "**保持登录状态**" 提示（如下图所示），但放弃登录尝试，则会看到指示中断的登录日志项。

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="显示保持登录的情况？处":::

有关登录错误的详细信息如下所示，如示例中所示。

* **登录错误代码**：50140
* **失败原因**：此错误是用户登录时 "使我保持登录" 中断造成的。

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="带有 "使我保持登录" 中断的示例登录日志条目":::

你可以通过将高级品牌设置中的 "**显示" 选项设置为**"**否**"，阻止用户查看中断。 这会对 Azure AD 目录中的所有用户禁用 KMSI 提示。

你还可以在条件性访问中使用持久性浏览器会话控件来防止用户查看 KMSI 提示。 使用此选项可以对选择的用户组（例如全局管理员）禁用 KMSI 提示，而不会影响目录中其他用户的登录行为。 有关详细信息，请参阅[用户登录频率](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。 

若要确保仅当 KMSI 提示可为用户带来好处时显示，KMSI 提示符特意不会显示在以下方案中：

* 用户通过无缝 SSO 和集成的 Windows 身份验证（IWA）登录
* 用户通过 Active Directory 联合身份验证服务和 IWA 登录
* 用户是租户中的来宾
* 用户的风险评分为高
* 用户或管理员许可流期间发生登录
* 在条件性访问策略中配置永久性浏览器会话控制

## <a name="next-steps"></a>后续步骤

了解影响登录会话超时的其他设置：

* Microsoft 365 –[空闲会话超时](https://docs.microsoft.com/sharepoint/sign-out-inactive-users)
* Azure AD 条件访问-[用户登录频率](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)
* Azure 门户–[目录级非活动超时](https://docs.microsoft.com/azure/azure-portal/admin-timeout)
