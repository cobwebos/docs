---
title: 配置安全性以访问和管理 Azure 时序见解预览版 | Microsoft Docs
description: 本文介绍如何将安全性和权限配置为管理访问策略和数据访问策略以保护 Azure 时序见解预览版。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
ms.custom: seodec18
ms.openlocfilehash: 9aea7a9c9dd96bf30ebb3def9354df9e4bd30114
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558509"
---
# <a name="grant-data-access-to-an-environment"></a>授予对环境的数据访问权限

本文讨论了两种类型的 Azure 时序见解预览版访问策略。

## <a name="grant-data-access"></a>授予数据访问权限

遵循以下步骤向用户主体授予数据访问权限。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 查找时序见解环境。 在“搜索”框中输入 `Time Series`。 在搜索结果中选择“时序环境”。
1. 从列表中选择时序见解环境。
1. 选择“数据访问策略”，然后选择“+ 添加”。

    ![Data-access-one][1]

1. 选择“选择用户”。 搜索用户名称或电子邮件地址，查找要添加的用户。 单击“选择”确认选择。

    ![Data-access-two][2]

1. 选择“选择角色”。 为用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询。

   选择“确定”，确认角色选择。

    ![Data-access-three][3]

1. 在“选择用户角色”页中，选择“确定”。

    ![Data-access-four][4]

1. 确认“数据访问策略”页列出了用户和每个用户的角色。

    ![Data-access-five][5]

## <a name="provide-guest-access-to-a-user-from-another-azure-active-directory-tenant"></a>向另一个 Azure Active Directory 租户的用户提供来宾访问权限

`Guest` 不是管理角色。 它是一个术语，是指从一个租户被邀请到另一个租户的帐户。 在将来宾帐户邀请到租户目录后，它就可以像任何其他帐户一样拥有相同的访问控制。 可以使用“访问控制(IAM)”边栏选项卡授予对时序见解环境的管理访问权限。 也可以通过“数据访问策略”边栏选项卡授予对环境中数据的访问权限。 若要详细了解 Azure Active Directory (Azure AD) 租户来宾访问权限，请阅读[在 Azure 门户中添加 Azure Active Directory B2B 协作用户](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)。

请按照以下步骤将来宾对时序见解环境的访问权限授予另一个租户的 Azure AD 用户。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 查找时序见解环境。 在“搜索”框中，输入“时序见解”。 在搜索结果中选择“时序环境”。
1. 从列表中选择时序见解环境。
1. 选择“数据访问策略”，然后选择“+ 邀请”。

    ![Data-access-six][6]

1. 输入要邀请的用户的电子邮件地址。 此电子邮件地址必须与 Azure AD 关联。 你可以根据情况在邀请中包括个人消息。

    ![Data-access-seven][7]

1. 查找屏幕上显示的确认气泡。

    ![Data-access-eight][8]

1. 选择“选择用户”。 搜索已邀请的来宾用户的电子邮件地址，找到要添加的用户。 单击“选择”确认选择。

    ![Data-access-nine][9]

1. 选择“选择角色”。 为来宾用户选择相应的访问角色：

    * 若要允许用户更改引用数据，以及与环境的其他用户共享保存的查询和透视，请选择“参与者”。

    * 否则请选择“读者”，允许用户查询环境中的数据，以及在环境中保存个人的非共享查询。

   选择“确定”，确认角色选择。

    ![Data-access-ten][10]

1. 在“选择用户角色”页中，选择“确定”。

1. 确认“数据访问策略”页列出了来宾用户和每个来宾用户的角色。

    ![Data-access-eleven][11]

1. 现在，来宾用户必须按步骤访问他们被邀请到的位于 Azure 租户中的环境。 首先，他们需要接受你发送给他们的邀请。 这份邀请通过电子邮件发送到你在步骤 5 中使用的电子邮件地址。 他们选择“开始”即可接受邀请。

    ![Data-access-twelve][12]

1. 接下来，来宾用户接受与管理员组织关联的权限。

    ![Data-access-thirteen][13]

1. 当来宾用户登录到你用来邀请他们的电子邮件地址后，他们就可以接受邀请，然后转到 insights.azure.com。 在那里，他们可以选择屏幕右上角的电子邮件地址旁的头像。

    ![Data-access-fourteen][14]

1. 接下来，来宾用户从目录下拉菜单中选择你的 Azure 租户。 此租户是你邀请他们访问的租户。

    ![Data-access-fifteen][15]

当来宾用户选择你的租户后，他们会看到你为他们提供访问权限的时序见解环境。 现在，他们拥有了与你在步骤 8 中提供的角色相关联的所有功能。

## <a name="next-steps"></a>后续步骤

* 了解[如何将 Azure 事件中心事件源添加到](./time-series-insights-how-to-add-an-event-source-eventhub.md)时序见解环境。
* 发送[事件到事件源](./time-series-insights-send-events.md)。
* [在时序见解预览版资源管理器中查看环境](./time-series-insights-update-explorer.md)。

<!-- Images -->
[1]: media/data-access/data-access-one.png
[2]: media/data-access/data-access-two.png
[3]: media/data-access/data-access-three.png
[4]: media/data-access/data-access-four.png
[5]: media/data-access/data-access-five.png
[6]: media/data-access/data-access-six.png
[7]: media/data-access/data-access-seven.png
[8]: media/data-access/data-access-eight.png
[9]: media/data-access/data-access-nine.png
[10]: media/data-access/data-access-ten.png
[11]: media/data-access/data-access-eleven.png
[12]: media/data-access/data-access-twelve.png
[13]: media/data-access/data-access-thirteen.png
[14]: media/data-access/data-access-fourteen.png
[15]: media/data-access/data-access-fifteen.png