---
title: 解决条件访问登录问题的疑难解答 - Azure 活动目录
description: 本文介绍了条件访问策略导致意外结果时应该怎么做
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337436"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>解决条件访问登录问题的疑难解答

本文中的信息可用于使用错误消息和 Azure AD 登录日志解决与条件访问相关的意外登录结果。

## <a name="conditional-access-sign-in-interrupt"></a>条件访问登录中断

第一种方法是查看显示的错误消息。 对于使用 Web 浏览器时登录的问题，错误页本身包含详细信息。 仅此信息可以描述问题所在，并可能建议解决问题。

![需要登录错误 - 符合设备的要求](./media/troubleshoot-conditional-access/image1.png)

在上述错误中，消息指出，应用程序只能从符合公司的移动设备管理策略的设备或客户端应用程序访问。 在这种情况下，应用程序和设备不符合该策略。

## <a name="azure-ad-sign-in-events"></a>Azure AD 登录事件

获取有关登录中断的详细信息的第二种方法是查看 Azure AD 登录事件，以查看应用了哪些条件访问策略或策略以及原因。

单击初始错误页中的 **"更多详细信息**"，可以找到有关此问题的详细信息。 单击 **"更多详细信息**"将显示在搜索 Azure AD 登录事件时有关用户看到的特定故障事件或打开 Microsoft 支持事件时有用的故障排除信息。

![来自条件访问的更多详细信息中断了 Web 浏览器登录。](./media/troubleshoot-conditional-access/image2.png)

了解应用了哪些条件访问策略或策略，以及为什么执行以下操作。

1. 以全局管理员、安全管理员或全局读取器身份登录到**Azure 门户**。
1. 浏览到**Azure 活动目录** > **登录**。
1. 查找要查看的登录事件。 添加或删除筛选器和列以筛选出不必要的信息。
   1. 添加筛选器以缩小范围：
      1. 当您要调查特定事件时 **，关联 ID。**
      1. **条件访问**以查看策略失败和成功。 将筛选器的范围范围范围，以仅显示限制结果的失败。
      1. **用户名**以查看与特定用户相关的信息。
      1. **日期**范围为相关时间范围。

   ![在登录日志中选择条件访问筛选器](./media/troubleshoot-conditional-access/image3.png)

1. 找到与用户登录失败对应的登录事件后，请选择 **"条件访问**"选项卡。"条件访问"选项卡将显示导致登录中断的特定策略或策略。
   1. **"故障排除和支持**"选项卡中的信息可能清楚地说明登录失败的原因，例如不符合合规性要求的设备。
   1. 要进一步调查，请点击**策略名称**，深入了解策略的配置。 单击 **"策略名称"** 将显示所选策略的"策略配置"用户界面，以便进行审阅和编辑。
   1. 用于条件访问策略评估的**客户端用户****和设备详细信息**也可在登录事件的 **"基本信息**、**位置**、**设备信息**、**身份验证详细信息**"和 **"其他详细信息**"选项卡中提供。

   ![登录事件条件访问选项卡](./media/troubleshoot-conditional-access/image5.png)

如果事件中的信息不足以了解登录结果或调整策略以获得预期结果，则可能会打开支持事件。 导航到该登录事件的**疑难解答和支持**选项卡，然后选择 **"创建新的支持请求**"。

![登录事件的故障排除和支持选项卡](./media/troubleshoot-conditional-access/image6.png)

提交事件时，请在事件提交详细信息中提供请求 ID 以及登录事件的时间和日期。 此信息将允许 Microsoft 支持找到您关注的事件。

### <a name="conditional-access-error-codes"></a>条件访问错误代码

| 登录错误代码 | 错误字符串 |
| --- | --- |
| 53000 | 设备不符合要求 |
| 53001 | 设备未进入 |
| 53002 | 应用程序使用isnotan批准应用程序 |
| 53003 | 阻止条件访问 |
| 53004 | 校对 |

## <a name="next-steps"></a>后续步骤

- [Azure Active Directory 门户中的“登录活动”报告](../reports-monitoring/concept-sign-ins.md)
- [使用 What If 工具排查条件访问问题](troubleshoot-conditional-access-what-if.md)
- [Azure 活动目录中的条件访问](best-practices.md)最佳做法
