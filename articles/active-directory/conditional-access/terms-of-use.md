---
title: 使用条款-Azure Active Directory |Microsoft Docs
description: 开始使用 Azure Active Directory 使用条款在获取访问权限之前向员工或来宾显示信息。
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dbd1d96cda88d500e16fff885dae03ebfa230f0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79079892"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 使用条款

Azure AD 使用条款提供了一个简单的方法，组织可以使用该方法向最终用户显示信息。 可以通过这样的呈现方式确保用户看到法律要求或符合性要求的相关免责声明。 本文介绍如何开始使用使用条款。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概述视频

以下视频概述了使用条款。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

有关其他视频，请参阅：
- [如何在 Azure Active Directory 中部署使用条款](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何在 Azure Active Directory 中推出使用条款](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>使用使用条款有哪些用途？

Azure AD 使用条款具有以下功能：

- 在获取访问权限之前，要求员工或来宾接受你的使用条款。
- 在获取访问权限之前，要求员工或来宾接受您在每个设备上的使用条款。
- 要求员工或来宾接受按定期计划使用的使用条款。
- 在 Azure 多重身份验证（MFA）中注册安全信息之前，要求员工或来宾接受你的使用条款。
- 要求员工在 Azure AD 自助服务密码重置（SSPR）中注册安全信息之前接受使用条款。
- 为组织中的所有用户提供一般使用条款。
- 基于用户属性显示特定的使用条款（例如 [动态组](../users-groups-roles/groups-dynamic-membership.md)中的医生与护士或国内员工和国际员工）的具体使用条款。
- 在访问高业务影响应用程序（如 Salesforce）时提供特定的使用条款。
- 提供不同语言的使用条款。
- 列出谁有或不接受您的使用条款。
- 有助于符合隐私法规。
- 显示用于相容性和审核的使用条款活动的日志。
- 使用[Microsoft Graph api](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement)创建和管理使用条款（当前为预览版）。

## <a name="prerequisites"></a>必备条件

若要使用和配置 Azure AD 使用条款，你必须具备以下条件：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 订阅。
   - 如果没有这其中的某个订阅，可以[获取 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[启用 Azure AD Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下述适用于需配置目录的管理员帐户之一：
   - 全局管理员角色
   - 安全管理员
   - 条件访问管理员

## <a name="terms-of-use-document"></a>使用条款文档

Azure AD 使用条款使用 PDF 格式来呈现内容。 此 PDF 文件可以是任意内容（例如现有的合同文档），只要能够在用户登录时收集最终用户协议即可。 若要支持移动设备上的用户，PDF 中建议的字号是 24 点。

## <a name="add-terms-of-use"></a>添加使用条款

完成使用条款文档的使用后，请使用以下过程添加它。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure。
1. 在[ 导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)

   ![条件性访问-使用条款边栏选项卡](./media/terms-of-use/tou-blade.png)

1. 单击“新建条款”。

   ![新术语 "使用" 窗格指定使用条款设置](./media/terms-of-use/new-tou.png)

1. 在 "**名称**" 框中，输入将在 Azure 门户中使用的使用条款的名称。
1. 在“显示名称”框中，输入用户登录时看到的标题。
1. 对于**使用条款文档**，请浏览到已完成的使用条款，并选择它。
1. 为使用条款选择语言文档。 可以通过语言选项上传多个版本的使用条款，每个版本的语言各不相同。 最终用户看到的使用条款版本取决于其浏览器首选项。
1. 若要要求最终用户在接受使用条款之前查看使用条款，请设置 "**要求用户将使用条款展开**为**启用**"。
1. 若要要求最终用户接受从其访问的每个设备上使用的条款，请设置 "**要求用户在每台设备上同意** **"。** 如果启用此选项，则用户可能需要安装其他应用程序。 有关详细信息，请参阅[按设备使用条款](#per-device-terms-of-use)。
1. 如果要按计划使使用条款同意过期，请将**同意过期**为 **"开"** 。 设置为“打开”时，会显示另外两项计划设置。

   ![使同意设置过期以设置开始日期、频率和持续时间](./media/terms-of-use/expire-consents.png)

1. 使用**过期开始时间**和**频率**设置来指定使用期限的计划。 下表显示了几项示例设置的结果：

   | 过期开始日期 | 频率 | 结果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 如今，用户必须接受使用条款，然后每个月面向。 |
   | 将来的日期  | 每月 | 如今，用户必须接受使用条款。 到达指定的将来日期时，同意状态将会过期，以后用户必须每个月接受使用条款。  |

   例如，如果将过期开始日期设置为“1 月 1 日”，将频率设置为“每月”，则两个用户的过期计划如下：

   | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用 "**重新接受前的持续时间" 需要（天）** 设置以指定用户必须面向的使用条款。 这可以让用户遵照自己的计划。 例如，如果将持续时间设置为 **30** 天，则两个用户的计划如下：

   | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   可以结合使用“使同意状态过期”和“需要重新接受使用条款之前的持续时间(天)”设置，但一般只使用其中的一项。

1. 在 "**条件性访问**" 下，使用 "**使用条件性访问策略模板**" 列表选择模板以强制使用使用条款。

   ![选择策略模板的 "条件性访问" 下拉列表](./media/terms-of-use/conditional-access-templates.png)

   | 模板 | 说明 |
   | --- | --- |
   | **所有来宾对云应用的访问权限** | 将为所有来宾和所有云应用创建条件性访问策略。 此策略会影响 Azure 门户。 创建后，可能需要注销再登录。 |
   | **所有用户对云应用的访问权限** | 将为所有用户和所有云应用创建条件性访问策略。 此策略会影响 Azure 门户。 创建后，需要注销再登录。 |
   | **自定义策略** | 选择将应用此使用条款的用户、组和应用。 |
   | **稍后创建条件性访问策略** | 创建条件性访问策略时，此使用条款将出现在 "授权控制" 列表中。 |

   >[!IMPORTANT]
   >条件访问策略控制（包括使用条款）不支持对服务帐户执行强制。 建议从条件访问策略中排除所有服务帐户。

    自定义条件访问策略可用于特定的云应用程序或用户组，从而实现精细的使用条款。 有关详细信息，请参阅[快速入门：访问云应用之前要求接受使用条款](require-tou.md)。

1. 单击“创建”。

   如果选择了自定义条件访问模板，则会出现一个新屏幕，允许您创建自定义条件访问策略。

   ![如果选择自定义条件访问策略模板，则新建条件访问窗格](./media/terms-of-use/custom-policy.png)

   你现在应该会看到你的新使用条款。

   ![使用条款边栏选项卡中列出的新使用条款](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>查看接受用户和拒绝用户的报告

“使用条款”边栏选项卡会显示接受用户和拒绝用户的计数。 在使用条款的使用期限内，这些计数和接受/拒绝的人都将进行存储。

1. 在[ 登录到 Azure 并导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)

   ![使用条款边栏选项卡中列出了已接受并拒绝用户显示的数量](./media/terms-of-use/view-tou.png)

1. 对于 "使用条款"，请单击 "**被接受**" 或 "已**拒绝**" 下的数字查看用户的当前状态。

   ![列出已接受用户的使用条款同意窗格](./media/terms-of-use/accepted-tou.png)

1. 若要查看单个用户的历史记录，请依次单击省略号 ( **...** )、“查看历史记录”。

   ![查看用户的 "查看历史记录" 上下文菜单](./media/terms-of-use/view-history-menu.png)

   在“查看历史记录”窗格中，可以看到所有接受、拒绝和过期操作状态的历史记录。

   !["查看历史记录" 窗格列出用户接受、拒绝和过期的历史记录](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>查看 Azure AD 审核日志

若要查看其他活动，Azure AD 使用条款包括审核日志。 每次用户许可都会在存储时间为 **30 天**的审核日志中触发一个事件。 可以在门户中查看这些日志，或者将其下载为 .csv 文件。

若要开始使用 Azure AD 审核日志，请执行以下过程：

1. 在[ 登录到 Azure 并导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)
1. 选择使用条款。
1. 单击“查看审核日志”。

   ![突出显示 "查看审核日志" 选项的使用条款边栏选项卡](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 审核日志屏幕上，可以使用提供的列表筛选信息，将特定审核日志信息作为目标。

   还可以单击“下载”，将信息下载到可以在本地使用的 .csv 文件中。

   ![Azure AD 审核日志屏幕列出日期、目标策略、启动者和活动](./media/terms-of-use/audit-logs-tou.png)

   如果单击某项日志，窗格中会显示更多活动详细信息。

   ![显示活动、活动状态、启动者、目标策略的日志活动详细信息](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>用户的使用条款

创建并强制使用使用条款后，用户（处于范围内）将在登录时看到以下屏幕。

![用户登录时显示的示例使用条款](./media/terms-of-use/user-tou.png)

用户可以查看使用条款，如有必要，请使用按钮进行放大和缩小。

![使用缩放按钮查看使用条款](./media/terms-of-use/zoom-buttons.png)

以下屏幕显示了使用条款在移动设备上的外观。

![用户登录移动设备时显示的示例使用条款](./media/terms-of-use/mobile-tou.png)

只需接受一次使用条款，用户就不会在后续登录时再次看到使用条款。

### <a name="how-users-can-review-their-terms-of-use"></a>用户如何查看其使用条款

用户可以使用以下过程查看和查看他们已接受的使用条款。

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角单击自己的姓名，然后选择“个人资料”。

   ![已打开用户窗格的 MyApps 网站](./media/terms-of-use/tou14.png)

1. 在“个人资料”页中单击“查看使用条款”。

   ![用户的 "配置文件" 页，显示 "查看使用条款" 链接](./media/terms-of-use/tou13a.png)

1. 可以在其中查看已接受的使用条款。

## <a name="edit-terms-of-use-details"></a>编辑使用条款详细信息

您可以编辑某些使用条款的详细信息，但不能修改现有文档。 以下过程介绍如何编辑详细信息。

1. 在[ 登录到 Azure 并导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)
1. 选择要编辑的使用条款。
1. 单击“编辑条款”。
1. 在“编辑使用条款”窗格中，更改名称、显示名称，或要求用户展开值。

   如果要更改其他设置（如 PDF 文档），要求用户在每个设备上同意，使同意过期、在 reacceptance 之前的持续时间或条件访问策略，则必须创建新的使用条款。

   !["编辑使用条款" 窗格显示名称和展开选项](./media/terms-of-use/edit-tou.png)

1. 单击“保存”以保存更改。

   保存更改后，用户必须重新接受这些编辑内容。

## <a name="add-a-terms-of-use-language"></a>添加使用条款语言

以下过程介绍如何添加使用条款。

1. 在[ 登录到 Azure 并导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)
1. 选择要编辑的使用条款。
1. 在详细信息窗格中，单击“语言”选项卡。

   ![使用条款在详细信息窗格中选择并显示 "语言" 选项卡](./media/terms-of-use/languages-tou.png)

1. 单击“添加语言”。
1. 在“添加使用条款语言”窗格中，上传已本地化的 PDF 并选择语言。

   ![使用上传本地化 Pdf 选项添加使用条款语言窗格](./media/terms-of-use/language-add-tou.png)

1. 单击“添加”以添加该语言。

## <a name="per-device-terms-of-use"></a>每设备使用条款

"**要求用户同意每个设备"** 设置使你能够要求最终用户接受其所访问的每个设备上的使用条款。 最终用户必须将其设备加入 Azure AD。 设备加入后，使用设备 ID 在每个设备上强制实施使用条款。

下面是支持的平台和软件列表。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **本机应用** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome（带扩展）** | 是 | 是 | 是 |  |

每个设备的使用条款有以下限制：

- 一个设备只能加入到一个租户。
- 用户必须有权加入其设备。
- 不支持 Intune 注册应用。
- 不支持 Azure AD B2B 用户。

如果用户的设备未加入，他们将收到一条消息，指出需要加入其设备。 他们的体验取决于平台和软件。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 设备

如果用户使用 Windows 10 和 Microsoft Edge，将会收到如下所示的，指出需要[加入其设备](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)的消息。

![Windows 10 和 Microsoft Edge-指示设备必须已注册的消息](./media/terms-of-use/per-device-win10-edge.png)

如果用户使用 Chrome，系统会提示他们安装 [Windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

### <a name="register-an-ios-device"></a>注册 iOS 设备

如果用户使用的是 iOS 设备，则系统会提示他们安装[Microsoft Authenticator 应用](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)。

### <a name="register-an-android-device"></a>注册 Android 设备

如果用户正在使用 Android 设备，则系统会提示他们安装[Microsoft Authenticator 应用](https://play.google.com/store/apps/details?id=com.azure.authenticator)。

### <a name="browsers"></a>浏览器

如果用户使用不受支持的浏览器，系统会要求他们使用其他浏览器。

![表明你的设备必须已注册的消息，但不支持浏览器](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>删除使用条款

您可以使用以下过程删除旧的使用条款。

1. 在[ 登录到 Azure 并导航到“使用条款”。 https://aka.ms/catou](https://aka.ms/catou)
1. 选择你想要删除的使用条款。
1. 单击“删除条款”。
1. 在出现的询问是否需要继续的消息中，单击“是”。

   ![请求确认删除使用条款的消息](./media/terms-of-use/delete-tou.png)

   你应该不会再看到你的使用条款。

## <a name="deleted-users-and-active-terms-of-use"></a>已删除用户和活动使用条款

默认情况下，删除的用户会在 Azure AD 中保持“已删除”状态 30 天，在此期间，管理员可以根据需要还原这些用户。 30 天后，该用户将被永久删除。 此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。 一个用户已被永久删除，有关该用户的后续数据将从 "活动使用条款" 中删除。 有关已删除用户的审核信息仍保留在审核日志中。

## <a name="policy-changes"></a>策略更改

条件性访问策略将立即生效。 发生此情况时，管理员就会看到“表示遗憾的阴云”或“Azure AD 令牌问题”。 管理员必须注销然后重新登录才能符合新策略的要求。

> [!IMPORTANT]
> 在以下情况下，处于范围的用户必须在注销后登录才能符合新策略的要求：
>
> - 条件性访问策略在使用条款上启用
> - 或创建了第二个使用条款

## <a name="b2b-guests-preview"></a>B2B 来宾（预览版）

大多数组织都有适当的流程，使其员工同意其组织的使用条款和隐私声明。 但是，对于通过 SharePoint 或 Teams 添加的 Azure AD 企业到企业 (B2B) 来宾，如何强制要求他们同样表示同意？ 使用条件性访问和使用条款，你可以直接对 B2B 来宾用户强制实施策略。 在邀请兑换流期间，用户会看到使用条款。 这种支持目前处于预览状态。

仅当用户在 Azure AD 中具有来宾帐户时，才显示使用条款。 SharePoint Online 当前有一个[即席外部共享接收方体验](/sharepoint/what-s-new-in-sharing-in-targeted-release)，可共享不需要用户拥有来宾帐户的文档或文件夹。 在这种情况下，将不会显示使用条款。

![已选中 "用户和组" 窗格-"包括所有来宾用户的选项卡" 选项](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>云应用支持（预览版）

可对不同的云应用（例如 Azure 信息保护和 Microsoft Intune）使用使用条款。 这种支持目前处于预览状态。

### <a name="azure-information-protection"></a>Azure 信息保护

可以为 Azure 信息保护应用配置条件访问策略，并在用户访问受保护的文档时要求使用使用条款。 这会在用户首次访问受保护文档之前触发使用条款。

![已选择 Microsoft Azure 信息保护应用的云应用窗格](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 注册

你可以配置 Microsoft Intune 注册应用的条件性访问策略，并在 Intune 中注册设备之前要求使用使用条款。 有关详细信息，请阅读[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

![已选择 Microsoft Intune 应用的云应用窗格](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Intune 注册应用不支持[按设备使用条款](#per-device-terms-of-use)。

## <a name="frequently-asked-questions"></a>常见问题

**问：如何查看用户何时/是否接受了使用条款？**<br />
答：在“使用条款”边栏选项卡中单击“已接受”下的数字。 也可在 Azure AD 审核日志中查看或搜索接受活动。 有关详细信息，请参阅“查看接受用户和拒绝用户的报告”以及[查看 Azure AD 审核日志](#view-azure-ad-audit-logs)。

**问：信息的存储时间为多长？**<br />
答：用户在使用条款报表中计数，并在使用条款的使用期限内存储接受/拒绝的用户。 Azure AD 审核日志的存储时间为 30 天。

**问：为什么在使用条款报表与 Azure AD 审核日志中看到不同数量的同意？**<br />
答：使用条款报表在该使用条款的生存期内存储，而 Azure AD 审核日志存储30天。 而且，使用条款报表仅显示用户当前同意状态。 例如，如果用户拒绝后接受，则 "使用条款" 报表将仅显示该用户的 "接受"。 如果需要查看历史记录，可以使用 Azure AD 审核日志。

**问：如果我编辑了使用条款的详细信息，是否要求用户再次接受？**<br />
答：不能，如果管理员编辑了使用条款的详细信息（名称、显示名称、要求用户展开或添加语言），则不要求用户面向新的条款。

**问：我是否可以更新现有的使用条款文档？**<br />
答：目前，无法更新现有使用条款文档。 若要更改使用条款文档，必须创建新的使用条款实例。

**问：如果超链接位于使用条款 PDF 文档中，最终用户是否能够单击它们？**<br />
答：是的，最终用户可以选择指向其他页面的超链接，但不支持链接到文档中的部分。

**问：使用条款是否支持多种语言？**<br />
答：是的。 目前，管理员可以为单一使用条款配置108种不同的语言。 管理员可以上传多个 PDF 文档，并使用相应的语言（最多 108 种）标记这些文档。 当最终用户登录时，我们会查看其浏览器语言首选项，并显示匹配的文档。 如果没有匹配项，我们将显示默认文档，即上传的第一个文档。

**问：什么时候会触发使用条款？**<br />
答：在登录体验期间触发使用条款。

**问：我可以将哪些应用程序作为使用条款的目标？**<br />
答：可以使用新式身份验证在企业应用程序上创建条件性访问策略。 有关详细信息，请参阅[企业应用程序](./../manage-apps/view-applications-portal.md)。

**问：是否可以向给定用户或应用添加多个使用条款？**<br />
答：可以，通过创建面向这些组或应用程序的多个条件性访问策略。 如果用户处于多种使用条款范围内，则他们一次接受一项使用条款。

**问：用户拒绝使用条款后会发生什么？**<br />
答：将阻止此用户访问该应用程序。 用户需要重新登录并接受条款才能获取访问权限。

**问：是否可以 unaccept 以前接受的使用条款？**<br />
答：你可以[查看以前接受的使用条款](#how-users-can-review-their-terms-of-use)，但目前尚不能 unaccept。

**问：如果我还使用 Intune 条款和条件，会发生什么情况？**<br />
答：如果你已配置 Azure AD 使用条款和[Intune 条款和条件](/intune/terms-and-conditions-create)，则用户需要接受这两者。 有关详细信息，请参阅[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

**问：使用条款服务使用哪些终结点进行身份验证？**<br />
答：使用条款利用以下用于身份验证的终结点： https://tokenprovider.termsofuse.identitygovernance.azure.com 和 https://account.activedirectory.windowsazure.com。 如果你的组织有用于注册的 Url 允许列表，则需要将这些终结点添加到允许列表，以及用于登录的 Azure AD 终结点。

## <a name="next-steps"></a>后续步骤

- [快速入门：在访问云应用之前要求接受使用条款](require-tou.md)
- [Azure Active Directory 中的条件性访问的最佳做法](best-practices.md)
