---
title: 使用-Azure Active Directory 使用条款 |Microsoft Docs
description: 开始使用 Azure Active Directory 使用条款若要获取的访问权限之前向员工或客户提供信息。
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
ms.openlocfilehash: 1abae0a454e17e8f633f68bc5853bfb4a4b24d14
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383181"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 使用条款

Azure AD 使用条款提供了一个简单的方法，组织可以通过向最终用户显示信息。 可以通过这样的呈现方式确保用户看到法律要求或符合性要求的相关免责声明。 本文介绍如何开始使用的使用条款。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>概述视频

下面的视频提供了使用条款的快速概述。

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

有关其他视频，请参阅：
- [如何部署 Azure Active Directory 中的使用条款](https://www.youtube.com/embed/N4vgqHO2tgY)
- [如何部署 Azure Active Directory 中的使用条款](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>我如何与使用条款？

Azure AD 使用条款使用具有以下功能：

- 要求员工或客户接受你的使用条款的之前获取的访问权限。
- 要求员工或客户接受你的使用条款获得访问权限之前每个设备上。
- 要求员工或客户接受你的使用条款的按重复计划。
- 要求员工或客户接受你的使用条款的之前注册 Azure 多重身份验证 (MFA) 中的安全信息。
- 要求员工在接受使用条款之前在 Azure AD 自助服务密码重置 (SSPR) 注册安全信息。
- 显示组织中的所有用户的常规使用条款。
- 提供基于用户属性 （例如的具体使用条款。 [动态组](../users-groups-roles/groups-dynamic-membership.md)中的医生与护士或国内员工和国际员工）的具体使用条款。
- 当访问高业务影响的应用程序，例如 Salesforce 时所显示具体的使用条款。
- 以不同语言显示的使用条款。
- 具有或没有接受到你的使用条款的用户的列表。
- 有助于符合隐私法规。
- 显示条款使用活动的相容性和审核的日志。
- 创建和管理使用使用条款[Microsoft Graph Api](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) （目前处于预览状态）。

## <a name="prerequisites"></a>必备组件

若要使用和配置 Azure AD 使用条款，必须具有：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 订阅。
   - 如果没有这其中的某个订阅，可以[获取 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) 或[启用 Azure AD Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下述适用于需配置目录的管理员帐户之一：
   - 全局管理员角色
   - 安全管理员
   - 条件访问管理员

## <a name="terms-of-use-document"></a>使用条款文档

Azure AD 使用条款使用 PDF 格式呈现内容。 此 PDF 文件可以是任意内容（例如现有的合同文档），只要能够在用户登录时收集最终用户协议即可。 若要支持移动设备上的用户，PDF 中建议的字号是 24 点。

## <a name="add-terms-of-use"></a>添加使用条款

完成你的使用条款的使用文档后，使用以下过程添加它。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 Azure。
1. 在 [https://aka.ms/catou](https://aka.ms/catou) 导航到“使用条款”。 

   ![“使用条款”边栏选项卡](./media/terms-of-use/tou-blade.png)

1. 单击“新建条款”。 

   ![添加 TOU](./media/terms-of-use/new-tou.png)

1. 在中**名称**框中，在 Azure 门户中输入将使用使用条款的名称。
1. 在“显示名称”框中，输入用户登录时看到的标题。 
1. 有关**使用条款文档**，浏览到已完成的使用条款使用 PDF 并选择它。
1. 选择用于你的使用条款的使用文档的语言。 可以通过语言选项上传多个版本的使用条款，每个版本的语言各不相同。 最终用户看到的使用条款版本取决于其浏览器首选项。
1. 如果需要最终用户能够查看之前接受的使用条款，请设置**要求用户展开使用条款**到**上**。
1. 若要要求最终用户接受你的使用条款的从访问每个设备上，设置**要求用户在每个设备上的同意**到**上**。 有关详细信息，请参阅[的每个设备使用条款](#per-device-terms-of-use)。
1. 如果你想要过期的按计划使用同意条款，设置**过期的许可：** 到**上**。 设置为“打开”时，会显示另外两项计划设置。

   ![使同意状态过期](./media/terms-of-use/expire-consents.png)

1. 使用**上启动的过期**并**频率**设置以指定的计划的条款，请使用过期时间。 下表显示了几项示例设置的结果：

   | 过期开始日期 | 频率 | 结果 |
   | --- | --- | --- |
   | 今天的日期  | 每月 | 从今天开始，用户必须接受的使用条款，然后接受每个月。 |
   | 将来的日期  | 每月 | 从今天开始，用户必须接受的使用条款。 到达指定的将来日期时，同意状态将会过期，以后用户必须每个月接受使用条款。  |

   例如，如果将过期开始日期设置为“1 月 1 日”，将频率设置为“每月”，则两个用户的过期计划如下：  

   | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 1 日 | 3 月 1 日 | 4 月 1 日 |

1. 使用**持续时间之前重新接受要求 （天）** 设置以指定的天数前用户必须接受的使用条款。 这可以让用户遵照自己的计划。 例如，如果将持续时间设置为 **30** 天，则两个用户的计划如下：

   | 用户 | 第一个接受日期 | 第一个过期日期 | 第二个过期日期 | 第三个过期日期 |
   | --- | --- | --- | --- | --- |
   | Alice | 1 月 1 日 | 1 月 31 日 | 3 月 2 日 | 4 月 1 日 |
   | Bob | 1 月 15 日 | 2 月 14 日 | 3 月 16 日 | 4 月 15 日 |

   可以结合使用“使同意状态过期”和“需要重新接受使用条款之前的持续时间(天)”设置，但一般只使用其中的一项。  

1. 下**条件访问**，使用**强制实施条件性访问策略模板与**列表可以选择模板以强制实施的使用条款。

   ![条件访问模板](./media/terms-of-use/conditional-access-templates.png)

   | 模板 | 描述 |
   | --- | --- |
   | **所有来宾对云应用的访问权限** | 将会针对所有来宾和所有云应用创建一个条件访问策略。 此策略会影响 Azure 门户。 创建后，可能需要注销再登录。 |
   | **所有用户对云应用的访问权限** | 将会针对所有用户和所有云应用创建条件访问策略。 此策略会影响 Azure 门户。 创建后，需要注销再登录。 |
   | **自定义策略** | 选择用户、 组和此使用条款将应用于的应用。 |
   | **稍后创建条件访问策略** | 此使用条款将显示在创建条件访问策略时弹出的授予控制权列表中。 |

   >[!IMPORTANT]
   >条件访问策略控制（包括使用条款）不支持对服务帐户强制实施。 我们建议从条件访问策略中排除所有服务帐户。

    自定义的条件性访问策略启用，到特定的云应用程序或用户组的具体的使用条款。 有关详细信息，请参阅[快速入门：在访问云应用之前要求接受使用条款](require-tou.md)。

1. 单击**创建**。

   选择自定义条件访问模板后，会显示一个新的屏幕用于创建自定义的条件访问策略。

   ![自定义策略](./media/terms-of-use/custom-policy.png)

   现在应看到新使用条款。

   ![添加 TOU](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>查看接受用户和拒绝用户的报告

“使用条款”边栏选项卡会显示接受用户和拒绝用户的计数。 这些计数和人员接受/拒绝了使用条款的生存期内存储。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。 

   ![“使用条款”边栏选项卡](./media/terms-of-use/view-tou.png)

1. 有关使用的术语，请单击下的数字**已接受**或**已拒绝**若要查看用户的当前状态。

   ![使用条款的同意](./media/terms-of-use/accepted-tou.png)

1. 若要查看单个用户的历史记录，请依次单击省略号 ( **...** )、“查看历史记录”。 

   ![“查看历史记录”菜单](./media/terms-of-use/view-history-menu.png)

   在“查看历史记录”窗格中，可以看到所有接受、拒绝和过期操作状态的历史记录。

   ![“查看历史记录”窗格](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>查看 Azure AD 审核日志

如果你想要查看其他活动，Azure AD 使用条款包括审核日志。 每次用户许可都会在存储时间为 **30 天**的审核日志中触发一个事件。 可以在门户中查看这些日志，或者将其下载为 .csv 文件。

若要开始使用 Azure AD 审核日志，请执行以下过程：

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。 
1. 选择的使用条款。
1. 单击“查看审核日志”。 

   ![“使用条款”边栏选项卡](./media/terms-of-use/audit-tou.png)

1. 在 Azure AD 审核日志屏幕上，可以使用提供的列表筛选信息，将特定审核日志信息作为目标。

   还可以单击“下载”，将信息下载到可以在本地使用的 .csv 文件中。 

   ![审核日志](./media/terms-of-use/audit-logs-tou.png)

   如果单击某项日志，窗格中会显示更多活动详细信息。

   ![活动详细信息](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>哪些使用条款的用户如下所示

一旦创建并强制实施的使用条款，处于范围内的用户将在登录期间看到以下屏幕。

![用户 Web 登录](./media/terms-of-use/user-tou.png)

用户可以查看的使用条款和，如有必要，使用按钮放大和缩小。

![查看使用条款的缩放按钮](./media/terms-of-use/zoom-buttons.png)

以下屏幕显示的使用条款的移动设备上的外观。

![用户移动登录](./media/terms-of-use/mobile-tou.png)

用户只需接受一次的使用条款，他们将看不使用条款再次在后续登录。

### <a name="how-users-can-review-their-terms-of-use"></a>用户如何查看其使用条款

用户可以查看并了解他们已使用以下过程接受使用条款。

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。
1. 在右上角单击自己的姓名，然后选择“个人资料”。 

   ![配置文件](./media/terms-of-use/tou14.png)

1. 在“个人资料”页中单击“查看使用条款”。 

   ![个人资料 - 查看使用条款](./media/terms-of-use/tou13a.png)

1. 可以在其中查看已接受的使用条款。

## <a name="edit-terms-of-use-details"></a>编辑使用条款详细信息

可以编辑的使用条款，一些详细信息，但不能修改现有文档。 以下过程介绍如何编辑详细信息。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。 
1. 选择你想要编辑的使用条款。
1. 单击“编辑条款”。 
1. 在“编辑使用条款”窗格中，更改名称、显示名称，或要求用户展开值。

   如果你想要更改，如 PDF 文档的其他设置要求用户在每个设备上的同意，则失效同意、 持续时间之前 reacceptance 或条件性访问策略，您必须创建的新使用条款。

   ![编辑使用条款](./media/terms-of-use/edit-tou.png)

1. 单击“保存”  以保存更改。

   保存更改后，用户必须重新接受这些编辑内容。

## <a name="add-a-terms-of-use-language"></a>添加使用语言的条款

以下过程介绍如何添加使用语言的条款。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。 
1. 选择你想要编辑的使用条款。
1. 在详细信息窗格中，单击“语言”选项卡。 

   ![添加 TOU](./media/terms-of-use/languages-tou.png)

1. 单击“添加语言”。 
1. 在“添加使用条款语言”窗格中，上传已本地化的 PDF 并选择语言。

   ![添加 TOU](./media/terms-of-use/language-add-tou.png)

1. 单击“添加”以添加该语言。 

## <a name="per-device-terms-of-use"></a>每个设备的使用条款

**要求用户在每个设备上的同意**设置，可要求最终用户接受你的使用条款的从访问每个设备上。 最终用户必须将其设备加入 Azure AD。 当加入设备后时，设备 ID 用于强制在每个设备上使用的条款。

下面是支持的平台和软件列表。

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 其他 |
> | --- | --- | --- | --- | --- |
> | **本机应用** | 是 | 是 | 是 |  |
> | **Microsoft Edge** | 是 | 是 | 是 |  |
> | **Internet Explorer** | 是 | 是 | 是 |  |
> | **Chrome（带扩展）** | 是 | 是 | 是 |  |

每个设备使用条款具有以下限制：

- 一个设备只能加入到一个租户。
- 用户必须有权加入其设备。
- 不支持 Intune 注册应用。
- 不支持 azure AD B2B 用户。

如果用户的设备未加入，他们将收到一条消息，指出需要加入其设备。 他们的体验取决于平台和软件。

### <a name="join-a-windows-10-device"></a>加入 Windows 10 设备

如果用户使用 Windows 10 和 Microsoft Edge，将会收到如下所示的，指出需要[加入其设备](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)的消息。

![Windows 10 和 Microsoft Edge - 加入设备提示](./media/terms-of-use/per-device-win10-edge.png)

如果用户使用 Chrome，系统会提示他们安装 [Windows 10 帐户扩展](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

### <a name="browsers"></a>浏览器

如果用户使用不受支持的浏览器，系统会要求他们使用其他浏览器。

![不支持的浏览器](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>删除使用条款

您可以删除旧使用条款使用以下过程。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。 
1. 选择你想要删除的使用条款。
1. 单击“删除条款”。 
1. 在出现的询问是否需要继续的消息中，单击“是”。 

   ![删除使用条款](./media/terms-of-use/delete-tou.png)

   您应该不会再看到你的使用条款。

## <a name="deleted-users-and-active-terms-of-use"></a>已删除的用户和 active 使用条款

默认情况下，删除的用户会在 Azure AD 中保持“已删除”状态 30 天，在此期间，管理员可以根据需要还原这些用户。 30 天后，该用户将被永久删除。 此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](../fundamentals/active-directory-users-restore.md)。 一个用户已被永久删除，将从 active 使用条款中删除有关该用户的后续数据。 有关已删除用户的审核信息仍保留在审核日志中。

## <a name="policy-changes"></a>策略更改

条件访问策略会立即生效。 发生此情况时，管理员就会看到“表示遗憾的阴云”或“Azure AD 令牌问题”。 管理员必须注销然后重新登录才能符合新策略的要求。

> [!IMPORTANT]
> 在以下情况下，处于范围的用户必须在注销后登录才能符合新策略的要求：
>
> - 在使用条款上启用了条件访问策略
> - 或创建了第二个使用条款

## <a name="b2b-guests-preview"></a>B2B 来宾（预览版）

大多数组织有员工同意其组织的使用条款和隐私声明的位置中的过程。 但是，对于通过 SharePoint 或 Teams 添加的 Azure AD 企业到企业 (B2B) 来宾，如何强制要求他们同样表示同意？ 使用条件性访问和使用的条款，您可以强制执行策略直接向 B2B 来宾用户。 在邀请兑换流程，则用户会看到使用的条款。 这种支持目前处于预览状态。

仅当用户在 Azure AD 中具有来宾帐户时，才显示使用条款。 SharePoint Online 当前具有[即席外部共享收件人体验](/sharepoint/what-s-new-in-sharing-in-targeted-release)共享文档或不需要用户具有来宾帐户的文件夹。 在这种情况下，不显示的使用条款。

![所有来宾用户](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>云应用支持（预览版）

可对不同的云应用（例如 Azure 信息保护和 Microsoft Intune）使用使用条款。 这种支持目前处于预览状态。

### <a name="azure-information-protection"></a>Azure 信息保护

你可以配置 Azure 信息保护应用的条件性访问策略，并且用户访问受保护的文档时需要的使用条款。 这会触发使用条款之前对用户首次访问受保护的文档。

![Azure 信息保护云应用](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 注册

你可以配置 Microsoft Intune 注册应用的条件性访问策略，并且需要在 Intune 中设备的注册之前的使用条款。 有关详细信息，请阅读[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

![Microsoft Intune 云应用](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> 不支持 Intune 注册应用[的每个设备使用条款](#per-device-terms-of-use)。

## <a name="frequently-asked-questions"></a>常见问题

**问：如何查看在 / 用户已接受的使用条款？**<br />
答：在“使用条款”边栏选项卡中单击“已接受”下的数字  。 也可在 Azure AD 审核日志中查看或搜索接受活动。 有关详细信息，请参阅“查看接受用户和拒绝用户的报告”以及[查看 Azure AD 审核日志](#view-azure-ad-audit-logs)。

**问：信息的存储时间为多长？**<br />
答：用户计数中使用的报告以及接受/拒绝了使用条款的生存期内存储的人员的术语。 Azure AD 审核日志的存储时间为 30 天。

**问：为什么看不同数量的许可条款使用与 Azure AD 的报告中： 审核日志？**<br />
答：使用报表中的条款是该使用条款，时 Azure AD 审核日志存储 30 天的生存期内存储中。 此外，使用报表中的条款仅显示用户的当前许可状态。 例如，如果用户拒绝，然后接受使用条款使用报表将仅显示该用户的接受。 如果需要查看历史记录，可以使用 Azure AD 审核日志。

**问：如果我编辑使用条款详细信息，不要求用户重新接受？**<br />
答：否，如果管理员使用的术语的编辑详细信息 （名称、 显示名称、 要求用户展开，或添加一种语言），它不需要用户重新接受新的条款。

**问：可以更新现有使用条款文档？**<br />
答：目前，无法更新现有使用条款文档。 若要更改使用条款文档，您将必须创建使用实例的新使用条款。

**问：如果使用 PDF 文档的条款中的超链接，将最终用户能够单击它们？**<br />
答：该 PDF 默认呈现为 JPEG，因此无法单击超链接。 用户可以选择“查看时遇到问题? 请单击此处” **，以本机方式呈现该 PDF（支持超链接）** 。

**问：可以使用使用条款支持多种语言？**<br />
答：是的。 目前有 108 不同语言，管理员可以配置为单个使用条款。 管理员可以上传多个 PDF 文档，并使用相应的语言（最多 108 种）标记这些文档。 当最终用户登录时，我们会查看其浏览器语言首选项，并显示匹配的文档。 如果没有匹配项，我们将显示默认文档，即上传的第一个文档。

**问：何时触发使用条款？**<br />
答：在登录体验期间触发使用条款。

**问：哪些应用程序我可以目标到使用条款？**<br />
答：可以使用新式验证对企业应用程序创建条件访问策略。 有关详细信息，请参阅[企业应用程序](./../manage-apps/view-applications-portal.md)。

**问：可以向给定的用户或应用程序添加多个使用条款？**<br />
答：可以，方法是创建多个以这些组或应用程序为目标的条件访问策略。 如果用户属于多个使用条款的作用域中，它们接受一次一个的使用条款。

**问：如果用户拒绝使用条款，会发生什么情况？**<br />
答：将阻止此用户访问该应用程序。 用户需要重新登录并接受条款才能获取访问权限。

**问：是否可以 unaccept 以前接受的使用条款？**<br />
答：你可以[查看以前接受的使用条款](#how-users-can-review-their-terms-of-use)，但目前没有办法 unaccept。

**问：如果我还使用 Intune 条款和条件，会发生什么情况？**<br />
答：如果已配置这两个 Azure AD 使用条款和[Intune 条款和条件](/intune/terms-and-conditions-create)，用户将需要接受这两项。 有关详细信息，请参阅[为组织博客文章选择合适的条款解决方案](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)。

**问：使用服务条款使用了哪些终结点进行身份验证？**<br />
答：使用条款利用以下终结点进行身份验证： https://tokenprovider.termsofuse.identitygovernance.azure.com和 https://account.activedirectory.windowsazure.com。 如果你的组织已注册的 Url 的允许列表，您需要添加到允许列表，以及 Azure AD 终结点的这些终结点登录。

## <a name="next-steps"></a>后续步骤

- [快速入门：在访问云应用之前要求接受使用条款](require-tou.md)
- [Azure Active Directory 中条件访问的最佳做法](best-practices.md)
