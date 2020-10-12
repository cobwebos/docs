---
title: 排查自助密码重置问题 - Azure Active Directory
description: 了解如何排查在 Azure Active Directory 中进行自助式密码重置时遇到的常见问题及其解决方法步骤
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a02d46688fa49401684f836a7c289906affb413
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90030015"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>排查在 Azure Active Directory 中进行自助式密码重置时遇到的问题

通过 Azure Active Directory (Azure AD) 自助式密码重置 (SSPR)，用户可在云中重置自己的密码。

如果你遇到 SSPR 方面的问题，以下故障排除步骤和常见错误可能会有所帮助。 如果找不到问题的解答，[我们的支持团队始终愿意提供](#contact-microsoft-support)进一步的帮助。

## <a name="sspr-configuration-in-the-azure-portal"></a>Azure 门户中的 SSPR 配置

如果在 Azure 门户中查看或配置 SSPR 选项时遇到问题，请查看以下故障排除步骤：

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Azure 门户中的“Azure AD”下面未显示“密码重置”部分。

如果未向执行该操作的管理员分配 Azure AD 许可证，则不会看到“密码重置”菜单选项。

若要向相关管理员帐户分配许可证，请按照以下步骤来[分配、验证许可证和解决相关问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

### <a name="i-dont-see-a-particular-configuration-option"></a>未看到某个特定的配置选项。

UI 的许多元素都是隐藏的，直到需要它们时才会显示。 在查找特定的配置选项之前，请确保已启用此选项。

### <a name="i-dont-see-the-on-premises-integration-tab"></a>未看到“本地集成”选项卡。

只有在已下载 Azure AD Connect 并配置了功能时，本地密码写回才可见。

有关详细信息，请参阅 [Azure AD Connect 入门](../hybrid/how-to-connect-install-express.md)。

## <a name="sspr-reporting"></a>SSPR 报告

如果在 Azure 门户中遇到 SSPR 报告方面的问题，请查看以下故障排除步骤：

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>“自助密码管理”审核事件类别中未显示任何密码管理活动类型。

如果未向执行该操作的管理员分配 Azure AD 许可证，可能会发生这种情况。

若要向相关管理员帐户分配许可证，请按照以下步骤来[分配、验证许可证和解决相关问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

### <a name="user-registrations-show-multiple-times"></a>用户注册显示了多次。

目前，在用户注册时，我们会将所注册的每个单独的数据片记录为一个单独的事件。

如果希望聚合此数据并更灵活地查看此数据，可以下载报告并在 excel 中作为数据透视表打开数据。

## <a name="sspr-registration-portal"></a>SSPR 注册门户

如果用户在注册 SSPR 时遇到问题，请查看以下故障排除步骤：

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>未针对密码重置启用目录。 用户可能会看到报告“管理员未允许你使用此功能”的错误。

可为所有用户或所选的几组用户启用 SSPR，也可不对任何用户启用 SSPR。 当前只能使用 Azure 门户为 SSPR 启用一个 Azure AD 组。 支持使用嵌套组作为较广泛 SSPR 部署的一部分。 确保为所选组中的用户分配适当的许可证。

在 Azure 门户中，将“自助式密码重置已启用”配置更改为“选定”或“全部”，然后选择“保存” 。

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>用户未分配有 Azure AD 许可证。 用户可能会看到报告“管理员未允许你使用此功能”的错误。

当前只能使用 Azure 门户为 SSPR 启用一个 Azure AD 组。 支持使用嵌套组作为较广泛 SSPR 部署的一部分。 确保为所选组中的用户分配适当的许可证。 查看之前的故障排除步骤，根据需要启用 SSPR。

另请查看故障排除步骤，确保向执行配置选项的管理员分配了许可证。 若要向相关管理员帐户分配许可证，请按照以下步骤来[分配、验证许可证和解决相关问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

### <a name="theres-an-error-processing-the-request"></a>处理请求时出错。

许多问题都可能会导致出现一般性的 SSPR 注册错误，但此错误通常是由服务中断或配置问题造成的。 如果在重试 SSPR 注册时仍然看到这个一般性错误，请[联系 Microsoft 支持部门](#contact-microsoft-support)来获取更多帮助。

## <a name="sspr-usage"></a>SSPR 用法

如果你或你的用户在使用 SSPR 时遇到问题，请查看以下故障排除方案和解决步骤：

| 错误 | 解决方案 |
| --- | --- |
| 未针对密码重置启用目录。 | 在 Azure 门户中，将“自助式密码重置已启用”配置更改为“选定”或“全部”，然后选择“保存” 。 |
| 用户未分配有 Azure AD 许可证。 | 如果未向所需的用户分配 Azure AD 许可证，则可能会发生这种情况。 若要向相关管理员帐户分配许可证，请按照以下步骤来[分配、验证许可证和解决相关问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。 |
| 已针对密码重置启用了目录，但用户的身份验证信息缺失或格式错误。 | 确保用户在目录下的文件中具有格式正确的联系人数据。 有关详细信息，请参阅 [Azure AD 自助密码重置使用的数据](howto-sspr-authenticationdata.md)。 |
| 已针对密码重置启用目录，但用户在文件中只有一个联系人数据片段，而策略要求执行两种验证方法。 | 请确保用户至少有 2 个正确配置的联系方式。 例如，同时提供了移动电话号码和办公电话号码。 |
| 已针对密码重置启用目录并正确配置用户，但无法联系到用户。 | 这可能是因临时服务错误或联系人数据错误而导致的，我们无法正确检测到这些错误。 <br> <br> 如果用户等待 10 秒，会出现一个“重试”和“请联系管理员”链接。 如果用户选择“重试”，则会重新尝试电话联系。 如果用户选择“请联系管理员”，则向管理员发送一封表单电子邮件，请求其为该用户帐户执行密码重置。 |
| 用户一直收不到密码重置短信或电话呼叫。 | 这可能是由于目录中的电话号码格式不正确导致的。 请确保电话号码的格式为“+1 4251234567”。 <br> <br>即使你在目录中指定了分机，密码重置也不支持分机。 分机号码会在呼叫前被剥离。 使用不带分机的号码，或者在专用交换机 (PBX) 中将分机集成到电话号码中。 |
| 用户一直收不到密码重置电子邮件。 | 此问题的最常见原因是消息被垃圾邮件筛选器拒绝。 请检查垃圾邮件或已删除邮件文件夹中是否有电子邮件。 <br> <br> 此外，请确保用户检查已注册 SSPR 的电子邮件帐户是否正确。 |
| 我已经设置了密码重置策略，但当管理员帐户使用密码重置时，该策略未应用。 | 为确保最高级别的安全性，管理员密码重置策略由 Microsoft 进行管理和控制。 |
| 一天中阻止用户尝试密码重置的次数太多。 | 自动扼制机制用于阻止用户在短时间内过多地尝试重置其密码。 出现以下情况时会进行扼制： <br><ul><li>用户在一小时内 5 次尝试验证某个电话号码。</li><li>用户在一小时内 5 次尝试使用安全问题入口。</li><li>用户在一小时内 5 次尝试为同一用户帐户重置密码。</li></ul>如果用户遇到此问题，必须在上次尝试后等待 24 小时。 然后用户即可重置其密码。 |
| 用户在验证其电话号码时看到了一个错误。 | 当输入的电话号码与存档的电话号码不匹配时，会发生此错误。 当尝试使用基于电话的方法进行密码重置时，请确保用户输入了完整的电话号码（包括区域和国家/地区代码）。 |
| 处理请求时出错。 | 许多问题都可能会导致出现一般性的 SSPR 注册错误，但此错误通常是由服务中断或配置问题造成的。 如果在重试 SSPR 注册过程时仍然看到这个一般性的错误，请[联系 Microsoft 支持部门](#contact-microsoft-support)来获取更多帮助。 |
| 本地策略冲突 | 该密码不符合本地 Active Directory 密码策略。 用户必须定义满足复杂性或强度要求的密码。 |
| 密码不符合模糊策略 | 使用的密码出现在 " [禁止密码" 列表](./concept-password-ban-bad.md#how-are-passwords-evaluated) 中，无法使用。 用户必须定义满足或超过禁用密码列表策略要求的密码。 |

## <a name="sspr-errors-that-a-user-might-see"></a>用户可能会看到的 SSPR 错误

在 SSPR 过程中，用户可能会看到以下错误和技术详细信息。 通常，他们自己并不能解决此问题，因为需要为他们的帐户启用、配置或注册 SSPR 功能。

请通过以下信息了解问题以及需要在 Azure AD 租户或单个用户帐户上更正的内容。

| 错误 | 详细信息 | 技术详细信息 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 抱歉，由于管理员已对组织禁用密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们启用此功能。<br /><br />若要了解详细信息，请参阅[请为我提供帮助，我忘记了 Azure AD 密码](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions)。 | SSPR_0009：我们检测到管理员尚未启用密码重置。 请联系管理员，并请求他们为组织启用密码重置。 |
| WritebackNotEnabled = 10 |抱歉，由于管理员没有为组织启用必要服务，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们检查你组织的配置。<br /><br />若要了解此必要服务的详细信息，请参阅[配置密码写回](./tutorial-enable-sspr-writeback.md)。 | SSPR_0010：我们检测到尚未启用密码写回。 请联系管理员，并请求他们启用密码写回。 |
| SsprNotEnabledInUserPolicy = 11 | 抱歉，由于管理员未为组织配置密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们配置密码重置。<br /><br />若要了解有关密码重置配置的详细信息，请参阅[快速入门：Azure AD 自助式密码重置](./tutorial-enable-sspr.md)。 | SSPR_0011：你的组织尚未定义密码重置策略。 请联系管理员，并请求他们定义密码重置策略。 |
| UserNotLicensed = 12 | 抱歉，由于组织缺少必要的许可证，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们检查许可证分配。<br /><br />若要了解有关许可的详细信息，请参阅 [Azure AD 自助密码重置的许可要求](./concept-sspr-licensing.md)。 | SSPR_0012：你的组织没有执行密码重置所需的许可证。 请联系管理员，并请求他们查阅许可证分配。 |
| UserNotMemberOfScopedAccessGroup = 13 | 抱歉，由于管理员未将帐户配置为使用密码重置，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们配置你的帐户以进行密码重置。<br /><br />若要了解有关帐户配置以进行密码重置的详细信息，请参阅[为用户推出密码重置](./howto-sspr-deployment.md)。 | SSPR_0013：你不是已启用密码重置的组的成员。 请联系管理员，并请求添加到组。 |
| UserNotProperlyConfigured = 14 | 抱歉，由于帐户缺少必要信息，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们为你重置密码。 有权重新访问帐户后，需要注册必要的信息。<br /><br />若要注册信息，请遵循[注册自助密码重置](../user-help/active-directory-passwords-reset-register.md)一文中的步骤。 | SSPR_0014：还需要提供其他安全信息才能重置密码。 若要继续，请联系管理员，并请求他们重置你的密码。 有权访问帐户后，可前往 https://aka.ms/ssprsetup 注册额外安全信息。 管理员可按照[为密码重置设置和读取身份验证数据](howto-sspr-authenticationdata.md)中所述的步骤，向帐户添加额外安全信息。 |
| OnPremisesAdminActionRequired = 29 | 抱歉，由于你的组织的密码重置配置出现问题，目前无法重置密码。 无法采取进一步操作来解决这种情况。 请联系管理员，并请求他们进行调查。 <br /><br />或<br /><br />由于你的组织的密码重置配置出现问题，目前无法重置密码。 无法采取进一步操作来解决此问题。 请联系管理员，并请求他们进行调查。<br /><br />若要了解有关潜在问题的详细信息，请参阅[排查密码写回问题](troubleshoot-sspr-writeback.md)。 | SSPR_0029：由于本地配置中的错误，无法重置你的密码。 请联系管理员，并请求他们进行调查。 |
| OnPremisesConnectivityError = 30 | 抱歉，由于组织的连接问题，目前无法重置密码。 当前没有可执行的操作，但如果稍后重新尝试，问题有可能已得到解决。 如果问题仍然存在，请联系管理员，并请求他们进行调查。<br /><br />若要了解有关连接问题的详细信息，请参阅[排查密码写回连接问题](troubleshoot-sspr-writeback.md)。 | SSPR_0030：由于与本地环境的连接不佳，无法重置你的密码。 请联系管理员，并请求他们进行调查。|

## <a name="azure-ad-forums"></a>Azure AD 论坛

如果遇到有关 Azure AD 和自助式密码重置的一般性问题，可在 [Azure Active Directory 的 Microsoft Q&A 问答页](/answers/topics/azure-active-directory.html)中请求社区帮助。 社区的成员包括工程师、产品经理、MVP 和其他 IT 专业人员。

## <a name="contact-microsoft-support"></a>请与 Microsoft 支持部门联系

如果找不到问题的解答，我们的支持团队始终愿意提供进一步的帮助。

为了能够提供适当的帮助，我们希望你在建立支持案例时提供尽量多的详细信息。 这些详细信息包括：

* **错误的一般描述**：错误是什么？ 看到该错误时出现了哪种行为？ 我们如何再现该错误？ 请尽量提供详尽的信息。
* **页面**：在哪个页面上看到了该错误？ 请附送页面的 URL（如果可以）和屏幕截图。
* **支持代码**：用户看到该错误时生成了哪个支持代码？
   * 若要找到此代码，请再现错误，然后选择屏幕底部的“支持代码”链接，将生成的 GUID 发送给支持工程师。

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="支持代码位于 Web 浏览器窗口的右下角。":::

  * 如果所在页面的底部没有支持代码，请按 F12，搜索 SID 和 CID，然后将这两个结果发送给支持工程师。
* **日期、时间和时区**：包含发生错误时的确切日期和时间及“时区”。
* **用户 ID**：哪个用户看到了该错误？ 例如，user\@contoso.com。
   * 是否是联合用户？
   * 是否是直通身份验证用户？
   * 是否是密码哈希同步的用户？
   * 是否是仅限云的用户？
* **许可**：用户是否分配有 Azure AD 许可证？
* **应用程序事件日志**：如果你使用的是密码写回，并且错误发生在本地基础结构中，请包含 Azure AD Connect 服务器中的应用程序事件日志的压缩副本。

## <a name="next-steps"></a>后续步骤

若要详细了解 SSPR，请参阅[工作原理：Azure AD 自助式密码重置](concept-sspr-howitworks.md)或 [Azure AD 中的自助式密码重置写回如何工作？](concept-sspr-writeback.md)。
