---
title: 使用-Azure Active Directory 使用条款 |Microsoft Docs
description: 介绍如何开始使用 Azure Active Directory 使用条款若要获取的访问权限之前向员工或客户提供信息。
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/24/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25a3eb6fdfc6928e2e414dcbe799861c3b2eb999
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576167"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory 使用条款功能
组织可以通过 Azure AD 使用条款这种简单的方法向最终用户显示信息。 可以通过这样的呈现方式确保用户看到法律要求或符合性要求的相关免责声明。 本文介绍如何快速了解使用条款。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概述视频

下面的视频提供了使用条款的快速概述。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

有关其他视频，请参阅：
- [如何部署 Azure Active Directory 使用条款](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何实行 Azure Active Directory 使用条款](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>使用条款可以用来做什么？
Azure AD 使用条款提供以下功能：
- 要求员工或来宾在获取访问权限之前先接受使用条款。
- 要求员工或来宾在获取访问权限之前先在每台设备上接受使用条款。
- 要求员工或来宾按重复性计划接受使用条款。
- 显示针对组织中所有用户的常规使用条款。
- 显示基于用户属性（例如， [动态组](../users-groups-roles/groups-dynamic-membership.md)中的医生与护士或国内员工和国际员工）的具体使用条款。
- 在用户访问业务影响大的应用程序（例如 Salesforce）时显示特定使用条款。
- 以不同的语言显示使用条款。
- 列出已接受或尚未接受使用条款的人员。
- 有助于符合隐私法规。
- 显示使用条款活动的日志，以了解合规性和进行审核。
- 使用 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement)（目前提供预览版）创建和管理使用条款。

