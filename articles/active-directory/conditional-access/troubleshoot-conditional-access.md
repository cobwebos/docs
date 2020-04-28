---
title: 使用条件性访问对登录问题进行故障排除-Azure Active Directory
description: 本文介绍当条件性访问策略导致意外结果时要执行的操作
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 03/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf1daca79f3ed20d9b7a89af20d74ff5f3148b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337436"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>使用条件性访问对登录问题进行故障排除

本文中的信息可用于排查使用错误消息和 Azure AD 登录日志的条件性访问相关的意外登录结果。

## <a name="conditional-access-sign-in-interrupt"></a>条件访问登录中断

第一种方法是查看显示的错误消息。 对于使用 web 浏览器登录时遇到的问题，错误页面本身包含详细信息。 此信息仅可用于描述问题，并可能会建议解决方案。

![需要登录错误兼容设备](./media/troubleshoot-conditional-access/image1.png)

在上述错误中，消息指出只能从满足公司的移动设备管理策略的设备或客户端应用程序访问应用程序。 在这种情况下，应用程序和设备不符合该策略。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登录事件

获取有关登录中断的详细信息的第二种方法是查看 Azure AD 登录事件，以查看应用了哪些条件访问策略或策略以及原因。

在初始错误页中单击 "**更多详细**信息"，可以找到有关该问题的详细信息。 单击 "**更多详细信息**" 将显示在搜索用户看到的特定失败事件或在使用 Microsoft 提出支持事件时的 Azure AD 登录事件时有帮助的故障排除信息。

![从条件访问中断了 web 浏览器登录的更多详细信息。](./media/troubleshoot-conditional-access/image2.png)

若要找出已应用的条件性访问策略，请执行以下操作。

1. 以全局管理员、安全管理员或全局读者身份登录到**Azure 门户**。
1. 浏览到**Azure Active Directory** > **登录**。
1. 查找要查看的登录事件。 添加或删除筛选器和列，以筛选掉不必要的信息。
   1. 添加筛选器以缩小范围：
      1. 要调查的特定事件时的**相关 ID** 。
      1. 用于查看策略失败和成功的**条件性访问**。 确定筛选器的范围，以便仅显示限制结果的失败。
      1. 用于查看与特定用户相关的信息的**用户名**。
      1. 范围为所涉及的时间范围的**日期**。

   ![选择登录日志中的条件访问筛选器](./media/troubleshoot-conditional-access/image3.png)

1. 如果找到对应于用户登录失败的登录事件，请选择 "**条件访问**" 选项卡。"条件性访问" 选项卡将显示导致登录中断的特定策略。
   1. "**故障排除" 和 "支持**" 选项卡中的信息可能会清晰地说明登录失败的原因，例如无法满足合规性要求的设备。
   1. 若要进一步调查，请通过单击**策略名称**向下钻取策略的配置。 单击**策略名称**将显示所选策略的 "策略配置" 用户界面，以便查看和编辑。
   1. "**基本信息**"、"**位置**"、"**设备信息**"、"**身份验证详细信息**" 和 "登录" 事件的 "**其他详细信息**" 选项卡中还提供了用于条件性访问策略评估的**客户端用户**和**设备详细信息**。

   ![登录事件 "条件性访问" 选项卡](./media/troubleshoot-conditional-access/image5.png)

如果事件中的信息不足以了解登录结果或调整策略以获得所需的结果，则可能会打开支持事件。 导航到该登录事件的 "**故障排除和支持**" 选项卡，然后选择 "**创建新的支持请求**"。

![登录事件的 "故障排除和支持" 选项卡](./media/troubleshoot-conditional-access/image6.png)

提交事件时，请在事件提交详细信息的登录事件中提供请求 ID 和时间和日期。 此信息将允许 Microsoft 支持人员查找你所关注的事件。

### <a name="conditional-access-error-codes"></a>条件访问错误代码

| 登录错误代码 | 错误字符串 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 门户中的“登录活动”报告](../reports-monitoring/concept-sign-ins.md)
- [使用 What If 工具排查条件访问问题](troubleshoot-conditional-access-what-if.md)
- [Azure Active Directory 中的条件性访问](best-practices.md)的最佳做法
