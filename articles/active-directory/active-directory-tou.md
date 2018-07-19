---
title: Azure Active Directory 使用条款 | Microsoft Docs
description: 通过借鉴 Azure AD 使用条款，你和你的公司可以向 Azure AD 服务用户提供使用条款。
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
ms.component: compliance-reports
ms.date: 06/29/2018
ms.author: rolyon
ms.openlocfilehash: 7833c9da2303d119f0cb421f21bea455ab449898
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856410"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Azure Active Directory 使用条款功能
组织可以通过 Azure AD 使用条款这种简单的方法向最终用户显示信息。 可以通过这样的呈现方式确保用户看到法律要求或符合性要求的相关免责声明。 本文介绍如何开始使用 Azure AD 使用条款。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-can-i-do-with-terms-of-use"></a>使用条款可以用来做什么？
可以通过 Azure AD 使用条款来完成以下事项：
- 要求员工或来宾在获得访问权限之前先同意使用条款。
- 显示针对组织中所有用户的常规使用条款。
- 显示基于用户属性（例如， [动态组](users-groups-roles/groups-dynamic-membership.md)中的医生与护士或国内员工和国际员工）的具体使用条款。
- 在用户访问业务影响大的应用程序（例如 Salesforce）时显示特定使用条款。
- 以不同的语言显示使用条款。
- 列出已同意或尚未同意使用条款的人员。
- 显示使用条款活动的审核日志。

## <a name="prerequisites"></a>先决条件
若要使用和配置 Azure AD 使用条款，必须具备以下先决条件：

