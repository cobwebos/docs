---
title: Azure Active Directory 中的领英集成数据共享和许可 | Microsoft Docs
description: 介绍领英集成如何通过 Azure Active Directory 中的 Microsoft 应用共享数据
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/22/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 776c793fb797e9388f0852773315f27cd42dde25
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54471712"
---
# <a name="linkedin-integration-data-sharing-and-consent"></a>领英集成数据共享和许可

作为 Azure Active Directory (Azure AD) 管理员，可以允许组织中的用户同意将其 Microsoft 工作或学校帐户与领英帐户相连接。 当用户连接其帐户时，来自领英的信息和热点可以在某些 Microsoft 应用和服务中使用。 用户还可以期望通过来自 Microsoft 的信息改进和丰富领英上的网络体验。

若要在 Microsoft 应用和服务中查看领英信息，用户必须同意将自己的 Microsoft 帐户和领英帐户进行连接。 当用户首次在 Outlook、OneDrive 或 SharePoint Online 中的个人资料卡片上单击查看某人的领英信息时，系统会提示他们连接其帐户。 在用户同意体验并连接其帐户之前，不会为他们完整启用领英帐户连接。

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>共享领英信息的优点

在 Microsoft 应用和服务中访问领英信息可以使用户更方便地与组织内部和外部的同事、客户和合作伙伴进行联系、接洽和建立职业关系。 新用户可以与同事进行联系、详细了解他们并轻松地访问更多信息，从而更快掌握情况。 下面是有关领英信息如何出现在 Microsoft 应用中的个人资料卡片上的示例：

![启用领英集成](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>启用并公布领英集成

必须是 Azure Active Directory 管理员才能管理组织的设置。 可以为所有用户或一组特定用户启用它。

1. 若要启用或禁用集成，请按照[领英集成](linkedin-integration.md)中的步骤进行操作。
2. 当在组织中公布领英集成时，让用户访问有关 [Microsoft 应用和服务中的领英信息](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)的常见问题解答。 该文章提供有关领英信息在何处显示、如何连接帐户等内容的信息。

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>用于在 Microsoft 和领英中进行数据访问的用户同意

从领英访问的数据不会永久存储在 Microsoft 服务中。 从 Microsoft 访问的数据不会随领英永久存储（除了联系人）。 Microsoft 联系人在用户删除之前会存储在领英中，如[从领英删除导入的联系人](https://www.linkedin.com/help/linkedin/answer/43377)中所述。

当用户连接其帐户时，来自领英的信息和见解可以在某些 Microsoft 应用（如个人资料卡片）中使用。 用户还可以期望通过来自 Microsoft 的信息改进和丰富领英上的网络体验。
当组织中的用户连接其 LinkedIn 和 Microsoft 工作或学校帐户时，可以做出两种选择：

* 授予权限，以便可从两个帐户访问数据。 这意味着他们可以授予权限，以便其 Microsoft 工作或学校帐户可以从其领英帐户访问数据，并且[其领英帐户可以从其 Microsoft 工作或学校帐户访问数据](https://www.linkedin.com/help/linkedin/answer/84077)。
* 授予权限，以便只有其 Microsoft 工作和学校帐户可访问领英数据。

用户可以随时断开帐户连接并删除数据访问权限，并且[用户可以控制其自己的领英个人资料的查看方式](https://www.linkedin.com/help/linkedin/answer/83)，包括是否可以在 Microsoft 应用中查看其个人资料。

### <a name="linkedin-account-data"></a>领英帐户数据

当连接 Microsoft 和领英帐户时，会允许领英向 Microsoft 提供以下数据：

* 个人资料数据 - 包括领英身份、联系信息以及在[领英个人资料](https://www.linkedin.com/help/linkedin/answer/15493)中与他人共享的信息。
* 兴趣数据 - 包括领英上的兴趣，如关注的主题、课程组以及点赞和共享的内容。
* 订阅数据 - 包括领英应用程序和服务订阅以及关联数据。 
* 连接数据 - 包括[领英网络](https://www.linkedin.com/help/linkedin/answer/110)，包括第 1 级连接的个人资料和联系信息。

从领英访问的数据不会永久存储在 Microsoft 服务中。 有关 Microsoft 对个人数据的使用的详细信息，请参阅 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement/)。

### <a name="microsoft-work-or-school-account-data"></a>Microsoft 工作或学校帐户数据

当连接 Microsoft 和领英帐户时，会允许 Microsoft 向领英提供以下数据：

* 个人资料数据 - 包括诸如名字、姓氏、个人资料照片、电子邮件地址、经理以及你管理的人员的信息。
* 日历数据 - 包括日历中的会议、其时间、位置和与会者联系信息。 日历数据中不包括有关议程、内容或会议标题等会议相关信息。
* 兴趣数据 - 包括与帐户关联的兴趣，基于对 Microsoft 服务（如 Cortana 和 Bing for Business）的使用。
* 订阅数据 - 包括组织提供的 Microsoft 应用和服务（如 Office 365）订阅。
* 联系人数据 - 包含 Outlook、 Skype 和其他 Microsoft 帐户服务中的联系人列表，包括频繁通信或协作的人员的联系信息。 联系人会由领英定期导入、存储和使用，例如用于提供连接建议、帮助组织联系人以及显示有关联系人的更新。

从 Microsoft 访问的数据不会随领英永久存储（除了联系人）。 Microsoft 联系人在用户删除之前会存储在领英中。 详细了解如何[从领英删除导入的联系人](https://www.linkedin.com/help/linkedin/answer/43377)。

有关领英对个人数据的使用的详细信息，请参阅[领英隐私策略](https://www.linkedin.com/legal/privacy-policy)。 对于领英服务、数据传输和存储，数据可以从欧盟流向美国并流回，你的隐私按[欧盟数据传输](https://www.linkedin.com/help/linkedin/answer/62533)中所述受到保护。

## <a name="next-steps"></a>后续步骤

* [使用工作或学校帐户的 Microsoft 应用程序中的领英](https://www.linkedin.com/help/linkedin/answer/84077)