## <a name="prerequisites"></a>必备组件
若要使用和配置 Azure AD 使用条款，必须具备以下先决条件：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 订阅。
    - 如果没有这其中的某个订阅，可以[获取 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[启用 Azure AD Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下述适用于需配置目录的管理员帐户之一：
    - 全局管理员角色
    - 安全管理员
    - 条件访问管理员

## <a name="terms-of-use-document"></a>使用条款文档

Azure AD 使用条款使用 PDF 格式显示内容。 此 PDF 文件可以是任意内容（例如现有的合同文档），只要能够在用户登录时收集最终用户协议即可。 若要支持移动设备上的用户，PDF 中建议的字号是 24 点。

## <a name="add-terms-of-use"></a>添加使用条款
完成使用条款文档后，请使用以下过程添加它。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 导航到“使用条款”。

    ![“使用条款”边栏选项卡](./media/active-directory-tou/tou-blade.png)

1. 单击“新建条款”。

    ![添加 TOU](./media/active-directory-tou/new-tou.png)

1. 在“名称”框中，输入要在 Azure 门户中使用的使用条款的名称。

1. 在“显示名称”框中，输入用户登录时看到的标题。

1. 对于“使用条款文档”，请浏览到已完成的使用条款 (PDF)，并将其选中。

1. 选择使用条款文档的语言。 可以通过语言选项上传多个版本的使用条款，每个版本的语言各不相同。 最终用户看到的使用条款版本取决于其浏览器首选项。

1. 如果要求最终用户在接受使用条款之前先查看条款，请将“要求用户展开使用条款”设置为“打开”。

1. 如果要求最终用户在每台访问设备上接受使用条款，请将“要求用户在每台设备上同意”设置为“打开”。 有关详细信息，请参阅[按设备实施的使用条款](#per-device-terms-of-use)。

1. 若要按计划使使用条款同意状态过期，请将“使同意状态过期”设置为“打开”。 设置为“打开”时，会显示另外两项计划设置。

    ![使同意状态过期](./media/active-directory-tou/expire-consents.png)

1. 使用“过期开始日期”和“频率”设置来指定使用条款过期的计划。 下表显示了几项示例设置的结果：

    | 过期开始日期 | 频率 | 结果 |
    | --- | --- | --- |
    | 今天的日期  | 每月 | 从今天开始，用户必须接受使用条款，并且以后每个月都要接受。 |
    | 将来的日期  | 每月 | 从今天开始，用户必须接受使用条款。 到达指定的将来日期时，同意状态将会过期，以后用户必须每个月接受使用条款。  |

    例如，如果将过期开始日期设置为“1 月 1 日”，将频率设置为“每月”，则两个用户的过期计划如下：

    | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
    | --- | --- | --- | --- | --- |
    | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
    | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用“需要重新接受使用条款之前的持续时间(天)”设置来指定用户必须重新接受使用条款之前所要经过的天数。 这可以让用户遵照自己的计划。 例如，如果将持续时间设置为 **30** 天，则两个用户的计划如下：

    | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
    | --- | --- | --- | --- | --- |
    | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
    | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

    可以结合使用“使同意状态过期”和“需要重新接受使用条款之前的持续时间(天)”设置，但一般只使用其中的一项。

1. 在“条件访问”下，使用“使用条件访问策略模板强制实施”列表选择用于强制实施使用条款的模板。

    ![条件访问模板](./media/active-directory-tou/conditional-access-templates.png)

    | 模板 | 描述 |
    | --- | --- |
    | **所有来宾对云应用的访问权限** | 将会针对所有来宾和所有云应用创建一个条件访问策略。 此策略会影响 Azure 门户。 创建后，可能需要注销再登录。 |
    | **所有用户对云应用的访问权限** | 将会针对所有用户和所有云应用创建条件访问策略。 此策略会影响 Azure 门户。 创建后，需要注销再登录。 |
    | **自定义策略** | 选择此使用条款适用的用户、组和应用。 |
    | **稍后创建条件访问策略** | 此使用条款将显示在创建条件访问策略时弹出的授予控制权列表中。 |

    >[!IMPORTANT]
    >条件访问策略控制（包括使用条款）不支持对服务帐户强制实施。 我们建议从条件访问策略中排除所有服务帐户。

     可以使用自定义条件访问策略将使用条款细化，向下细化到特定云应用程序或用户组。 有关详细信息，请参阅[快速入门：在访问云应用之前要求接受使用条款](../conditional-access/require-tou.md)。

1. 单击“创建”。

    选择自定义条件访问模板后，会显示一个新的屏幕用于创建自定义的条件访问策略。

    ![自定义策略](./media/active-directory-tou/custom-policy.png)

    现在会看到新的使用条款。

    ![添加 TOU](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>查看接受用户和拒绝用户的报告
“使用条款”边栏选项卡会显示接受用户和拒绝用户的计数。 在使用条款有效期内，会存储这些计数以及接受用户/拒绝用户。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

    ![“使用条款”边栏选项卡](./media/active-directory-tou/view-tou.png)

1. 对于某个使用条款，单击“已接受”或“已拒绝”下的数字，以查看用户的当前状态。

    ![使用条款的同意](./media/active-directory-tou/accepted-tou.png)

1. 若要查看单个用户的历史记录，请依次单击省略号 (**...**)、“查看历史记录”。

    ![“查看历史记录”菜单](./media/active-directory-tou/view-history-menu.png)

    在“查看历史记录”窗格中，可以看到所有接受、拒绝和过期操作状态的历史记录。

    ![“查看历史记录”窗格](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>查看 Azure AD 审核日志
Azure AD 使用条款包括审核日志，方便你查看其他活动。 每次用户许可都会在存储时间为 **30 天**的审核日志中触发一个事件。 可以在门户中查看这些日志，或者将其下载为 .csv 文件。

若要开始使用 Azure AD 审核日志，请执行以下过程：

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 选择一个使用条款。

1. 单击“查看审核日志”。

    ![“使用条款”边栏选项卡](./media/active-directory-tou/audit-tou.png)

1. 在 Azure AD 审核日志屏幕上，可以使用提供的列表筛选信息，将特定审核日志信息作为目标。

    还可以单击“下载”，将信息下载到可以在本地使用的 .csv 文件中。

    ![审核日志](./media/active-directory-tou/audit-logs-tou.png)

    如果单击某项日志，窗格中会显示更多活动详细信息。

    ![活动详细信息](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>使用条款呈现给用户的外观
创建并强制执行使用条款后，处于使用条款范围内的用户会在登录过程中看到以下屏幕。

![用户 Web 登录](./media/active-directory-tou/user-tou.png)

用户可以查看的使用条款和，如有必要，使用按钮放大和缩小。

![使用缩放按钮的查看使用条款](./media/active-directory-tou/zoom-buttons.png)

以下屏幕显示了使用条款在移动设备上的外观。

![用户移动登录](./media/active-directory-tou/mobile-tou.png)

用户只需接受一次的使用条款，他们将看不使用条款再次在后续登录。

### <a name="how-users-can-review-their-terms-of-use"></a>用户如何查看其使用条款
用户可按以下过程查看已接受的使用条款。

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上角单击自己的姓名，然后选择“个人资料”。

    ![配置文件](./media/active-directory-tou/tou14.png)

1. 在“个人资料”页中单击“查看使用条款”。

    ![个人资料 - 查看使用条款](./media/active-directory-tou/tou13a.png)

1. 可以在其中查看已接受的使用条款。

## <a name="edit-terms-of-use-details"></a>编辑使用条款详细信息
可以编辑使用条款的某些详细信息，但不能修改现有文档。 以下过程介绍如何编辑详细信息。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 选择要编辑的使用条款。

1. 单击“编辑条款”。

1. 在“编辑使用条款”窗格中，更改名称、显示名称，或要求用户展开值。

    若要更改其他设置（例如，PDF 文档、要求用户在每台设备上同意使用条款、使同意状态过期、重新接受使用条款之前的持续时间，或条件访问策略），必须创建新的使用条款。

    ![编辑使用条款](./media/active-directory-tou/edit-tou.png)

1. 单击“保存”以保存更改。

    保存更改后，用户必须重新接受这些编辑内容。

## <a name="add-a-terms-of-use-language"></a>添加使用条款语言
以下过程介绍如何添加使用条款语言。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 选择要编辑的使用条款。

1. 在详细信息窗格中，单击“语言”选项卡。

    ![添加 TOU](./media/active-directory-tou/languages-tou.png)

1. 单击“添加语言”。

1. 在“添加使用条款语言”窗格中，上传已本地化的 PDF 并选择语言。

    ![添加 TOU](./media/active-directory-tou/language-add-tou.png)

1. 单击“添加”以添加该语言。

## <a name="per-device-terms-of-use"></a>按设备实施的使用条款

使用“要求用户在每台设备上同意”设置可以要求最终用户在每台访问设备上接受使用条款。 最终用户必须将其设备加入 Azure AD。 加入设备后，使用设备 ID 在每台设备上强制实施使用条款。

下面是支持的平台和软件列表。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **本机应用** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome（带扩展）** | 是 | 是 | 是 |  |

按设备实施的使用条款具有以下约束：

- 一个设备只能加入到一个租户。
- 用户必须有权加入其设备。
- 不支持 Intune 注册应用。

如果用户的设备未加入，他们将收到一条消息，指出需要加入其设备。 他们的体验取决于平台和软件。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 设备

如果用户使用 Windows 10 和 Microsoft Edge，将会收到如下所示的，指出需要[加入其设备](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)的消息。

![Windows 10 和 Microsoft Edge - 加入设备提示](./media/active-directory-tou/per-device-win10-edge.png)

如果用户使用 Chrome，系统会提示他们安装 [Windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

### <a name="browsers"></a>浏览器

如果用户使用不受支持的浏览器，系统会要求他们使用其他浏览器。

![不支持的浏览器](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>删除使用条款
可以通过以下过程删除旧的使用条款。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 选择要删除的使用条款。

1. 单击“删除条款”。

1. 在出现的询问是否需要继续的消息中，单击“是”。

    ![删除使用条款](./media/active-directory-tou/delete-tou.png)

    将再也看不到你的使用条款。

## <a name="deleted-users-and-active-terms-of-use"></a>删除的用户和活动的使用条款
默认情况下，删除的用户会在 Azure AD 中保持“已删除”状态 30 天，在此期间，管理员可以根据需要还原这些用户。 30 天后，该用户将被永久删除。 此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。 某个用户被永久删除后，有关该用户的数据随后会从活动使用条款中删除。 有关已删除用户的审核信息仍保留在审核日志中。

## <a name="policy-changes"></a>策略更改
条件访问策略会立即生效。 发生此情况时，管理员就会看到“表示遗憾的阴云”或“Azure AD 令牌问题”。 管理员必须注销然后重新登录才能符合新策略的要求。

>[!IMPORTANT]
> 在以下情况下，处于范围的用户必须在注销后登录才能符合新策略的要求：
> - 在使用条款上启用了条件访问策略
> - 或创建了第二个使用条款

## <a name="b2b-guests-preview"></a>B2B 来宾（预览版）

大多数组织都在实施相应的流程，让其员工同意组织的使用条款和隐私声明。 但是，对于通过 SharePoint 或 Teams 添加的 Azure AD 企业到企业 (B2B) 来宾，如何强制要求他们同样表示同意？ 使用条件访问和使用条款，可以直接对 B2B 来宾用户强制实施策略。 在邀请兑换流程中，用户会看到使用条款。 这种支持目前处于预览状态。

仅当用户在 Azure AD 中具有来宾帐户时，才显示使用条款。 SharePoint Online 当前具有[即席外部共享收件人体验](/sharepoint/what-s-new-in-sharing-in-targeted-release)共享文档或不需要用户具有来宾帐户的文件夹。 在这种情况下，不会显示使用条款。

![所有来宾用户](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>云应用支持（预览版）

可对不同的云应用（例如 Azure 信息保护和 Microsoft Intune）使用使用条款。 这种支持目前处于预览状态。

### <a name="azure-information-protection"></a>Azure 信息保护

可以针对 Azure 信息保护应用配置条件访问策略，并要求用户在访问受保护的文档时接受使用条款。 这会在用户首次访问受保护的文档之前触发使用条款。

![Azure 信息保护云应用](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 注册

可以针对 Microsoft Intune 注册应用配置条件访问策略，并要求在 Intune 中注册设备之前接受使用条款。 有关详细信息，请阅读[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

![Microsoft Intune 云应用](./media/active-directory-tou/cloud-app-intune.png)

> [!NOTE]
> [按设备实施的使用条款](#per-device-terms-of-use)不支持 Intune 注册应用。

## <a name="frequently-asked-questions"></a>常见问题

**问：如何查看用户何时/是否接受了使用条款？**<br />
答：在“使用条款”边栏选项卡中单击“已接受”下的数字。 也可在 Azure AD 审核日志中查看或搜索接受活动。 有关详细信息，请参阅“查看接受用户和拒绝用户的报告”以及[查看 Azure AD 审核日志](#view-azure-ad-audit-logs)。

**问：信息的存储时间为多长？**<br />
答：使用条款报告中的用户计数以及接受用户/拒绝用户的存储时间为使用条款有效期。 Azure AD 审核日志的存储时间为 30 天。

**问：为什么我在使用条款报告与 Azure AD 审核日志中看到的同意数量不同？**<br />
答：使用条款报告的存储时间为该使用条款的有效期，而 Azure AD 审核日志的存储时间为 30 天。 此外，使用条款报告仅显示用户当前的同意状态。 例如，如果用户拒绝然后接受，则使用条款报告仅显示该用户的接受。 如果需要查看历史记录，可以使用 Azure AD 审核日志。

**问：如果我编辑了使用条款的详细信息，用户是否必须重新接受？**<br />
答：不是。如果管理员编辑了使用条款的详细信息（名称、显示名称、要求用户展开，或添加语言），用户不需要重新接受新条款。

**问：是否可以更新现有的使用条款文档？**<br />
答：目前无法更新现有的使用条款文档。 若要更改使用条款文档，必须创建新的使用条款实例。

**问：如果使用条款 PDF 文档中有超链接，最终用户能否单击这些超链接？**<br />
答：该 PDF 默认呈现为 JPEG，因此无法单击超链接。 用户可以选择“查看时遇到问题? 请单击此处”**，以本机方式呈现该 PDF（支持超链接）**。

**问：使用条款是否支持多种语言？**<br />
答：是的。 目前，管理员可以为单个使用条款配置 108 种不同的语言。 管理员可以上传多个 PDF 文档，并使用相应的语言（最多 108 种）标记这些文档。 当最终用户登录时，我们会查看其浏览器语言首选项，并显示匹配的文档。 如果没有匹配项，我们将显示默认文档，即上传的第一个文档。

**问：什么时候会触发使用条款？**<br />
答：在登录体验期间触发使用条款。

**问：可将哪些应用程序作为使用条款的目标？**<br />
答：可以使用新式验证对企业应用程序创建条件访问策略。 有关详细信息，请参阅[企业应用程序](./../manage-apps/view-applications-portal.md)。

**问：是否可以向给定用户或应用添加多个使用条款？**<br />
答：可以，方法是创建多个以这些组或应用程序为目标的条件访问策略。 如果用户处于多个使用条款的范围内，他们可一次接受一个使用条款。

**问：用户拒绝使用条款后会发生什么？**<br />
答：将阻止此用户访问该应用程序。 用户需要重新登录并接受条款才能获取访问权限。

**问：是否可以取消接受以前接受的使用条款？**<br />
答：可以[查看以前接受的使用条款](#how-users-can-review-their-terms-of-use)，但目前没有办法取消接受。

**问：如果我还使用 Intune 条款和条件，会发生什么情况？**<br />
答：如果你已配置 Azure AD 使用条款和 [Intune 条款和条件](/intune/terms-and-conditions-create)，则用户必须同时接受这两者。 有关详细信息，请参阅[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

## <a name="next-steps"></a>后续步骤

- [快速入门：在访问云应用之前要求接受使用条款](../conditional-access/require-tou.md)
- [Azure Active Directory 中条件访问的最佳做法](../conditional-access/best-practices.md)