- Azure AD Premium P1、P2、EMS E3 或 EMS E5 订阅。
    - 如果没有这其中的某个订阅，可以[获取 Azure AD Premium](fundamentals/active-directory-get-started-premium.md) 或[启用 Azure AD Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。
- 下述适用于需配置目录的管理员帐户之一：
    - 全局管理员
    - 安全管理员
    - 条件访问管理员

## <a name="terms-of-use-document"></a>使用条款文档

Azure AD 使用条款使用 PDF 格式显示内容。 此 PDF 文件可以是任意内容（例如现有的合同文档），只要能够在用户登录时收集最终用户协议即可。 在 PDF 中，建议的字号是 24。

## <a name="add-terms-of-use"></a>添加使用条款
完成使用条款文档后，请使用以下过程添加它。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录 Azure。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 导航到“使用条款”。

    ![“使用条款”边栏选项卡](media/active-directory-tou/tou-blade.png)

1. 单击“新建条款”。

    ![添加 TOU](media/active-directory-tou/new-tou.png)

1. 输入使用条款的**名称**

2. 输入**显示名称**。  这是用户登录时看到的标题。

3. **浏览**到已完成的使用条款 (PDF)，将其选中。

4. **选择**使用条款的语言。  可以通过语言选项上传多个版本的使用条款，每个版本的语言各不相同。  最终用户看到的使用条款版本取决于其浏览器首选项。

5. 对于“要求用户展开使用条款”这一功能，请选择“启用”或“禁用”。  如果此设置设为“启用”，则最终用户在接受使用条款之前必须先进行查看。

6. 可以在“条件访问”下从下拉列表中选择模板，或者选择自定义条件访问策略，以便**强制执行**已上传的使用条款。  可以使用自定义条件访问策略将使用条款细化，向下细化到特定云应用程序或用户组。  有关详细信息，请参阅[配置条件访问策略](active-directory-conditional-access-best-practices.md)。

    >[!IMPORTANT]
    >条件访问策略控制（包括使用条款）不支持对服务帐户强制实施。  我们建议从条件访问策略中排除所有服务帐户。

7. 单击“创建”。

8. 选择自定义条件访问模板后，会出现新的屏幕，用于自定义条件访问策略。

    现在会看到新的使用条款。

    ![添加 TOU](media/active-directory-tou/create-tou.png)

## <a name="view-who-has-accepted-and-declined"></a>查看接受的和拒绝的用户
“使用条款”边栏选项卡会显示接受用户和拒绝用户的计数。 在使用条款有效期内，会存储这些计数以及接受用户/拒绝用户。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

    ![审核事件](media/active-directory-tou/view-tou.png)

1. 单击“已接受”或“已拒绝”下的数字，以便查看用户的当前状态。

    ![审核事件](media/active-directory-tou/accepted-tou.png)

## <a name="view-audit-logs"></a>查看审核日志
Azure AD 使用条款包括审核日志，方便你查看其他活动。 每次用户许可都会在存储时间为 30 天的审核日志中触发一个事件。 可以在门户中查看这些日志，或者将其下载为 .csv 文件。

若要开始使用审核日志，请执行以下过程：

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 单击“查看审核日志”。

    ![审核事件](media/active-directory-tou/audit-tou.png)

1. 在 Azure AD 审核日志屏幕上，可以使用提供的下拉列表筛选信息，将特定审核日志信息作为目标。

    ![审核事件](media/active-directory-tou/audit-logs-tou.png)

1. 还可以单击“下载”，将信息下载到可以在本地使用的 .csv 文件中。

## <a name="what-terms-of-use-looks-like-for-users"></a>使用条款呈现给用户的外观
创建并强制执行使用条款后，处于使用条款范围内的用户会在登录过程中看到以下屏幕。

![审核事件](media/active-directory-tou/user-tou.png)

以下屏幕显示了使用条款在移动设备上的外观。

![审核事件](media/active-directory-tou/mobile-tou.png)

### <a name="how-users-can-review-their-terms-of-use"></a>用户如何查看其使用条款
用户可按以下过程查看已接受的使用条款。

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

1. 在右上角单击自己的姓名，然后从下拉列表中选择“个人资料”。

    ![配置文件](media/active-directory-tou/tou14.png)

1. 在“个人资料”页中单击“查看使用条款”。

    ![审核事件](media/active-directory-tou/tou13a.png)

1. 可以在其中查看已接受的使用条款。 

## <a name="delete-terms-of-use"></a>删除使用条款
可以通过以下过程删除旧的使用条款。

1. 在 [https://aka.ms/catou](https://aka.ms/catou) 登录到 Azure 并导航到“使用条款”。

1. 选择要删除的使用条款。

1. 单击“删除条款”。

1. 在出现的询问是否需要继续的消息中，单击“是”。

    ![添加 TOU](media/active-directory-tou/delete-tou.png)

    将再也看不到你的使用条款。

## <a name="deleted-users-and-active-terms-of-use"></a>删除的用户和活动的使用条款
默认情况下，删除的用户会在 Azure AD 中保持“已删除”状态 30 天，在此期间，管理员可以根据需要还原这些用户。  30 天后，该用户将被永久删除。  此外，使用 Azure Active Directory 门户，全局管理员可以在达到该时间段之前，显式地[永久删除最近删除的用户](fundamentals/active-directory-users-restore.md)。  某个用户被永久删除后，有关该用户的数据随后会从活动使用条款中删除。  有关已删除用户的审核信息仍保留在审核日志中。

## <a name="policy-changes"></a>策略更改
条件访问策略会立即生效。 发生此情况时，管理员就会看到“表示遗憾的阴云”或“Azure AD 令牌问题”。 管理员必须注销然后重新登录才能符合新策略的要求。

>[!IMPORTANT]
> 在以下情况下，处于范围的用户必须在注销后登录才能符合新策略的要求：
> - 在使用条款上启用了条件访问策略
> - 或创建了第二个使用条款

## <a name="frequently-asked-questions"></a>常见问题

**问：如何查看用户何时/是否接受了使用条款？**</br>
答：在“使用条款”边栏选项卡中单击“已接受”下的数字。 还可在审核日志中查看或搜索接受活动。 有关详细信息，请参阅[查看接受的和拒绝的用户](#view-who-has-accepted-and-declined)以及[查看审核日志](#view-audit-logs)。

**问：更改使用条款的条款后，是否需要用户重新接受该条款？**</br>
答：是的，管理员可以更改使用条款的条款，这需要用户重新接受新的条款。

**问：使用条款是否支持多种语言？**</br>
答：是的。  目前，管理员可以为单个使用条款配置 18 种不同的语言。 

**问：什么时候会触发使用条款？**</br>
答：在登录体验期间触发使用条款。

**问：我可以将哪些应用程序作为使用条款的目标？**</br>
答：可以使用新式验证对企业应用程序创建条件访问策略。  有关详细信息，请参阅[企业应用程序](./manage-apps/view-applications-portal.md)。

**问：是否可以向给定用户或应用添加多个使用条款？**</br>
答：可以，方法是创建多个以这些组或应用程序为目标的条件访问策略。 如果用户处于多个使用条款的范围内，他们可一次同意一个使用条款。
 
**问：用户拒绝使用条款后会发生什么？**</br>
答：将阻止此用户访问该应用程序。 用户需要重新登录并同意条款才能获得访问权限。
 
**问：是否可以取消接受以前接受的使用条款？**</br>
答：可以[查看以前接受的使用条款](#how-users-can-review-their-terms-of-use)，但目前没有办法取消接受。
 
**问：信息的存储时间为多长？**</br>
答：在使用条款有效期内，会存储用户计数以及接受用户/拒绝用户。 审核日志存储 30 天。
