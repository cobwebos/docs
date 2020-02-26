---
title: 将 Azure AD 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure Active Directory 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: be9241a6156621d3f90dbab2da5bebeb463b4232
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588614"
---
# <a name="connect-data-from-azure-active-directory"></a>连接 Azure Active Directory 的数据



Azure Sentinel 使你能够从[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)收集数据并将其流式传输到 Azure Sentinel。 你可以选择对[登录日志](../active-directory/reports-monitoring/concept-sign-ins.md)和[审核日志](../active-directory/reports-monitoring/concept-audit-logs.md)进行流式处理。

## <a name="prerequisites"></a>必备条件

- 如果要从 Active Directory 导出登录数据，则必须具有 Azure AD P1 或 P2 许可证。

- 对要从中流式传输日志的租户拥有全局管理员或安全管理员权限的用户。

- 若要查看连接状态，你必须有权访问 Azure AD 诊断日志。 


## <a name="connect-to-azure-ad"></a>连接到 Azure AD

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure Active Directory** " 磁贴。

1. 在要流式传输到 Azure Sentinel 的日志旁边，单击 "**连接**"。

1. 你可以选择是否希望 Azure AD 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”，以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。

1. 若要在 Azure AD 警报 Log Analytics 中使用相关架构，请搜索**SigninLogs**和**AuditLogs**。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure AD 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
