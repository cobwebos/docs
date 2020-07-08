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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 37106517c47c86f4a4a562eebd6d120e31e22334
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564526"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>连接 Azure Active Directory （Azure AD）中的数据



Azure Sentinel 使你能够从[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)收集数据并将其流式传输到 Azure Sentinel。 你可以选择对[登录日志](../active-directory/reports-monitoring/concept-sign-ins.md)和[审核日志](../active-directory/reports-monitoring/concept-audit-logs.md)进行流式处理。

## <a name="prerequisites"></a>先决条件

- 如果要从 Azure AD 导出登录数据，则必须具有 Azure AD P1 或 P2 许可证。

- 对要从中流式传输日志的租户拥有全局管理员或安全管理员权限的用户。

- 若要查看连接状态，你必须有权访问 Azure AD 诊断日志。 


## <a name="connect-to-azure-active-directory"></a>连接到 Azure Active Directory

1. 在 Azure Sentinel 中，从导航菜单中选择 "**数据连接器**"。

1. 从 "数据连接器" 库中，选择 " **Azure Active Directory** "，然后单击 "**打开连接器页**" 按钮。

1. 将想要流式传输到 Azure 标记的日志旁边的复选框标记为，并单击 "**连接**"。

1. 你可以选择是否希望 Azure AD 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”， 以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。 

1. 若要使用 Log Analytics 中的相关架构查询 Azure AD 警报，请 `SigninLogs` `AuditLogs` 在查询窗口中键入或。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure Active Directory 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
