---
title: 将 Azure Active Directory 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何从 Azure Active Directory 收集数据，并将 Azure AD 登录日志和审核日志流式传输到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 279f54c3de964580cc37d1288a6e1b7726348e10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208620"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>将数据从 Azure Active Directory (Azure AD) 



可以使用 Azure Sentinel 的内置连接器从 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 收集数据，并将其流式传输到 Azure Sentinel。 连接器允许流式传输 [登录日志](../active-directory/reports-monitoring/concept-sign-ins.md) 和 [审核日志](../active-directory/reports-monitoring/concept-audit-logs.md)。

## <a name="prerequisites"></a>必备条件


- 任何 Azure AD 许可证 (免费/O365/P1/P2) 足以将登录日志引入 Azure Sentinel。 对于 Azure Monitor (Log Analytics) 和 Azure Sentinel，可能会收取额外的每 gb 费用。

- 必须在工作区中为你的用户分配 Azure Sentinel 参与者角色。

- 你的用户必须在你要从中流式传输日志的租户上向全局管理员或安全管理员角色分配。

- 用户必须对 Azure AD 诊断设置具有读取和写入权限，才能查看连接状态。 


## <a name="connect-to-azure-active-directory"></a>连接到 Azure Active Directory

1. 在 Azure Sentinel 中，从导航菜单中选择 " **数据连接器** "。

1. 从 "数据连接器" 库中，选择 " **Azure Active Directory** "，然后选择 " **打开连接器" 页面**。

1. 将想要流式传输到 Azure 标记的日志旁边的复选框标记为，并单击 " **连接**"。

1. 你可以选择是否希望 Azure AD 中的警报在 Azure Sentinel 中自动生成事件。 在 " **创建事件** " 下，选择 " **启用** " 以启用从连接的安全服务中生成的警报自动创建事件的默认分析规则。 然后，可以在“分析”下的“活动规则”中编辑此规则。 

1. 若要使用 Log Analytics 中的相关架构查询 Azure AD 警报，请 `SigninLogs` `AuditLogs` 在查询窗口中键入或。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure Active Directory 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
