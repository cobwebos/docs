---
title: 排查使用条件访问时的登录问题 - Azure Active Directory
description: 本文介绍如何处理条件访问策略导致意外结果的问题
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9a2ab4b1ebc1c958be9dc4bd07a010f7fef8afc
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610494"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>排查使用条件访问时的登录问题

可以参考本文中的信息，使用错误消息和 Azure AD 登录日志来排查与条件访问相关的意外登录结果。

## <a name="conditional-access-sign-in-interrupt"></a>条件访问登录中断

第一种方法是查看显示的错误消息。 对于使用 Web 浏览器登录时出现的问题，错误页面本身会包含详细信息。 此信息本身可能会描述具体的问题，并可能会建议解决方法。

![登录错误 - 需要合规的设备](./media/troubleshoot-conditional-access/image1.png)

出现上述错误时，消息会指出只能从符合公司移动设备管理策略的设备或客户端应用程序访问该应用程序。 但在此场合下，应用程序和设备并不符合该策略。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登录事件

获取有关登录中断的详细信息的第二种方法是查看 Azure AD 登录事件，以确定应用了哪个（或哪些）条件访问策略，以及为何应用这个（这些）策略。

在初始错误页面中单击“更多详细信息”可以找到有关问题的详细信息。  单击“更多详细信息”会显示故障排除信息，在 Azure AD 登录事件中搜索用户看到的特定失败事件时，或者在向 Microsoft 提出支持事件时，这些信息非常有用。 

![有关条件访问策略中断的 Web 浏览器登录的更多详细信息。](./media/troubleshoot-conditional-access/image2.png)

若要查明已应用哪个（或哪些）条件访问策略，以及为何应用这个（这些）策略，请执行以下操作。

1. 以全局管理员、安全管理员或全局读取者的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory”   > “登录”  。
1. 找到要查看的登录事件。 添加或删除筛选器和列，以筛选掉不必要的信息。
   1. 添加筛选器以缩小范围：
      1. 想要调查的特定事件时，请添加“关联 ID”。 
      1. 要查看策略失败和成功事件，请添加“条件访问”。  限定筛选器的范围，以便仅显示失败事件来限制结果。
      1. 要查看与特定用户相关的信息，请添加“用户名”。 
      1. 添加已限定到相关时间范围的“日期”。 

   ![在登录日志中选择“条件访问”筛选器](./media/troubleshoot-conditional-access/image3.png)

1. 找到与用户登录失败对应的登录事件后，选择“条件访问”选项卡。  “条件访问”选项卡将显示导致登录中断的特定策略。
   1. “故障排除和支持”选项卡中的信息可能会明确地解释登录失败的原因，例如，设备不满足合规性要求。 
   1. 若要进一步调查，请单击“策略名称”向下钻取到策略的配置。  单击“策略名称”会显示所选策略的策略配置用户界面，供你进行查看和编辑。 
   1. 在登录事件的“基本信息”、“位置”、“设备信息”、“身份验证详细信息”和“其他详细信息”选项卡中，也提供了用于评估条件访问策略的客户端用户和设备详细信息。       
   1. 选择策略右侧的省略号将显示策略详细信息。 这为管理员提供了有关策略成功应用的其他信息。

   ![登录事件的“条件访问”选项卡](./media/troubleshoot-conditional-access/image5.png)

   ![策略详细信息（预览版）](./media/troubleshoot-conditional-access/policy-details.png)

如果事件中的信息不足以让你了解登录结果或调整策略来获取所需结果，可以提出支持事件。 导航到该登录事件的“故障排除和支持”选项卡，然后选择“创建新的支持请求”。  

![登录事件的“故障排除和支持”选项卡](./media/troubleshoot-conditional-access/image6.png)

提交事件时，请在事件提交详细信息中提供请求 ID，以及登录事件中的时间和日期。 Microsoft 支持人员将通过此信息找到你所关注的事件。

### <a name="conditional-access-error-codes"></a>条件访问错误代码

| 登录错误代码 | 错误字符串 |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 门户中的“登录活动”报表](../reports-monitoring/concept-sign-ins.md)
- [使用 What If 工具排查条件访问问题](troubleshoot-conditional-access-what-if.md)
- [Azure Active Directory 中的条件访问](best-practices.md)的最佳做法
